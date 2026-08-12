# Terraform: Workload Identity Federation for GitHub Actions

This snippet shows how to configure Workload Identity Federation to allow a specific GitHub repository to impersonate a GCP Service Account. This is the recommended, keyless authentication method for CI/CD pipelines.

## Terraform Snippet

```terraform
# 1. Create the Workload Identity Pool to group external identities
resource "google_iam_workload_identity_pool" "github_pool" {
  project                   = "your-gcp-project-id"
  workload_identity_pool_id = "github-actions-pool"
  display_name              = "GitHub Actions Pool"
  description               = "Pool for GitHub Actions CI/CD"
}

# 2. Create a provider within the pool for GitHub (OIDC)
resource "google_iam_workload_identity_pool_provider" "github_provider" {
  project                            = google_iam_workload_identity_pool.github_pool.project
  workload_identity_pool_id          = google_iam_workload_identity_pool.github_pool.workload_identity_pool_id
  workload_identity_pool_provider_id = "github-provider"
  display_name                       = "GitHub OIDC Provider"
  attribute_mapping = {
    # Map GitHub token attributes to GCP attributes
    "google.subject"       = "assertion.sub"
    "attribute.actor"      = "assertion.actor"
    "attribute.repository" = "assertion.repository"
  }
  oidc {
    issuer_uri = "https://token.actions.githubusercontent.com"
  }
}

# 3. Grant the external identity permission to impersonate a Service Account
# This binds a specific GitHub repo to a specific GCP service account.
resource "google_service_account_iam_member" "wif_iam_binding" {
  service_account_id = "projects/your-gcp-project-id/serviceAccounts/your-service-account-email"
  role               = "roles/iam.workloadIdentityUser"
  
  # This is the critical part: only allow identities from a specific repo
  member             = "principalSet://iam.googleapis.com/${google_iam_workload_identity_pool.github_pool.name}/attribute.repository/your-org/your-repo-name"
}
```

### Key Concepts
- **`google_iam_workload_identity_pool`**: Creates a container for external identities.
- **`google_iam_workload_identity_pool_provider`**: Configures the trust relationship with the external IdP (GitHub's OIDC endpoint). The `attribute_mapping` is crucial for passing claims.
- **`google_service_account_iam_member`**: Grants the `workloadIdentityUser` role to a specific external principal, identified by its mapped attributes (e.g., the repository name). This allows the external identity to generate a short-lived token for the specified service account.