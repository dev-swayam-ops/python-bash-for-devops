# CI/CD Helpers and Pipeline Automation

## What You'll Learn
- CI/CD pipeline concepts and stages
- GitHub Actions workflow creation
- Jenkins pipeline scripting
- GitLab CI/CD configuration
- Docker image builds in pipelines
- Testing automation in CI
- Deployment strategies (blue-green, canary)
- Pipeline monitoring and notifications
- Artifact management and versioning

## Prerequisites
- Completed: [11-kubernetes-automation](../11-kubernetes-automation)
- Git repository knowledge
- Basic Docker understanding
- Understanding of build/test/deploy stages
- Familiarity with YAML syntax

## Key Concepts
- **Pipeline**: Sequence of stages (build, test, deploy)
- **Trigger**: Events that start pipeline (push, PR, schedule)
- **Stage**: Logical grouping of steps
- **Artifact**: Output files (binaries, container images)
- **Environment**: Dev/staging/production targets
- **Secrets**: API keys, credentials (encrypted)
- **Status Checks**: Tests that must pass
- **Deployment**: Release to environment

## Hands-on Lab: Build a Simple CI/CD Pipeline

### Step 1: Create GitHub Actions Workflow
```yaml
cat > .github/workflows/ci.yml << 'EOF'
name: CI Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Build
        run: echo "Building application..."
      
      - name: Test
        run: echo "Running tests..."
      
      - name: Success
        run: echo "✓ Pipeline passed"
EOF

# Expected: Workflow file created
# Trigger: Push to main or create PR
```

### Step 2: Add Build Step
```yaml
- name: Build Docker Image
  run: |
    docker build -t myapp:${{ github.sha }} .
    docker save myapp:${{ github.sha }} > image.tar
```

### Step 3: Add Test Step
```yaml
- name: Run Tests
  run: |
    # Run unit tests
    npm test
    # Run integration tests
    npm run test:integration
```

### Step 4: Store Artifacts
```yaml
- name: Upload Artifacts
  uses: actions/upload-artifact@v3
  with:
    name: docker-image
    path: image.tar
```

### Step 5: Deploy to Environment
```yaml
- name: Deploy to Staging
  if: github.ref == 'refs/heads/main'
  run: |
    kubectl set image deployment/app app=myapp:${{ github.sha }}
```

## Validation Checklist
- [ ] Workflow file valid YAML syntax
- [ ] Pipeline triggers on expected events
- [ ] Build step completes successfully
- [ ] Tests run and pass
- [ ] Artifacts uploaded correctly
- [ ] Deployment only on main branch
- [ ] Status checks visible on PR

## Cleanup
```bash
rm .github/workflows/ci.yml
# Delete workflow runs in GitHub UI
```

## Common Mistakes
1. **Wrong trigger events**: Pipeline doesn't run
2. **Missing secrets**: API keys hardcoded or missing
3. **Failing tests block deploy**: No skip option
4. **No artifact retention**: Artifacts disappear
5. **Credentials in logs**: Leaked sensitive data

## Troubleshooting

### Pipeline not triggering
- **Cause**: Wrong event or branch
- **Solution**: Check `on:` section, verify branch name

### Build fails with missing dependencies
- **Cause**: Dependencies not installed
- **Solution**: Add install step before build

### Tests passing locally but failing in CI
- **Cause**: Environment differences
- **Solution**: Check environment variables, paths

### Deployment fails silently
- **Cause**: No error checking
- **Solution**: Add error handling, check logs

## Next Steps
1. Practice with [exercises](exercises.md)
2. Review [CI/CD cheatsheet](cheatsheet.md)
3. Implement multi-stage pipelines
4. Add Slack notifications
5. Set up artifact registry
