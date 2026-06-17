# Passo B: Como Criar os Environments e Variables no GitHub

Os **Environments** no GitHub Actions funcionam como cofres seguros que armazenam as variáveis específicas de cada ambiente de sua esteira (DEV e PROD) e permitem adicionar regras de aprovação humana.

O nosso template e os arquivos YAML (`ci.yml` e `cd.yml`) já estão programados para procurar os environments `dev` e `prod`.

## B.1. Criar os Ambientes

1. No seu repositório no GitHub (já com o projeto gerado), clique na aba **Settings**.
2. No menu lateral esquerdo, clique em **Environments**.
3. Clique no botão **New environment**.
4. Digite **`dev`** (tudo minúsculo) e clique em **Configure environment**. (Não precisa colocar aprovação para o DEV). Salve.
5. Volte para a tela de Environments e clique novamente em **New environment**.
6. Digite **`prod`** (tudo minúsculo) e clique em **Configure environment**.

### B.2. Configurar a Aprovação Humana em PROD

1. Na tela de configuração do environment **`prod`**, marque a caixa **Required reviewers**.
2. No campo de busca que vai aparecer, procure o seu nome ou o nome dos Tech Leads/Aprovadores responsáveis por aprovar idas para produção.
3. Clique em **Save protection rules**.
   - *O que isso faz: Quando a branch `main` receber um push/merge, a Action vai começar a rodar, mas vai pausar e enviar uma notificação para esses aprovadores. O deploy no Databricks só ocorre após alguém ir lá e clicar em "Approve".*

## B.3. Cadastrar as Variáveis de Ambiente (Environment Variables)

Para que a Action saiba a qual Workspace conectar e qual o Client ID da Azure, precisamos colocar as variáveis.

> **Importante:** Vá na aba "Environment variables" ou, caso esteja no painel principal, vá em **Settings > Secrets and variables > Actions > Aba Variables**. É importante criar como **Variables** e não como Secrets, pois URLs e Client IDs não precisam ser mascarados nos logs e a forma de injeção no OIDC funciona melhor com variáveis.

Se for cadastrar direto na tela do ambiente que você acabou de criar:

### No Ambiente `dev`:
1. Role a página do environment `dev` até a seção **Environment variables** e clique em **Add variable**.
2. Crie a variável do Host:
   - **Name**: `DATABRICKS_HOST_DEV`
   - **Value**: A URL do seu workspace DEV (ex: `https://adb-1234567890.12.azuredatabricks.net`)
   - Clique em **Add variable**.
3. Crie a variável do Client ID:
   - **Name**: `DATABRICKS_CLIENT_ID_DEV`
   - **Value**: O "Application (client) ID" que você copiou no Passo A (Azure).
   - Clique em **Add variable**.

### No Ambiente `prod`:
1. Role a página do environment `prod` até a seção **Environment variables** e clique em **Add variable**.
2. Repita o processo acima, porém com os nomes:
   - **Name**: `DATABRICKS_HOST_PRD`
   - **Value**: A URL do seu workspace PROD.
3. E para o Client ID:
   - **Name**: `DATABRICKS_CLIENT_ID_PRD`
   - **Value**: O "Application (client) ID" do seu Service Principal na Azure.

---
✅ **Pronto!** O GitHub agora sabe com qual Workspace se comunicar e quem deve aprovar a produção. Prossiga para o **Passo C (03_setup_branch_protection.md)**.
