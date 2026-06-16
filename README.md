# Internship Log — UltraSistech

Registro das tasks realizadas durante o estágio na UltraSistech.
Cada entrada documenta o problema, o que foi feito e o que aprendi.

---

## DEV-10 — Pesquisa UltraPonto no Google redirecionava para site da Ebserh

**Problema:** Pesquisar "UltraPonto" no Google retornava o site da Ebserh nos primeiros resultados, causando confusão nos clientes.

**O que foi feito:**
- Adicionada meta tag `noindex` no `_app.tsx`, controlada pela variável `NEXT_PUBLIC_ALLOW_INDEXING` — permite que o deployment principal (`ultraponto.ultrasistech.com.br`) continue aparecendo no Google enquanto os outros deployments ficam invisíveis para os buscadores
- Criado `robots.txt` na pasta `public/` bloqueando o rastreamento por padrão em todos os deployments
- Adicionada a variável `NEXT_PUBLIC_ALLOW_INDEXING=true` no `.env` de produção do domínio principal

**O que aprendi:**
- O que é indexação no Google e como os buscadores rastreiam páginas
- Como a meta tag `noindex` instrui o Google a ignorar uma página
- Como o `robots.txt` funciona como porteiro do site para os robôs de busca
- Como usar variáveis de ambiente para comportamentos diferentes por deployment

---

## DEV-17 — Corrigir busca por nome no UltraPonto

**Problema:** A busca por nome no sistema às vezes não funcionava como esperado — alguns nomes não eram encontrados mesmo existindo no banco.

**O que foi feito:**
- Investigada a query de busca no backend e identificado que a comparação era case-sensitive (diferenciava maiúsculas de minúsculas) e não tratava acentuação
- Corrigida a query no Prisma para usar `mode: 'insensitive'` na busca, permitindo encontrar "joão" ao digitar "Joao" ou "JOÃO"
- Ajustado o filtro para usar `contains` em vez de `equals`, permitindo busca parcial pelo nome

**O que aprendi:**
- A diferença entre busca `equals` (exata) e `contains` (parcial) no Prisma
- O que é `mode: 'insensitive'` e por que buscas case-sensitive causam bugs silenciosos
- Como queries de banco de dados se comportam diferente com acentuação e capitalização

---

## DEV-19 — Layout do cartão de ponto personalizado e preferências do usuário

**Problema:** O cartão de ponto não tinha layout personalizado e as preferências de exibição do usuário não eram salvas entre sessões.

**O que foi feito:**
- Criados componentes de UI para o layout personalizado do cartão de ponto
- Implementados endpoints no backend para salvar e recuperar as preferências do usuário
- As preferências ficam persistidas no banco vinculadas ao perfil do usuário, sendo carregadas automaticamente no próximo acesso
- Integrado o frontend com os novos endpoints via hook customizado

**O que aprendi:**
- Como persistir preferências de interface do usuário no banco de dados
- O padrão de hook customizado (`usePreferences`) para encapsular lógica de API no React
- Como carregar configurações salvas ao montar um componente com `useEffect`

---

## DEV-22 — Preferências do usuário sendo removidas ao atualizar cadastro

**Problema:** A opção de marcação remota era desmarcada automaticamente quando o usuário atualizava a senha, foto biométrica ou qualquer outro dado pelo aplicativo.

**O que foi feito:**

**Mobile:** Adicionado o `employeeProfile` ao objeto `newUser` usando o operador spread (`...`). O código faz uma cópia dos dados atuais do perfil e depois aplica apenas as alterações vindas da requisição, sobrescrevendo somente os campos modificados e preservando o restante.

**Backend:** Corrigida a conversão de boolean — antes o sistema usava `Boolean()` para qualquer valor, convertendo strings como `"false"` em `true`. Agora verifica explicitamente se o valor recebido é `"true"`. Se o campo não for enviado, mantém `undefined` evitando atualizações indevidas.

**O que aprendi:**
- O que é o operador spread (`...`) e como ele copia propriedades de um objeto para outro
- Por que `Boolean("false")` retorna `true` em JavaScript (qualquer string não vazia é truthy)
- A diferença entre `undefined` e `false` em atualizações parciais de banco de dados
- Como um bug de conversão de tipo pode silenciosamente apagar dados do usuário

---

## DEV-29 — Tela de supervisão de ponto não exibia registros corretamente

**Problema:** A tela de supervisão de ponto não estava exibindo os registros. Os dados chegavam da API mas não subiam para a interface.

**O que foi feito:**
- Investigada a integração entre o frontend e a API de registros
- Identificado que o estado do React não estava sendo atualizado corretamente após a resposta da API — os dados chegavam mas o componente não re-renderizava
- Corrigido o mapeamento da resposta da API para o formato esperado pelo componente
- Ajustadas as dependências do `useEffect` para garantir que os dados fossem buscados nos momentos certos

**O que aprendi:**
- Como depurar problemas de integração frontend-API usando o DevTools do navegador (aba Network)
- Por que um `useEffect` com dependências erradas pode fazer os dados ficarem "presos" sem atualizar a tela
- Como mapear a resposta de uma API para o formato que o componente React espera

---

## DEV-30 — Campo de assinatura manual no cartão de ponto

**Problema:** A task pedia para incluir um campo de assinatura manual no layout do cartão de ponto, complementando a assinatura digital.

**O que foi feito:**
- Investigado o código do cartão de ponto e verificado que a assinatura manual/física já estava implementada
- Documentado o funcionamento atual e confirmado para o time que a feature já existia
- Validado que o campo aparecia corretamente no layout impresso do cartão

**O que aprendi:**
- A importância de investigar o código existente antes de implementar algo novo — evita retrabalho
- Como ler e navegar em código legado para entender o que já está implementado
- O valor de comunicar findings ao time antes de começar a escrever código

---

## DEV-31 — Ocultar colunas de banco de horas e adicional noturno no relatório

**Problema:** Empresas que não utilizam banco de horas viam colunas desnecessárias no relatório, poluindo a visualização.

**O que foi feito:**
- Adicionada lógica condicional no componente de relatório para ocultar as colunas de banco de horas e adicional noturno com base nas configurações da empresa
- A visibilidade das colunas é controlada por uma flag nas configurações da empresa, carregada via API
- Quando a flag está desativada, as colunas não aparecem nem no relatório em tela nem na versão impressa/exportada

**O que aprendi:**
- Como renderização condicional funciona em React (`condition && <Component />`)
- Como configurações por empresa (multi-tenant) controlam comportamentos diferentes na interface
- A importância de adaptar a UI à realidade de cada cliente em vez de exibir tudo para todos

---

## DEV-32 — Ocultar registros excluídos no rodapé dos relatórios

**Problema:** O rodapé dos relatórios exibia informações de registros excluídos, expondo dados desnecessários para o colaborador.

**O que foi feito:**
- Adicionada opção de ocultar no rodapé as informações referentes a registros excluídos
- Implementado toggle na interface para o gestor controlar a visibilidade dessas informações
- Quando ativado, os registros excluídos são filtrados antes de renderizar o rodapé

**O que aprendi:**
- Como filtrar arrays em JavaScript/TypeScript antes de renderizar (`.filter()`)
- A diferença entre excluir um registro do banco e apenas ocultá-lo da exibição (soft delete vs hide)
- Como expor configurações de exibição para o usuário sem alterar os dados originais

---

## DEV-34 — Cadastro com CPF já registrado bloqueava duplo vínculo

**Problema:** O sistema rejeitava o cadastro de um colaborador cujo CPF já existia no banco, impedindo que a mesma pessoa fosse vinculada a múltiplas empresas simultaneamente.

**O que foi feito:**
- Identificado que havia uma validação de unicidade de CPF no backend que não considerava o contexto de empresa (tenant)
- Corrigida a validação para checar se o CPF já existe **dentro da mesma empresa**, e não no banco inteiro
- Ajustado o script de importação de colaboradores para seguir a mesma lógica
- O mesmo CPF agora pode ser vinculado a empresas diferentes sem conflito

**O que aprendi:**
- O conceito de multi-tenancy: dados que pertencem a empresas diferentes não devem colidir entre si
- Como uma validação de unicidade muito ampla pode bloquear casos de uso legítimos
- A diferença entre unicidade global (`unique` no banco) e unicidade por escopo (`unique` por empresa)

---

## DEV-35 — Opção de marcação remota desaparecia para colaboradores do varejo

**Problema:** A opção de marcação remota de ponto sumia da interface para colaboradores do varejo após determinadas ações no app.

**O que foi feito:**
- Investigada a causa raiz e identificado que o problema estava diretamente ligado ao bug corrigido no DEV-22
- A atualização de cadastro sobrescrevia o `employeeProfile` inteiro sem preservar os campos existentes, apagando a configuração de marcação remota
- A correção do DEV-22 (uso do operador spread para preservar os dados do perfil) resolveu este bug também
- Validado o comportamento com colaboradores do varejo após a correção

**O que aprendi:**
- Como um único bug pode se manifestar de formas diferentes em contextos diferentes (DEV-22 e DEV-35 eram o mesmo problema)
- A importância de investigar a causa raiz antes de corrigir — corrigir o DEV-22 eliminou o DEV-35 automaticamente
- Como relacionar tasks no Jira para evitar trabalho duplicado

---

## DEV-73 — Importar logs perdidos de REP ControliD/IDFace

**Problema:** Dispositivos ControliD IDFace estavam conectados, mas as batidas de ponto não apareciam no MongoDB. A tela de importação manual retornava erros ao tentar buscar os logs.

**O que foi feito:**
1. **Conectado o endpoint ao service:** O endpoint `GET /rep/controlid/:serialNumber/getalllog` existia no controller mas o `IdFaceService` não estava injetado no construtor, impedindo o app de subir.

2. **Implementado o método `triggerLostLogsImport`:** Convertia datas em Unix timestamp, zerava o `lastProcessedLogId`, enfileirava o comando `load_objects` no Redis do dispositivo e salvava o filtro de período para o processor usar.

3. **Corrigidas incompatibilidades do código portado:**
   - `placeCheckinAtTime` chamado com 8 argumentos mas aceita 6 — removidos os extras
   - `checkin._id` acessado direto, mas o método retorna `{ record, isNew }` — corrigido para `checkin.record._id`
   - Removido import de interface inexistente que travava a compilação

4. **Corrigido o número de série na URL:** O serial do ControliD contém `/` quebrando a rota — trocado para usar `deviceId` como query param.

5. **Corrigida a validação de modelo e o `BACKEND_URL`:** A lista de modelos suportados tinha nomes errados; o endereço do servidor estava como `localhost`.

**O que aprendi:**
- Como funciona injeção de dependência no NestJS e por que um service não injetado impede o app de subir
- O que é uma fila Redis e como comandos são enviados para dispositivos físicos
- Por que `/` em um path param quebra o roteamento HTTP e como usar query params no lugar
- Como incompatibilidades de assinatura de função causam erros de compilação TypeScript
- A diferença entre o objeto retornado e o tipo esperado ao acessar propriedades aninhadas

---

## DEV-75 — Listagem de colaboradores limitada a 10 registros

**Problema:** A tela de colaboradores mostrava "Mostrando 1 a 10 de 10 colaborador(es)" mesmo com 37 cadastrados. Era impossível navegar para outras páginas.

**Causa raiz:** O frontend passava `limit=10` para a rota de contagem. O backend aplicava esse limite com `take: 10` no Prisma, retornando 10 ao invés do total real. Com `count = 10`, o cálculo ficava `ceil(10/10) = 1 página`.

**O que foi feito:**
- Removidos `skip` e `take` da query `prisma.user.count()` — contagem sempre retorna o total real
- Removidos `skipCount` e `limitCount` do repositório e do service
- No frontend: substituído `limit` por `undefined` na chamada de `fetchEmployeeCount`
- Corrigidos os arrays de dependências do `useCallback` e `useEffect` de `[]` para as dependências corretas

**O que aprendi:**
- A diferença entre duas queries separadas: buscar registros da página atual vs contar o total
- Por que `useCallback` com `[]` congela os valores das variáveis no momento da criação (closure)
- O que são dependências do `useCallback` e `useEffect` e por que elas importam
- Como um `take` indevido no Prisma pode fazer o banco retornar um número errado silenciosamente

---

## DEV-82 — Identidade Visual UltraCRM

**Problema:** O sistema UltraCRM precisava de uma nova identidade visual com cores, ícones e componentes atualizados.

**O que foi feito:**

| Tela | Antes | Depois |
|------|-------|--------|
| Login | Fundo branco simples | Fundo escuro (emerald-950), branding lateral, inputs com ícones |
| Sidebar | Fundo branco | Fundo escuro (emerald-900), itens em verde/branco |
| Pipeline | Cards sem hover, sem alerta de atraso | Sombra verde no hover, banner "Atendimento atrasado", contador por estágio |
| Dashboard | Badges todos azuis | Badges com cor por estágio, empty state com ícone |
| Clientes | Botão cinza | Botão verde com dropdown |
| Tarefas | Barra de progresso estática | Barra pisca ao chegar em 100% |

- Mapeamento dos estágios do funil (`DealStage`) para cores e ícones via TypeScript `Record`
- Criação de badges visuais com Tailwind CSS com cor por estágio

**O que aprendi:**
- Como tipar objetos com `Record<Key, Value>` em TypeScript
- Uso de template literals no Tailwind para classes condicionais
- Diferença entre Server e Client Components no Next.js (`'use client'`)
- Como criar animações com classes Tailwind (`animate-pulse`)

---

## DEV-89 — Fix Deploy 502 Frontend (ultracrm-webapp)

**Problema:** Após o deploy via GitHub Actions, o Nginx retornava erro 502 Bad Gateway. A porta do container divergia do que o Nginx esperava e o Nginx não era recarregado após o deploy.

**O que foi feito:**
- Criado `Dockerfile` para o frontend Next.js
- Atualizado `docker-compose.deploy.yml` para usar imagem do GHCR com porta fixada em `3005:3000`
- Atualizado workflow GitHub Actions: builda a imagem no CI, publica no GHCR e faz pull na VPS
- Adicionado `sudo systemctl reload nginx` no script de deploy
- Configurada regra `NOPASSWD` no `/etc/sudoers.d/` para o reload do Nginx sem senha

**O que aprendi:**
- O que é um registry de imagens Docker (GHCR) e por que buildar no CI em vez de na VPS
- Como funciona `docker compose pull` + `up -d` para atualizar containers
- O que é reverse proxy e como o Nginx roteia requisições por porta
- Como criar regras no `/etc/sudoers.d/` para comandos sem senha
- Como usar Secrets no GitHub Actions para proteger credenciais

---

## DEV-92 — Fix Deploy Pipeline Backend (api-ultracrm)

**Problema:** O backend precisava seguir o mesmo padrão de deploy via GHCR do frontend. O Prisma 7 exige `DATABASE_URL` mesmo durante o `prisma generate` no build da imagem.

**O que foi feito:**
- Atualizado `Dockerfile` com multi-stage build (builder + runner separados)
- Adicionado `ENV DATABASE_URL=mysql://dummy:...` no estágio de build para satisfazer o Prisma durante o CI
- Atualizado `docker-compose.yml` para usar imagem do GHCR
- Criado workflow GitHub Actions com build, push para GHCR, deploy via SSH e `prisma migrate deploy` automático

**O que aprendi:**
- O que é multi-stage build no Docker e por que separar builder do runner reduz o tamanho da imagem final
- Por que o Prisma 7 precisa da `DATABASE_URL` mesmo em tempo de build (`prisma.config.ts`)
- Como rodar migrations automaticamente no deploy (`prisma migrate deploy`)
- Como limpar containers desnecessários na VPS para liberar memória

---

## Stack dos Projetos

| Camada | Tecnologia |
|--------|-----------|
| Frontend | Next.js, TypeScript, Tailwind CSS |
| Backend | NestJS, TypeScript |
| Mobile | React Native |
| Banco de dados | MySQL + Prisma ORM, MongoDB |
| Infraestrutura | Docker, GitHub Actions, Nginx, VPS |
| Registry | GitHub Container Registry (GHCR) |
| Cache/Filas | Redis |

## DEV-93 — Envio de proposta comercial por e-mail direto no sistema

**Problema:** O botão de enviar proposta por e-mail abria um popup no navegador pedindo para escolher um cliente de e-mail (Outlook, Gmail, etc.), o que causava má experiência e dependia de configuração do computador do usuário.

**O que foi feito:**
- Criado módulo de e-mail no backend (`api-ultracrm`) com a rota `POST /email/send`
- Integrado o AWS SES (Simple Email Service) como serviço de envio — o mesmo já usado pelo UltraPonto
- Criados os arquivos `email.module.ts`, `email.service.ts`, `email.controller.ts` e `send-email.dto.ts` seguindo o padrão NestJS do projeto
- Instalado o pacote `@aws-sdk/client-ses` e configuradas as credenciais AWS via variáveis de ambiente
- Criado `email.service.ts` no frontend (`ultracrm-webapp`) usando `apiFetch` para chamar a API
- Substituído o `window.open(mailto:...)` pela chamada à API de envio direto

**O que aprendi:**
- O que é AWS SES e como funciona o envio de e-mail via API em vez de cliente de e-mail
- Como estruturar um módulo NestJS do zero: module, controller, service e DTO
- O que é um DTO e como os decorators `@IsEmail()`, `@IsString()` validam automaticamente os dados da requisição
- Como variáveis de ambiente (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`) são lidas no NestJS via `ConfigService`
- Como criar um service no frontend seguindo o padrão existente do projeto com `apiFetch`
- A diferença entre `mailto:` (abre cliente de e-mail local) e envio via API (o servidor envia diretamente)

- ## DEV-103 — Migrar MySQL para PostgreSQL + Backup diário no S3

**Problema:** A api-ultracrm rodava com MySQL 8.0. A task pedia migração para PostgreSQL e criação de um script de backup diário automático para o S3.

**O que foi feito:**

**Etapa 1 — Migração MySQL → PostgreSQL:**
- Feito backup de segurança do MySQL antes de qualquer mudança (`mysqldump`)
- Trocada a dependência `@prisma/adapter-mariadb` por `@prisma/adapter-pg` + `pg` no `package.json`
- Atualizado `PrismaService` e `seed.ts` para usar `PrismaPg` no lugar de `PrismaMariaDb`
- Alterado o `schema.prisma` de `provider = "mysql"` para `provider = "postgresql"`
- Deletadas as migrations antigas (geradas para MySQL) e recriadas para PostgreSQL
- Adicionado serviço `postgres:16-alpine` no `docker-compose.yml` do servidor
- Exportados os dados do MySQL via `mysqldump --no-create-info`, convertidas as crases para aspas duplas com `sed`, e importados no PostgreSQL via `psql`
- Verificada a integridade dos dados comparando contagem de linhas entre MySQL e PostgreSQL nas 6 tabelas
- Atualizado `docker-compose.yml`, `.env.example` e `CLAUDE.md` para refletir a troca de banco
- Removida linha `git pull origin master` desnecessária do workflow de CI/CD (o servidor não é um repositório git — o código vai via imagem Docker)

**Etapa 2 — Script de backup diário:**
- Instalado AWS CLI v2 no servidor via instalador oficial (Ubuntu Noble não tem `awscli` no apt)
- Criado `/root/backup_ultracrm.sh`: faz `pg_dump` dentro do container, comprime com `gzip` e envia para `s3://ultracrm/backups/` via `aws s3 cp`
- Testado manualmente — arquivo chegou no S3 com sucesso
- Agendado via `crontab` para rodar todo dia à 1h da manhã, com log em `/var/log/ultracrm_backup.log`

**O que aprendi:**
- A diferença entre MySQL e PostgreSQL: MySQL usa crases para identificadores, PostgreSQL usa aspas duplas — por isso o `sed` foi necessário na migração dos dados
- O que é um driver adapter no Prisma 7 e por que trocar de banco exige trocar o adapter no código
- Como o `pg_dump` exporta um banco PostgreSQL e por que comprimir com `gzip` antes de enviar para o S3
- Como o `crontab` funciona: a sintaxe `0 1 * * *` significa "todo dia às 1h da manhã"
- Por que o servidor não precisa de `git pull` — o código já vai empacotado dentro da imagem Docker publicada no GHCR
- A importância de verificar a integridade dos dados após uma migração (contar linhas em ambos os bancos)
- Como o AWS CLI autentica via variáveis de ambiente (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`)
