---
title: "WIF in Practice: From Setup to CI/CD"
date: 2026-08-20
tags: [landing-zone, gcp, wif, terraform, github, cicd, security]
summary: "An end-to-end guide for setting up and using Workload Identity Federation with GitHub Actions."
---

# WIF in Practice: From Setup to CI/CD

## Summary

This guide provides a practical, step-by-step walkthrough of how to implement Workload Identity Federation (WIF) for a GitHub Actions CI/CD pipeline. It covers the initial infrastructure setup with Terraform, how to test authentication using the `gcloud` CLI, and the final implementation in a GitHub workflow. This demonstrates a complete, keyless authentication pattern.

## Step 1: Define the Infrastructure with Terraform

The first step is to create the necessary GCP resources. This includes the WIF pool, the OIDC provider for GitHub, and the IAM binding that allows a specific GitHub repository to impersonate a GCP service account.

This is achieved using the Terraform code from `scripts/terraform-wif-github.md`. The key components are:
1.  **`google_iam_workload_identity_pool`**: Creates a pool to trust identities from GitHub.
2.  **`google_iam_workload_identity_pool_provider`**: Configures the pool to trust GitHub's OIDC token issuer and maps the `repository` claim.
3.  **`google_service_account_iam_member`**: Grants the `roles/iam.workloadIdentityUser` role to a principal representing your GitHub repository (`principalSet://.../attribute.repository/your-org/your-repo`), allowing it to impersonate a specific service account.

## Step 2: Test the Configuration with `gcloud`

Before implementing in a CI/CD pipeline, it's a best practice to test the WIF configuration locally. The `gcloud` CLI can simulate the authentication flow.

1.  **Create a credential configuration file (`cred-config.json`)**:
    This file tells `gcloud` how to get a token from the external provider (in this case, we simulate it) and exchange it for a GCP token.

    ```json
    {
      "type": "external_account",
      "audience": "//iam.googleapis.com/projects/<PROJECT_NUMBER>/locations/global/workloadIdentityPools/<POOL_ID>/providers/<PROVIDER_ID>",
      "subject_token_type": "urn:ietf:params:oauth:token-type:jwt",
      "token_url": "https://sts.googleapis.com/v1/token",
      "service_account_impersonation_url": "https://iamcredentials.googleapis.com/v1/projects/-/serviceAccounts/<SERVICE_ACCOUNT_EMAIL>:generateAccessToken",
      "credential_source": {
        "file": "path/to/fake_github_token.jwt"
      }
    }
    ```
    *(Note: For a real local test, the `credential_source` would point to a command or file that can generate a valid OIDC token from your IdP).*

2.  **Log in using the configuration**:
    This command tells `gcloud` to use the WIF flow instead of a user account.

    ```bash
    gcloud auth login --cred-file=cred-config.json
    ```

This step is crucial for debugging IAM permissions and WIF attribute mappings without running a full CI/CD pipeline.

## Step 3: Implement in a GitHub Actions Workflow

Once the infrastructure is created and tested, you can use it in your CI/CD pipeline. The official `google-github-actions/auth` action simplifies this process significantly.

The workflow from `scripts/github-workflow-wif.md` shows the final implementation:

```yaml
name: 'Authenticate to GCP via WIF'

permissions:
  contents: 'read'
  id-token: 'write' # Required to get an OIDC token from GitHub

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: 'Authenticate to Google Cloud'
        uses: 'google-github-actions/auth@v1'
        with:
          workload_identity_provider: 'projects/<PROJECT_NUMBER>/locations/global/workloadIdentityPools/<POOL_ID>/providers/<PROVIDER_ID>'
          service_account: '<SERVICE_ACCOUNT_EMAIL>'

      - name: 'Run gcloud command'
        run: 'gcloud storage ls'
```

The `google-github-actions/auth` action automatically handles fetching the OIDC token from GitHub and exchanging it for a short-lived GCP access token, which is then used by subsequent `gcloud` or client library calls. This completes the secure, keyless authentication loop.