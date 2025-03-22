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