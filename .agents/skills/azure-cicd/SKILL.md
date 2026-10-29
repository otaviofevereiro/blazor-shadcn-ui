---
name: azure-cicd
description: >
  Two environments with required reviewers, OIDC login, secrets out of git, no production
  deploy from a feature branch. Use when *.yml, *.yaml, or *.bicep files are in play.
---

# Azure and CI/CD

**When:** `*.yml`, `*.yaml`, or `*.bicep` files are in play.

- Staging and production deploys use GitHub Environments with **required reviewers**. The deploy job declares `environment:`; approval is configured in the repo Environment settings, not in the workflow YAML.
- Staging deploys from the default branch (after approval). Production deploys from a version tag (and may allow `workflow_dispatch`) after approval.
- Separate App Service, SQL, Storage, and Key Vault per environment. Never copy production PII into staging.
- Login to Azure with OIDC. Do not add `AZURE_CLIENT_SECRET` if federated credentials work. Connection strings, passwords, and API keys go in Key Vault - not git, not Bicep parameters, not GitHub Actions secret values for the app.
- CI on PRs: build, analyzers (warnings as errors), tests. Do not deploy if tests fail.
- Do not deploy production from a feature branch or skip security tests with a filter.
- Idle reserved SKUs (App Service plan, Azure SQL Basic) bill while the staging resource group exists. A manual workflow may delete that group to park cost; it must not delete the subscription. Soft-deleted Key Vault (purge protection) is recovered on the next staging deploy before Bicep create.

```yaml
# BAD
on: { push: { branches: ["*"] } }
# deploy to prod with a client secret in repo secrets

# GOOD
on: { push: { tags: ["v*"] } }
# environment: production (required reviewers) + azure/login OIDC
```
