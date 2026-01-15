# CI/CD Helpers Solutions

## Solution 1: GitHub Actions Basic Workflow

```yaml
# .github/workflows/basic.yml
name: Basic Workflow

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]

jobs:
  hello:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Print hello message
        run: echo "Hello from GitHub Actions!"
      
      - name: Report success
        if: success()
        run: echo "✓ Workflow completed successfully"
      
      - name: Report failure
        if: failure()
        run: echo "✗ Workflow failed"
```

**Explanation**: Basic workflow with checkout, echo steps, conditional success/failure reporting. Triggers on push and PR.

---

## Solution 2: Build and Test Job

```yaml
# .github/workflows/build-test.yml
name: Build and Test

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]

jobs:
  build-test:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.9'
      
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt
          pip install pytest
      
      - name: Build application
        run: |
          echo "Building application..."
          # Build command here
          python -m py_compile src/
          echo "✓ Build successful"
      
      - name: Run tests
        run: |
          echo "Running tests..."
          pytest tests/ -v --tb=short
          echo "✓ Tests passed"
      
      - name: Generate coverage report
        if: always()
        run: |
          pytest tests/ --cov=src/ --cov-report=xml
```

**Explanation**: Sets up Python environment, installs dependencies, builds application, runs pytest, generates coverage report.

---

## Solution 3: Docker Image Build and Push

```yaml
# .github/workflows/docker-build.yml
name: Docker Build and Push

on:
  push:
    branches: [ main ]
    tags: [ 'v*' ]

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2
      
      - name: Login to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}
      
      - name: Extract metadata
        id: meta
        uses: docker/metadata-action@v4
        with:
          images: ${{ secrets.DOCKER_USERNAME }}/myapp
          tags: |
            type=semver,pattern={{version}}
            type=sha
            type=ref,event=branch
      
      - name: Build and push
        uses: docker/build-push-action@v4
        with:
          context: .
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
      
      - name: Output image digest
        run: echo "Image pushed with digest: ${{ steps.build.outputs.digest }}"
```

**Explanation**: Sets up Docker buildx, authenticates with registry, extracts metadata with semantic versioning, builds and pushes image.

---

## Solution 4: Artifact Upload and Download

```yaml
# .github/workflows/artifacts.yml
name: Artifact Management

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Build application
        run: |
          mkdir -p build
          echo "Build artifact v1.0" > build/app.jar
          echo "Build metadata" > build/metadata.txt
      
      - name: Create artifact archive
        run: |
          tar -czf build-artifact.tar.gz build/
          ls -lh build-artifact.tar.gz
      
      - name: Upload artifact
        uses: actions/upload-artifact@v3
        with:
          name: build-output
          path: build-artifact.tar.gz
          retention-days: 30
  
  deploy:
    needs: build
    runs-on: ubuntu-latest
    
    steps:
      - name: Download artifact
        uses: actions/download-artifact@v3
        with:
          name: build-output
      
      - name: Verify artifact
        run: |
          if [ -f build-artifact.tar.gz ]; then
            echo "✓ Artifact found"
            tar -tzf build-artifact.tar.gz | head -5
          else
            echo "✗ Artifact not found"
            exit 1
          fi
```

**Explanation**: Build job creates and uploads artifact, deploy job downloads it with dependency, verifies existence and contents.

---

## Solution 5: Environment-based Deployment

```yaml
# .github/workflows/env-deploy.yml
name: Environment Deployment

on:
  push:
    branches: [ main, develop, feature/* ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Determine environment
        id: env
        run: |
          if [ "${{ github.ref }}" = "refs/heads/main" ]; then
            echo "ENV=production" >> $GITHUB_OUTPUT
            echo "ENDPOINT=${{ secrets.PROD_ENDPOINT }}" >> $GITHUB_OUTPUT
          elif [ "${{ github.ref }}" = "refs/heads/develop" ]; then
            echo "ENV=staging" >> $GITHUB_OUTPUT
            echo "ENDPOINT=${{ secrets.STAGING_ENDPOINT }}" >> $GITHUB_OUTPUT
          else
            echo "ENV=dev" >> $GITHUB_OUTPUT
            echo "ENDPOINT=${{ secrets.DEV_ENDPOINT }}" >> $GITHUB_OUTPUT
          fi
      
      - name: Deploy to dev
        if: steps.env.outputs.ENV == 'dev'
        run: |
          echo "Deploying to dev..."
          curl -X POST ${{ steps.env.outputs.ENDPOINT }}/deploy \
            -H "Authorization: Bearer ${{ secrets.DEV_TOKEN }}"
      
      - name: Deploy to staging
        if: steps.env.outputs.ENV == 'staging'
        run: |
          echo "Deploying to staging..."
          curl -X POST ${{ steps.env.outputs.ENDPOINT }}/deploy \
            -H "Authorization: Bearer ${{ secrets.STAGING_TOKEN }}"
      
      - name: Deploy to production
        if: steps.env.outputs.ENV == 'production'
        run: |
          echo "Deploying to production..."
          curl -X POST ${{ steps.env.outputs.ENDPOINT }}/deploy \
            -H "Authorization: Bearer ${{ secrets.PROD_TOKEN }}"
```

**Explanation**: Determines environment from branch, sets endpoint and secrets accordingly, deploys to appropriate environment with conditional steps.

---

## Solution 6: Multi-job Pipeline with Dependencies

```yaml
# .github/workflows/multi-job.yml
name: Multi-Job Pipeline

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    outputs:
      version: ${{ steps.version.outputs.version }}
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Extract version
        id: version
        run: echo "version=$(cat VERSION)" >> $GITHUB_OUTPUT
      
      - name: Build
        run: echo "Building v${{ steps.version.outputs.version }}"
  
  test:
    needs: build
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      - name: Run tests
        run: echo "Running tests..."
  
  security-scan:
    needs: build
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      - name: Security scan
        run: echo "Running security scan..."
  
  deploy:
    needs: [test, security-scan]
    runs-on: ubuntu-latest
    if: success()
    
    steps:
      - uses: actions/checkout@v3
      - name: Deploy
        run: echo "Deploying version ${{ needs.build.outputs.version }}"
```

**Explanation**: Build passes version output, test and security scan run in parallel (both need build), deploy runs only if both pass, uses output from build job.

---

## Solution 7: Manual Approval and Rollback

```yaml
# .github/workflows/approval-rollback.yml
name: Approval and Rollback

on:
  push:
    branches: [ main ]
  workflow_dispatch:
    inputs:
      action:
        description: 'Action to perform'
        required: true
        default: 'deploy'
        type: choice
        options:
          - deploy
          - rollback

jobs:
  deploy-staging:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Deploy to staging
        run: |
          echo "Deploying to staging..."
          echo "Current version: ${{ github.sha }}" > deployment.txt
          echo "Deployment timestamp: $(date)" >> deployment.txt
      
      - name: Upload deployment info
        uses: actions/upload-artifact@v3
        with:
          name: deployment-info
          path: deployment.txt
  
  approve-production:
    needs: deploy-staging
    runs-on: ubuntu-latest
    environment:
      name: production
      url: https://prod.example.com
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Deploy to production
        if: github.event_name == 'workflow_dispatch' || github.event.pull_request.merged == true
        run: echo "Deploying ${{ github.sha }} to production"
  
  rollback:
    if: github.event.inputs.action == 'rollback'
    runs-on: ubuntu-latest
    
    steps:
      - name: Get previous version
        id: previous
        run: echo "version=previous-v1.0" >> $GITHUB_OUTPUT
      
      - name: Rollback to previous version
        run: echo "Rolling back to ${{ steps.previous.outputs.version }}"
```

**Explanation**: Auto-deploys to staging, requires manual approval for production via environment protection, supports rollback action.

---

## Solution 8: Slack Notifications

```yaml
# .github/workflows/notifications.yml
name: Pipeline with Notifications

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Build
        run: npm run build
    
    # Success notification
    - name: Notify Slack - Success
      if: success()
      uses: slackapi/slack-github-action@v1.24.0
      with:
        webhook-url: ${{ secrets.SLACK_WEBHOOK }}
        payload: |
          {
            "text": "✓ Build Successful",
            "blocks": [
              {
                "type": "section",
                "text": {
                  "type": "mrkdwn",
                  "text": "*Build Passed*\nCommit: ${{ github.sha }}\nAuthor: ${{ github.actor }}"
                }
              }
            ]
          }
    
    # Failure notification
    - name: Notify Slack - Failure
      if: failure()
      uses: slackapi/slack-github-action@v1.24.0
      with:
        webhook-url: ${{ secrets.SLACK_WEBHOOK }}
        payload: |
          {
            "text": "✗ Build Failed",
            "blocks": [
              {
                "type": "section",
                "text": {
                  "type": "mrkdwn",
                  "text": "*Build Failed*\nCommit: ${{ github.sha }}\nAuthor: ${{ github.actor }}\nLogs: ${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}"
                }
              }
            ]
          }
```

**Explanation**: Sends different Slack messages for success/failure, includes commit, author, and log links.

---

## Solution 9: Code Quality and Security Scanning

```yaml
# .github/workflows/quality-security.yml
name: Quality and Security

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  quality:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.9'
      
      - name: Install tools
        run: |
          pip install flake8 pylint bandit safety
      
      - name: Lint code
        run: flake8 src/ --max-line-length=100
      
      - name: Security check
        run: bandit -r src/
      
      - name: Dependency check
        run: safety check
      
      - name: Code coverage
        run: |
          pip install pytest-cov
          pytest --cov=src --cov-report=xml
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          file: ./coverage.xml
          fail_ci_if_error: true
```

**Explanation**: Runs linting (flake8), security scanning (bandit), dependency check (safety), and code coverage with uploading.

---

## Solution 10: Complete Production Pipeline

```yaml
# .github/workflows/complete-pipeline.yml
name: Complete Production Pipeline

on:
  push:
    branches: [ main, develop ]
  workflow_dispatch:

jobs:
  build-test:
    runs-on: ubuntu-latest
    outputs:
      image-tag: ${{ steps.meta.outputs.tags }}
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Run tests
        run: npm test -- --coverage
      
      - name: Build Docker image
        id: meta
        run: echo "tags=myapp:${{ github.sha }}" >> $GITHUB_OUTPUT

  security:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      - name: Security scan
        run: npm audit

  deploy-staging:
    needs: [build-test, security]
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      - name: Deploy to staging
        run: kubectl set image deployment/app app=${{ needs.build-test.outputs.image-tag }} -n staging

  deploy-production:
    needs: deploy-staging
    runs-on: ubuntu-latest
    environment: production
    if: github.ref == 'refs/heads/main'
    
    steps:
      - uses: actions/checkout@v3
      - name: Deploy to production
        run: kubectl set image deployment/app app=${{ needs.build-test.outputs.image-tag }} -n production

  notify:
    needs: [deploy-staging, deploy-production]
    runs-on: ubuntu-latest
    if: always()
    
    steps:
      - name: Notify Slack
        uses: slackapi/slack-github-action@v1.24.0
        with:
          webhook-url: ${{ secrets.SLACK_WEBHOOK }}
          payload: |
            {
              "text": "Pipeline ${{ job.status }}",
              "blocks": [{"type": "section", "text": {"type": "mrkdwn", "text": "Build: ${{ needs.build-test.result }}\nSecurity: ${{ needs.security.result }}\nStaging: ${{ needs.deploy-staging.result }}\nProduction: ${{ needs.deploy-production.result }}"}}]
            }
```

**Explanation**: Complete pipeline with build/test, security scanning, staged deployments, notifications on completion.

---

**Pro Tip**: Use `github.ref` to branch on specific branches, `secrets` for sensitive data, and `needs:` for job dependencies.
