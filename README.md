# Heroku Deployment Workflows

A collection of GitHub actions and workflows related to application deployments to Heroku.

Note that these workflows assume that the Heroku application names
follow our standard of `invh-<application_name>-<environment>`. Applications that do not follow
this standard _must_ provide their Heroku application names in the deployment section of their
[.repo-metadata.yaml](https://github.com/invitation-homes/technology-decisions/blob/main/resources/.repo-metadata.yaml) file. An example of this can be found [here](https://github.com/invitation-homes/atlas/blob/main/.repo-metadata.yaml).

## Workflows

### deploy-heroku-application.yml

This workflow performs the following steps:

- Deploys the application to the specified environment
- Records the deployment in our CI/CD database
- For production deployments, creates the production release tag in the format of `<major>.<minor>.<patch>`

#### Example Usage

```yaml
name: Deploy Application

on:
  workflow_dispatch:
    inputs:
      environment:
        description: The target deployment environment
        required: true
        type: choice
        options:
          - 'dev'
          - 'qa'
          - 'uat'
          - 'prod'

jobs:
  call_shared_workflow:
    uses: invitation-homes/heroku-deployment-workflows/.github/workflows/deploy-heroku-application.yml@v1
    secrets: inherit
    with:
      environment: ${{ github.event.inputs.environment }}
```

### auto-deploy-heroku-application.yml

This workflow performs the following steps:

- Create a unique tag for the commit in the format of `<major>.<minor>.<patch>-<github_run_number>-<short_sha>`
- Deploys the application to dev. Enabled by default, but can be disabled by adding a secret named `AUTO_DEPLOY_TO_DEV` with a value of false.
- Deploys the application to qa. Disabled by default, but can be enabled by adding a secret named `AUTO_DEPLOY_TO_QA` with a value of true.
- Records the deployment in our CI/CD database

#### Example Usage

```yaml
name: Auto Deploy Application

on:
  push:
    branches:
      - 'main'

jobs:
  call_shared_workflow:
    uses: invitation-homes/heroku-deployment-workflows/.github/workflows/auto-deploy-heroku-application.yml@v1
    secrets: inherit
```
