# Terraform: Enabling Project Services (APIs)

This snippet shows how to enable a list of required Google Cloud services (APIs) for a project using Terraform. This is a critical step in any project factory automation to ensure that developers can use the services they need immediately after project creation.

## Terraform Snippet

```terraform
variable "project_id" {
  description = "The ID of the project to enable APIs on."
  type        = string
}

variable "apis_to_enable" {
  description = "A list of APIs to enable on the project."
  type        = list(string)
  default = [
    "compute.googleapis.com",
    "storage.googleapis.com",
    "run.googleapis.com",
    "iam.googleapis.com",
    "cloudresourcemanager.googleapis.com",
    "secretmanager.googleapis.com",
  ]
}

resource "google_project_service" "enabled_apis" {
  for_each = toset(var.apis_to_enable)

  project                    = var.project_id
  service                    = each.key
  disable_on_destroy         = false
  disable_dependent_services = false
}
```

### Key Concepts
- **`google_project_service`**: The Terraform resource responsible for managing the state of an API on a project.
- **`for_each`**: Using a `for_each` loop over a list of APIs makes the code scalable and easy to manage. You can simply add or remove API identifiers from the `apis_to_enable` variable.
- **`disable_on_destroy = false`**: This is a crucial safety setting. It prevents Terraform from accidentally disabling an API if the resource is removed from your code. Disabling an in-use API can cause a major outage, so this setting should almost always be `false` in production environments.