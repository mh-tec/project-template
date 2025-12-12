---
description: Configura Claude GitHub Action no repositório atual
allowed-tools: Bash(mkdir:*), Bash(git add:*), Bash(git commit:*), Bash(git push:*), Bash(gh secret set:*), Read, Write, Glob
---

Configure o Claude GitHub Action neste repositório seguindo estes passos:

1. **Verificar pré-requisitos:**
   - Use Glob para verificar se `.github/workflows/claude.yml` já existe
   - Se existir, informe e pergunte se quer sobrescrever

2. **Criar workflow:**
   - Crie o diretório `.github/workflows` se não existir
   - Crie `.github/workflows/claude.yml` com este conteúdo:

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

3. **Commit e push:**
   - `git add .github/workflows/claude.yml`
   - `git commit -m "chore: add claude github action"`
   - `git push`

4. **Configurar secret:**
   - Pergunte ao usuário se quer configurar o secret ANTHROPIC_API_KEY agora
   - Se sim, peça a chave e execute: `gh secret set ANTHROPIC_API_KEY`

5. **Resumo:** Confirme o que foi configurado e instrua como testar (criar Issue com "@claude olá")
