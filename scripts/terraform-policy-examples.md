# Terraform: Policy Application Examples

This file provides practical Terraform snippets for applying both IAM Policies and Organization Policies. Understanding where and how to apply these is critical for landing zone governance.

## Core Principles: Where to Apply Policies

In GCP, there are two primary types of policies, and they follow opposite rules for application:

1.  **IAM Policies (Permissions)**: These control **who** can do **what**.
    *   **Rule of Thumb**: Apply at the **lowest possible level** in the resource hierarchy.
    *   **Why**: This follows the principle of least privilege. Granting broad permissions at the top level (e.g., Organization) is risky. It's safer to grant specific permissions only on the resources where they are needed (e.g., a project or a specific storage bucket).

2.  **Organization Policies (Guardrails)**: These control **what** is allowed within your cloud environment.
    *   **Rule of Thumb**: Apply at the **highest possible level** in the resource hierarchy.
    *   **Why**: This ensures consistent enforcement of security and governance constraints. A guardrail set at the Organization level (e.g., restricting resource locations) applies to all folders and projects, preventing exceptions and ensuring compliance.

The following examples illustrate these principles in practice.

## 1. IAM Policy Example (Granting Permissions)

**Scenario**: You want to grant a group of network administrators the ability to act as Compute Network Users across all projects within the `prod-environment` folder.

**Best Practice**: Apply the IAM binding at the **Folder Level**. This is better than applying it to every project individually.

```terraform
variable "prod_folder_id" {
  description = "The ID of the production environment folder (e.g., folders/12345)."
  type        = string
}

variable "network_admins_group" {
  description = "The email of the Google Group for network admins."
  type        = string
  # Example: "gcp-network-admins@your-domain.com"
}

# Apply an IAM policy binding at the folder level
resource "google_folder_iam_binding" "network_user_binding" {
  folder = var.prod_folder_id
  role   = "roles/compute.networkUser"

  members = [
    "group:${var.network_admins_group}",
  ]
}
```

## 2. Organization Policy Example (Setting Guardrails)

**Scenario**: You want to enforce a security guardrail across your entire organization that only allows IAM principals from your corporate domain (`your-domain.com`) to be added to IAM policies.

**Best Practice**: Apply the Organization Policy at the **Organization Level** for maximum enforcement.

```terraform
variable "organization_id" {
  description = "Your GCP Organization ID."
  type        = string
}

variable "corp_domain" {
  description = "Your corporate domain name."
  type        = string
}

resource "google_organization_policy" "allowed_domains_policy" {
  org_id     = var.organization_id
  constraint = "iam.allowedPolicyMemberDomains"

  list_policy {
    allow {
      values = ["C0123abcd"] # The customer ID for your corp_domain
    }
  }
}
```