# Example: GitHub Actions Workflow for WIF

This file provides a complete GitHub Actions workflow that uses Workload Identity Federation (WIF) to authenticate to Google Cloud without a service account key. This workflow assumes you have already set up the WIF pool, provider, and IAM bindings using the `terraform-wif-github.md` snippet.

## GitHub Workflow Snippet (`.github/workflows/gcp-auth.yml`)

```yaml
name: 'Authenticate to GCP and Run GCloud'

on:
  push:
    branches:
      - main

jobs:
  gcp-auth-example:
    runs-on: ubuntu-latest

    # 1. Add permissions for OIDC token generation
    permissions:
      contents: 'read'
      id-token: 'write'

    steps:
      - name: 'Checkout code'
        uses: 'actions/checkout@v3'

      - name: 'Authenticate to Google Cloud'
        id: 'auth'
        uses: 'google-github-actions/auth@v1'
        with:
          # The full resource name of the WIF provider
          workload_identity_provider: 'projects/<YOUR_PROJECT_NUMBER>/locations/global/workloadIdentityPools/<YOUR_POOL_ID>/providers/<YOUR_PROVIDER_ID>'
          # The email of the service account to impersonate
          service_account: 'your-service-account-email@your-gcp-project-id.iam.gserviceaccount.com'

      - name: 'Run gcloud command'
        run: |-
          gcloud storage ls
```

### Key Concepts
- **`permissions: id-token: 'write'`**: This is mandatory. It gives the GitHub Actions runner permission to request an OIDC JWT token from GitHub's token server. This token is then presented to GCP.
- **`google-github-actions/auth@v1`**: This is the official Google action that handles the complex token exchange process for you. It gets the OIDC token from GitHub, sends it to GCP's Security Token Service (STS), and receives a short-lived GCP access token in return.
- **`workload_identity_provider`**: This must be the full, correct path to the WIF provider you created with Terraform.
- **`service_account`**: This is the GCP service account that the GitHub Action will impersonate. The WIF provider must have an IAM binding granting it the `roles/iam.workloadIdentityUser` role on this service account.