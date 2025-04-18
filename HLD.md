# GitHub Actions - High-Level Design (HLD) Overview
GitHub Actions is a CI/CD (Continuous Integration and Continuous Deployment) automation tool that enables developers to automate workflows, run tests, and deploy applications directly from a GitHub repository. It works by defining workflows using YAML files that get triggered by events such as pushes, pull requests, and schedule-based executions.

### Workflows
A workflow is an automated process defined in a repository. Workflows are written in YAML and stored in .github/workflows/.
```
name: CI Workflow  # Workflow name
on: [push, pull_request]  # Events that trigger the workflow
jobs:
  build:
    runs-on: ubuntu-latest  # Runner environment
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      - name: Install dependencies
        run: npm install
      - name: Run tests
        run: npm test
```

### Events
Events are triggers that start a workflow. Some common events include:

push: When a commit is pushed to a branch.

pull_request: When a PR is created or updated.

schedule: Time-based triggers using cron syntax.

workflow_dispatch: Manually triggered workflows.

Example of a scheduled event running at midnight daily:
```
on:
  schedule:
    - cron: '0 0 * * *'

```
###  Jobs
Jobs define units of work in a workflow.

Each job runs independently unless dependencies are defined.

They can run in parallel or sequentially.

Example with dependencies:
```
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      - name: Build the project
        run: npm run build

  test:
    needs: build  # Runs only after 'build' job completes
    runs-on: ubuntu-latest
    steps:
      - name: Run tests
        run: npm test

```

###  Runners
A runner is a virtual machine that executes the workflow jobs.

GitHub-hosted runners: Pre-configured environments (ubuntu-latest, windows-latest, macos-latest).

Self-hosted runners: Custom environments hosted on private infrastructure.

Example of running on a Windows environment:
```
runs-on: windows-latest

```

### Steps
Steps are individual tasks inside a job.

They can use actions (prebuilt reusable steps) or run commands.

```yaml
steps:
  - name: Checkout code
    uses: actions/checkout@v3
  - name: Run a script
    run: echo "Hello, GitHub Actions!"
```

### Actions
Actions are reusable components inside workflows. They can be:
- **Official GitHub actions** (e.g., `actions/checkout`, `actions/setup-node`).
- **Community actions** from the GitHub Marketplace.
- **Custom actions** stored in repositories.

Example of using an official GitHub action to set up Node.js:

```yaml
steps:
  - name: Set up Node.js
    uses: actions/setup-node@v3
    with:
      node-version: '16'
```

### Artifacts and Caching
GitHub Actions allows storing and caching files to improve workflow efficiency.

- **Caching dependencies**:
  ```yaml
  - name: Cache Node modules
    uses: actions/cache@v3
    with:
      path: ~/.npm
      key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
      restore-keys: |
        ${{ runner.os }}-node-
  ```

- **Uploading build artifacts**:
  ```yaml
  - name: Upload Build Artifact
    uses: actions/upload-artifact@v3
    with:
      name: my-artifact
      path: dist/
  ```

## Advanced GitHub Actions Use Cases

### Deploying to AWS S3
```yaml
name: Deploy to S3

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v1
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-1

      - name: Deploy to S3
        run: aws s3 sync ./build s3://my-bucket-name --delete
```
### Running a Python Script on Schedule
```yaml
name: Daily Python Job

on:
  schedule:
    - cron: '0 2 * * *'  # Runs daily at 2 AM UTC

jobs:
  run-script:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.9'

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Run script
        run: python script.py
```
### Using Secrets for Secure Credentials
```yaml
steps:
  - name: Authenticate API
    run: curl -H "Authorization: Bearer ${{ secrets.API_KEY }}" https://api.example.com/data
```

## Debugging and Best Practices

### Debugging Workflows
- Use `ACTIONS_RUNNER_DEBUG` to enable verbose logging:
  ```yaml
  env:
    ACTIONS_RUNNER_DEBUG: true
  ```
- Use the `actions/upload-artifact` action to store logs for debugging.

### Best Practices
✅ **Use job dependencies** (`needs:`) to control execution flow.  
✅ **Use caching** to speed up builds.  
✅ **Use matrix strategy** to test on multiple platforms.  
✅ **Use reusable workflows** to avoid duplication.  
✅ **Secure credentials** using GitHub secrets.  

## Conclusion
GitHub Actions is a powerful automation tool for CI/CD workflows. By leveraging **workflows, jobs, runners, caching, secrets, and matrix builds**, developers can create scalable pipelines for testing, deployment, and monitoring.