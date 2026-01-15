# CI/CD Helpers Exercises

## Easy Exercises (1-4)

### Exercise 1: GitHub Actions Basic Workflow
Write a workflow file that:
- Triggers on push and pull_request
- Runs on ubuntu-latest
- Has single job with checkout step
- Prints hello message
- Reports success/failure

**Hint**: Create `.github/workflows/basic.yml`

**Requirements**:
- Valid YAML syntax
- Proper indentation
- Clear job names
- Status reporting

### Exercise 2: Build and Test Job
Create workflow that:
- Checks out code
- Sets up build environment
- Builds application
- Runs test suite
- Reports test results

**Hint**: Add build and test steps to workflow

**Requirements**:
- Build step working
- Test step with exit code
- Pass/fail status
- Step naming

### Exercise 3: Docker Image Build and Push
Write workflow for:
- Building Docker image from Dockerfile
- Tagging with branch/tag/SHA
- Push to registry (Docker Hub or GitHub Registry)
- Output image digest

**Hint**: Use docker/build-push-action@v4

**Requirements**:
- Image built successfully
- Tagged correctly
- Registry credentials handled
- Digest captured

### Exercise 4: Artifact Upload and Download
Create workflow that:
- Builds application
- Creates artifact (tar, zip, etc.)
- Uploads artifact with name
- Downloads in separate job
- Verifies artifact exists

**Hint**: Use actions/upload-artifact, actions/download-artifact

**Requirements**:
- Artifact compressed
- Upload succeeds
- Download in other job
- Verification step

## Medium Exercises (5-10)

### Exercise 5: Environment-based Deployment
Build workflow that:
- Different steps for dev/staging/prod
- Conditional deployment based on branch
- Uses environment secrets
- Deploys to correct endpoint
- Validates deployment

**Hint**: Use `if:` conditions, environment vars

**Requirements**:
- Dev on feature branches
- Staging on develop
- Prod on main only
- Secrets per environment

### Exercise 6: Multi-job Pipeline with Dependencies
Create workflow with:
- Build job
- Test job (depends on build)
- Security scan job (parallel with test)
- Deploy job (only if all pass)
- Notification job

**Hint**: Use `needs:` for dependencies

**Requirements**:
- Job ordering correct
- Parallel jobs where appropriate
- Pass/fail propagation
- Clear dependencies

### Exercise 7: Manual Approval and Rollback
Write workflow that:
- Auto-deploys to staging
- Requires manual approval for prod
- Stores previous version info
- Can rollback on demand
- Logs all deployments

**Hint**: Use `workflow_dispatch`, environment protection rules

**Requirements**:
- Manual trigger option
- Approval checkpoint
- Version tracking
- Rollback capability

### Exercise 8: Slack/Email Notifications
Build workflow that:
- Sends notification on success
- Sends notification on failure
- Includes build info (commit, duration)
- Links to logs/artifacts
- Mentions responsible developer

**Hint**: Use actions for Slack/Email, github context

**Requirements**:
- Different message content
- Conditional notifications
- Include metadata
- Proper formatting

### Exercise 9: Code Quality and Security Scanning
Create workflow that:
- Runs linting (eslint/flake8)
- Security scan (SAST)
- Dependency check
- Code coverage report
- Blocks merge if critical issues

**Hint**: Use CodeQL, Snyk, or custom scanners

**Requirements**:
- Multiple scan types
- Report generation
- Pass/fail thresholds
- Issue linking

### Exercise 10: Complete Production CI/CD Pipeline
Build comprehensive workflow that:
- Builds and tests on every push
- Runs security scans
- Builds Docker image
- Pushes to registry with versioning
- Auto-deploys to staging
- Manual promotion to production
- Includes monitoring and rollback
- Sends notifications with status
- Tracks deployment metrics

**Hint**: Combine all previous exercises

**Requirements**:
- Multi-stage pipeline
- Security/quality gates
- Multiple environments
- Notifications
- Metrics tracking
- Rollback plan
