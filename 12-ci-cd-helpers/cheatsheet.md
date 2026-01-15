# CI/CD Helpers Cheatsheet

## GitHub Actions Syntax

| Element | Purpose | Example |
|---------|---------|---------|
| `on:` | Trigger events | `on: [push, pull_request]` |
| `jobs:` | Define jobs | Multiple parallel jobs |
| `runs-on:` | Runner OS | `ubuntu-latest`, `macos-latest` |
| `steps:` | Job tasks | Sequential steps |
| `uses:` | Reuse action | `actions/checkout@v3` |
| `run:` | Execute command | `run: npm test` |
| `name:` | Step description | Clear naming |
| `if:` | Conditional step | `if: success()` |
| `env:` | Environment vars | `env: { KEY: value }` |

## Common Triggers

| Trigger | Event | Example |
|---------|-------|---------|
| `push` | Code push | `on: push` |
| `pull_request` | PR opened/updated | `on: pull_request` |
| `schedule` | Cron job | `schedule: [cron: '0 0 * * *']` |
| `workflow_dispatch` | Manual trigger | Buttons in GitHub UI |
| `release` | Release created | `on: release` |
| `tags` | Tag push | `tags: ['v*']` |

## Job Configuration

| Setting | Purpose | Example |
|---------|---------|---------|
| `needs:` | Job dependencies | `needs: build` |
| `if:` | Conditional job | `if: success()` |
| `environment:` | Deployment target | `environment: production` |
| `runs-on:` | Runner type | `ubuntu-latest` |
| `timeout-minutes:` | Max duration | `timeout-minutes: 60` |
| `strategy.matrix:` | Matrix build | `node-version: [12, 14, 16]` |

## Common Actions

| Action | Purpose | Example |
|--------|---------|---------|
| `actions/checkout@v3` | Clone repository | Standard first step |
| `actions/setup-python@v4` | Python environment | `python-version: '3.9'` |
| `actions/setup-node@v3` | Node.js environment | `node-version: 16` |
| `actions/cache@v3` | Cache dependencies | `path: node_modules` |
| `actions/upload-artifact@v3` | Store artifacts | `name: build-output` |
| `actions/download-artifact@v3` | Retrieve artifacts | `name: build-output` |
| `docker/setup-buildx-action@v2` | Docker builder | For multi-platform builds |
| `docker/login-action@v2` | Registry login | `username`, `password` |
| `docker/build-push-action@v4` | Build/push image | Standard Docker CI |

## GitHub Contexts

| Context | Purpose | Example |
|---------|---------|---------|
| `github.ref` | Branch/tag | `refs/heads/main` |
| `github.event_name` | Trigger type | `push`, `pull_request` |
| `github.sha` | Commit SHA | Full hash |
| `github.actor` | Username | PR author |
| `github.repository` | Repo name | `owner/repo` |
| `github.run_id` | Workflow run ID | Unique number |
| `github.server_url` | GitHub domain | `https://github.com` |

## Conditional Expressions

| Condition | Purpose | Example |
|-----------|---------|---------|
| `success()` | Job succeeded | `if: success()` |
| `failure()` | Job failed | `if: failure()` |
| `always()` | Always run | `if: always()` |
| `cancelled()` | Workflow cancelled | `if: cancelled()` |
| `contains()` | String contains | `if: contains(github.ref, 'main')` |
| `startsWith()` | String starts with | `if: startsWith(github.ref, 'refs/heads')` |
| `matrix.node` | Matrix variable | `node-version: [12, 14, 16]` |

## Secrets and Variables

| Feature | Usage | Example |
|---------|-------|---------|
| Repository secrets | Sensitive data | `${{ secrets.API_KEY }}` |
| Organization secrets | Shared secrets | Available to all repos |
| Environment secrets | Env-specific | Per-environment secrets |
| Variables | Non-sensitive | `${{ vars.ENVIRONMENT }}` |
| GITHUB_TOKEN | Auto token | Built-in token for actions |

## Docker in CI/CD

| Step | Command | Example |
|------|---------|---------|
| Build image | `docker build -t name:tag .` | Build Dockerfile |
| Push image | `docker push name:tag` | Push to registry |
| Run container | `docker run image cmd` | Execute in container |
| Docker buildx | `docker buildx build` | Multi-platform builds |
| Layer caching | `actions/cache` | Speed up builds |

## Pipeline Best Practices

| Practice | Reason | Implementation |
|----------|--------|---|
| Short steps | Easier debugging | 5-10 line steps |
| Meaningful names | Clear workflow | Descriptive step names |
| Fail fast | Quick feedback | Put quick checks first |
| Caching | Speed up builds | Cache dependencies |
| Secrets | Security | Use `secrets.*` vars |
| Notifications | Visibility | Slack/email on status |
| Artifact cleanup | Cost saving | `retention-days:` setting |

## Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| Workflow not running | Wrong trigger | Check `on:` section |
| Step fails silently | No error handling | Add `set -e` in shell |
| Can't find secret | Typo or missing | Check secret name exactly |
| Permission denied | Insufficient token | Use GITHUB_TOKEN |
| Timeout | Long running step | Add `timeout-minutes:` |
| Large artifacts | Missing cleanup | Add retention policy |

## Matrix Strategy Example

```yaml
strategy:
  matrix:
    node-version: [12, 14, 16, 18]
    os: [ubuntu-latest, macos-latest, windows-latest]

steps:
  - uses: actions/setup-node@v3
    with:
      node-version: ${{ matrix.node-version }}
```

## Deployment Protection Rules

```yaml
environment:
  name: production
  url: https://prod.example.com
  protection_rules:
    - type: required_reviewers
      count: 1
```

## Reusable Workflows

```yaml
# Call reusable workflow
jobs:
  call-workflow:
    uses: owner/repo/.github/workflows/reusable.yml@main
    with:
      input-name: value
    secrets:
      secret-name: ${{ secrets.SECRET }}
```

## Common Workflow Patterns

### On Every Push
```yaml
on: push
```

### On PR to Main
```yaml
on:
  pull_request:
    branches: [main]
```

### Scheduled Daily
```yaml
on:
  schedule:
    - cron: '0 0 * * *'  # Daily at midnight UTC
```

### On Release
```yaml
on:
  release:
    types: [published]
```

### Manual with Input
```yaml
on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Target environment'
        required: true
        default: 'staging'
```

## Common Exit Codes

| Code | Meaning | Example |
|------|---------|---------|
| 0 | Success | Script completed |
| 1 | General error | Test failure |
| 2 | Misuse | Wrong arguments |
| 127 | Command not found | Missing binary |

## Step Status in Conditions

| Status | Check | Example |
|--------|-------|---------|
| Success | `success()` | `if: success()` |
| Failure | `failure()` | `if: failure()` |
| Skipped | `skipped()` | `if: skipped()` |
| Always | Always runs | `if: always()` |

## Quick Reference - Job Status

```yaml
# After each job completes, check status
- name: After build
  if: always()  # Runs even if previous failed
  run: echo "Build status: ${{ job.status }}"
```

---

**Pro Tip**: Use `${{ github.sha }}` for unique image tags instead of `latest` - never rebuild same tag.
