# Passo C: Como Proteger as Branches de Trabalho

Para que a nossa esteira funcione de forma govervada, precisamos garantir que ninguém faça "push direto" nas branches principais (`developer` e `main`). Tudo deve passar por Pull Request para que as Actions rodem e validem o código (Gate 1).

## C.1. Configurar Regra para a branch `developer`

1. No seu repositório no GitHub, clique na aba **Settings**.
2. No menu lateral esquerdo, clique em **Branches**.
3. Na seção "Branch protection rules" ou "Rulesets" (dependendo da sua interface, Rulesets é o mais moderno), clique em **Add branch ruleset**.
4. Configure o Ruleset:
   - **Ruleset Name**: `Proteção Developer`
   - **Enforcement status**: `Active`
5. Na seção **Target branches**, clique em `Add target` > `Include by pattern` e digite `developer`.
6. Na seção **Rules**, marque as seguintes opções:
   - ✅ **Require a pull request before merging**: 
     - *Isso garante que código só entra via PR.*
     - Marque a sub-opção "Require approvals" (se quiser obrigar Code Review entre pares).
   - ✅ **Require status checks to pass**:
     - *Isso é o que barra o merge se o código quebrar!*
     - No campo de busca que aparece embaixo, pesquise por `Gate 1 + Deploy DEV` (esse é o nome do nosso job no `ci.yml`). Selecione ele.
7. Clique em **Create**.

## C.2. Configurar Regra para a branch `main`

A branch `main` é a sua branch de produção. A regra aqui é parecida, mas um pouco mais rígida.

1. Na mesma tela, clique novamente em **New ruleset**.
2. Configure:
   - **Ruleset Name**: `Proteção Main`
   - **Enforcement status**: `Active`
3. Em **Target branches**, adicione `main`.
4. Em **Rules**, marque:
   - ✅ **Require a pull request before merging**
   - ✅ **Require status checks to pass**
     - Opcional: Se você tiver outros checks, você pode exigi-los aqui. Em alguns fluxos, as pessoas exigem que a Action "CI" rode de novo. Em outros, apenas que o PR seja aprovado.
5. Clique em **Create**.

---

## 🎉 Resumo do Fluxo do Desenvolvedor

Com tudo isso configurado (Passos A, B e C), o dia a dia do seu Engenheiro de Dados será:

1. Ele clona o repositório.
2. Cria uma branch a partir de `developer` (ex: `feature/nova-tabela`).
3. Desenvolve os códigos SQL/Python localmente.
4. Faz o `git commit` e `git push` para a `feature/nova-tabela`.
5. Abre um Pull Request apontando da `feature` para a `developer`.
   - **Aqui o GitHub vai rodar o CI sozinho!** Vai testar e fazer deploy em DEV.
6. Aprovado o PR, ele clica em "Merge".
7. Depois de validar os dados no Databricks DEV, ele abre um PR de `developer` para `main`.
8. O Líder ou Aprovador revisa. Faz o "Merge".
9. O GitHub Actions de `prod` começa a rodar, **pede aprovação**, e então faz o deploy em Produção!

Sucesso absoluto e automação total com governança! 🚀
