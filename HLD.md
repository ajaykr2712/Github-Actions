# GitHub Actions - High-Level Design (HLD) Overview
GitHub Actions is a CI/CD (Continuous Integration and Continuous Deployment) automation tool that enables developers to automate workflows, run tests, and deploy applications directly from a GitHub repository. It works by defining workflows using YAML files that get triggered by events such as pushes, pull requests, and schedule-based executions.

## 1.1 Workflows
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

## 1.2 Events
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
## 1.3 Jobs
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

## 1.4 Runners
A runner is a virtual machine that executes the workflow jobs.

GitHub-hosted runners: Pre-configured environments (ubuntu-latest, windows-latest, macos-latest).

Self-hosted runners: Custom environments hosted on private infrastructure.

Example of running on a Windows environment:
```
runs-on: windows-latest

```

# 1.5 Steps
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