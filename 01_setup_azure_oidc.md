# Passo A: Configuração da Federação de Identidade (OIDC) no Azure e Databricks

Este guia detalha como configurar a confiança entre o GitHub Actions e o Azure Databricks. Isso permite que a esteira de CI/CD faça deploy no seu Workspace **sem precisar de senhas ou Personal Access Tokens (PATs)**.

## A.1. Criar o Service Principal no Azure (Microsoft Entra ID)

1. Acesse o [Portal do Azure](https://portal.azure.com/) e busque por **Microsoft Entra ID** (antigo Azure Active Directory).
2. No menu lateral esquerdo, clique em **App registrations** e depois no botão **New registration**.
3. Preencha os dados:
   - **Name**: Ex: `sp-databricks-github-actions` (um nome claro que identifique o propósito).
   - **Supported account types**: Escolha a primeira opção (`Accounts in this organizational directory only`).
4. Clique no botão **Register**.
5. Na tela de "Overview" (Visão Geral) do App que acabou de ser criado, **copie o valor do campo "Application (client) ID"**. 
   - *Você vai precisar desse valor exato no Passo B para as variáveis `DATABRICKS_CLIENT_ID_DEV` e `DATABRICKS_CLIENT_ID_PRD` no GitHub.*

## A.2. Configurar a Confiança OIDC (Federated Credential)

Agora vamos dizer à Azure para confiar nos tokens emitidos pelo seu repositório do GitHub.

1. Ainda na tela do seu App (Service Principal) no Entra ID, clique em **Certificates & secrets** no menu da esquerda.
2. Clique na aba **Federated credentials**.
3. Clique em **Add credential**.
4. No campo **Federated credential scenario**, selecione a opção **GitHub Actions deploying Azure resources**.
5. Preencha os detalhes exatos do seu projeto no GitHub:
   - **Organization**: O nome da sua organização ou usuário no GitHub.
   - **Repository**: O nome exato do repositório onde a esteira vai rodar (o repositório gerado a partir do template).
   - **Entity type**: Selecione **Environment**.
   - **GitHub environment name**: Digite `dev` (Tudo minúsculo, exatamente como colocamos no YAML do template).
6. Dê um nome para essa credencial (Ex: `github-actions-dev-env`) e clique em **Add**.
7. **⚠️ Repita o passo 3 ao 6** para o ambiente de Produção, mudando apenas o campo "GitHub environment name" de `dev` para `prod`.

## A.3. Dar Permissão ao Service Principal no Databricks

O Azure já confia no GitHub, mas o Databricks precisa permitir que esse Service Principal crie arquivos e rode jobs.

1. Acesse o **Workspace do Databricks** (primeiro o ambiente DEV) utilizando um usuário com perfil **Admin**.
2. Clique no seu nome de usuário/email no canto superior direito e vá em **Settings**.
3. Vá na seção **Identity and access** e clique em **Manage** na linha "Service Principals".
4. Clique em **Add service principal** e adicione o Service Principal que você criou no Azure (você pode buscar pelo Application ID dele).
5. Após adicionar, clique no nome dele na lista, vá na aba **Workspace admin** (ou Role Management) e conceda o privilégio de **Workspace admin**.
   - *Nota: Em ambientes mais restritivos, em vez de Admin, você pode conceder permissões granulares nos catálogos do Unity Catalog e nas pastas do Workspace, mas o Service Principal precisa de permissão de "Can Manage" ou equivalente.*
6. **Repita este passo** no Workspace de Produção (caso seja um Workspace separado).

---
✅ **Pronto!** A comunicação GitHub ➔ Azure ➔ Databricks é 100% confiável. Prossiga para o **Passo B (02_setup_github_environments.md)**.
