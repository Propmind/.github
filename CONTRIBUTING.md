# Contributing to Propmind

## Branch Naming

Todas as branches devem seguir o padrão:

```
PMT-{id}/kebab-case-description
```

**Exemplos:**
- `PMT-101/add-dependabot`
- `PMT-183/fix-contract-charge-constraint`

**Branches permanentes** (exceções): `main`, `develop`, `release/*`, `hotfix/*`

Este padrão habilita automação cross-repo e rastreamento de issues no Linear. Aplicado via GitHub Branch Ruleset — pushes de branches fora do padrão são rejeitados pelo servidor.

## Conventional Commits

Todas as mensagens de commit devem seguir o padrão [Conventional Commits](https://www.conventionalcommits.org/):

```
type(scope): description
```

**Tipos aceitos:**

| Type | Quando usar |
|---|---|
| `feat` | Nova funcionalidade |
| `fix` | Correção de bug |
| `refactor` | Reestruturação de código sem mudança de comportamento |
| `test` | Adição ou correção de testes |
| `docs` | Documentação apenas |
| `ci` | Configuração de CI/CD |
| `chore` | Manutenção (deps, config, tooling) |

**Breaking changes:** adicionar `!` ao type — `feat!:` ou `fix!:`

**Exemplos:**
- `feat(contracts): add charge validation endpoint`
- `fix(api): guard delete against linked payments`
- `ci(control-panel): add automated PR review`
- `chore(deps): bump sqlalchemy to 2.0.40`

Aplicado localmente via `pre-commit` (Python) / `husky` (Node.js) e no CI via `commitlint-github-action`.

## Architecture Decision Records (ADRs)

Decisões arquiteturais de ecossistema são documentadas como ADRs em [`propmind-dev-cli/docs/adr/`](https://github.com/Propmind/propmind-dev-cli/tree/main/docs/adr).

Formato: `E-NNN-short-title.md`

Leia [`E-000`](https://github.com/Propmind/propmind-dev-cli/blob/main/docs/adr/E-000-ai-first-context-engineering.md) para o princípio guia desta estrutura.
