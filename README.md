# GitHub Actions & Workflows Overview

This repository uses **GitHub Actions** to automate CI/CD processes across multiple environments and brands. Below you'll find documentation on workflows for the `dev`, `staging`, and `main` environments, as well as guidelines for naming environment variables and secrets—especially in multi-brand contexts.

---

## Environments

### 1. **dev**
- Used for feature development, testing, and integration.
- Deploys are triggered on push events to branches prefixed with `dev-` or by pull requests targeting `dev`.

### 2. **staging**
- Serves as a pre-production environment for QA and user acceptance testing.
- Deploys are triggered on push events to the `staging` branch or merges from `dev`.

### 3. **main**
- Represents the production environment.
- Deploys are triggered on push events to the `main` branch or merges from `staging`.

---

## Workflows

### Typical Workflow Steps
- **Checkout code**: Uses `actions/checkout`.
- **Setup environment**: Configures language/runtime as needed.
- **Run tests**: Executes unit/integration tests.
- **Build & package**: Prepares artifacts.
- **Deploy**: Publishes to the target environment.

### Example Workflow File Structure

```yaml
# .github/workflows/deploy-dev.yaml
name: Deploy Dev

on:
  push:
    branches:
      - 'dev-*'

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: dev
    steps:
      - uses: actions/checkout@v3
      - name: Set up Node.js
        uses: actions/setup-node@v3
      - name: Deploy
        run: ./deploy.sh
```

Each environment has a dedicated workflow YAML file (e.g., `deploy-dev.yaml`, `deploy-staging.yaml`, `deploy-main.yaml`).

---

## Brand Support

Workflows support deployment for multiple brands. Brand-specific logic uses environment variables and secrets named according to the brand.

### Example Brands
- `acme`
- `globex`
- `initech`

---

## Variable & Secret Naming Rules

To handle multiple brands, use **environment variables** and **secrets** with clear, consistent naming:

### Environment Variables

- Format: `BRANDNAME_<VARIABLE>`
- Example: `ACME_API_URL`, `GLOBEX_DB_PASSWORD`

### Secrets

- Format: `BRANDNAME_<SECRET_NAME>`
- Example: `ACME_DEPLOY_KEY`, `INITECH_AWS_SECRET`

#### Tips:
- Use uppercase for brand names and variables/secrets.
- Separate brand name and variable/secret name with `_`.
- Avoid generic names like `API_URL`; always prefix with brand.
- For workflow-level secrets, use the `env:` key or reference secrets with `${{ secrets.BRANDNAME_SECRETNAME }}`.

### Example Usage in Workflow

```yaml
env:
  API_URL: ${{ secrets.ACME_API_URL }}

steps:
  - name: Deploy for Acme
    run: ./deploy.sh --brand acme
    env:
      API_URL: ${{ secrets.ACME_API_URL }}
```

---

## Best Practices

- **Group secrets by environment and brand** (e.g., `ACME_STAGING_API_KEY`, `GLOBEX_DEV_DB_URL`).
- **Document all variables and secrets** in your workflow files or in this README for clarity.
- **Rotate secrets regularly** and limit access per environment and brand.

---

## References

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Environment Variables in GitHub Actions](https://docs.github.com/en/actions/learn-github-actions/environment-variables)
- [Encrypted Secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets)

---

For more details or to add a new brand, update workflow YAML files and follow the variable/secret naming conventions above.
