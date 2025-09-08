Environments-Lab
To use multiple environments, secrets, and variables in a GitHub Actions workflow, you need to define them in your repository settings first. Then, you can reference them in your YAML file. This guide explains a sample workflow file, multiple-environments.yml, that demonstrates this process.

Workflow Overview
This workflow is triggered manually and has three jobs: dev_deploy, qa_deploy, and prod_deploy. Each job uses a different environment, demonstrating how to handle secrets and variables specific to each.

YAML
```yaml
name: Deploy to Multiple Environments

on:
  workflow_dispatch:

jobs:
  dev_deploy:
    runs-on: ubuntu-latest
    environment:
      name: development
      url: https://dev.example.com
    steps:
      - name: Deploy to Development
        run: |
          echo "Deploying to the development environment..."
          echo "Using a secret specific to this environment: ${{ secrets.DEV_SECRET }}"
          echo "Using a variable specific to this environment: ${{ vars.DEV_VAR }}"
          echo "Deployment URL: ${{ env.environment_url }}"
          
  qa_deploy:
    runs-on: ubuntu-latest
    environment:
      name: qa
      url: https://qa.example.com
    needs: dev_deploy
    steps:
      - name: Deploy to QA
        run: |
          echo "Deploying to the QA environment..."
          echo "Using a secret specific to this environment: ${{ secrets.QA_SECRET }}"
          echo "Using a variable specific to this environment: ${{ vars.QA_VAR }}"
          echo "Deployment URL: ${{ env.environment_url }}"

  prod_deploy:
    runs-on: ubuntu-latest
    environment:
      name: production
      url: https://prod.example.com
    needs: qa_deploy
    steps:
      - name: Deploy to Production
        run: |
          echo "Deploying to the production environment..."
          echo "Using a secret specific to this environment: ${{ secrets.PROD_SECRET }}"
          echo "Using a variable specific to this environment: ${{ vars.PROD_VAR }}"
          echo "Deployment URL: ${{ env.environment_url }}"
```

How to Set Up the Workflow
1. Configure Environments
Before you can run the workflow, you must set up the environments in your GitHub repository. Go to Settings > Environments and create three new environments: development, qa, and production.

2. Add Secrets and Variables
Within each environment, add the secrets and variables that the workflow will use.

For the development environment, add:

A secret named DEV_SECRET with a value like dev_api_key_123.

A variable named DEV_VAR with a value like dev_version_1.0.

For the qa environment, add:

A secret named QA_SECRET with a value like qa_api_key_456.

A variable named QA_VAR with a value like qa_version_1.2.

For the production environment, add:

A secret named PROD_SECRET with a value like prod_api_key_789.

A variable named PROD_VAR with a value like prod_version_2.0.

3. Review the YAML File
on: workflow_dispatch:: This trigger allows you to manually run the workflow from the Actions tab.

environment:: This key is used in each job to specify which environment it will run in. Using this key enables the job to access secrets and variables scoped to that specific environment.

needs:: This key ensures that the jobs run in a specific order: dev_deploy runs first, followed by qa_deploy, and finally prod_deploy.

secrets.SECRET_NAME and vars.VAR_NAME: This syntax is how you reference secrets and variables from the defined environment. GitHub automatically resolves the correct value based on the environment specified for the job.

env.environment_url: This is a built-in environment variable that automatically gets populated with the URL you defined for the environment. This is useful for creating a direct link to the deployment.