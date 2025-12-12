---
description: Configura Claude GitHub Action no repositório atual
allowed-tools: Bash(mkdir:*), Bash(git add:*), Bash(git commit:*), Bash(git push:*), Bash(git remote:*), Bash(gh secret set:*), Bash(curl:*), Read, Write
---

## Contexto

- Remote atual: !`git remote get-url origin 2>/dev/null || echo "SEM_REMOTE"`
- Status do git: !`git status --short 2>/dev/null | head -5`
- Workflow existe: !`test -f .github/workflows/claude.yml && echo "SIM" || echo "NAO"`

## Tarefa

Configure o Claude GitHub Action neste repositório:

1. **Verificar pré-requisitos:**
   - Confirme que é um repositório git com remote configurado
   - Identifique owner/repo do remote (ex: hmaurus/meu-repo ou mh-tec/meu-repo)

2. **Criar workflow** (se não existir):
   - Crie `.github/workflows/claude.yml` com o conteúdo padrão:
   ```yaml
   name: Claude Code
   on:
     issue_comment:
       types: [created]
     pull_request_review_comment:
       types: [created]
     pull_request_review:
       types: [submitted]
     issues:
       types: [opened, assigned]

   jobs:
     claude:
       if: |
         (github.event_name == 'issue_comment' && contains(github.event.comment.body, '@claude')) ||
         (github.event_name == 'pull_request_review_comment' && contains(github.event.comment.body, '@claude')) ||
         (github.event_name == 'pull_request_review' && contains(github.event.review.body, '@claude')) ||
         (github.event_name == 'issues' && contains(github.event.issue.body, '@claude'))
       runs-on: ubuntu-latest
       permissions:
         contents: write
         pull-requests: write
         issues: write
         id-token: write
       steps:
         - name: Run Claude Code
           uses: anthropics/claude-code-action@v1
           with:
             anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
   ```

3. **Commit e push** do workflow:
   - `git add .github/workflows/claude.yml`
   - `git commit -m "chore: add claude github action"`
   - `git push`

4. **Configurar secret** (se repo for privado ou owner for hmaurus):
   - Pergunte ao usuário a ANTHROPIC_API_KEY se necessário
   - Execute: `gh secret set ANTHROPIC_API_KEY --repo OWNER/REPO`
   - Para repos públicos em mh-tec, informe que o secret da org já cobre

5. **Resumo final:**
   - Confirme o que foi configurado
   - Instrua como testar: criar Issue com "@claude olá"
