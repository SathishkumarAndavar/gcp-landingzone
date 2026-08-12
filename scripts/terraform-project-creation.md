# Terraform: Project Creation

This snippet provides the foundational Terraform code for creating a new GCP project. It includes assigning it to a folder and linking a billing account, which are essential steps in a project factory.

## Terraform Snippet

```terraform
variable "folder_id" {
  description = "The ID of the folder to create the project in."
  type        = string
  # Example: "folders/123456789012"
}

variable "billing_account_id" {
  description = "The ID of the billing account to associate with the project."
  type        = string
  # Example: "01A2B3-C4D5E6-F7G8H9"
}

variable "project_name_prefix" {
  description = "A prefix for the new project's name."
  type        = string
  default     = "my-app"
}

# Use a random suffix to ensure the project ID is globally unique
resource "random_id" "project_id_suffix" {
  byte_length = 4
}

# Create the GCP Project
resource "google_project" "new_project" {
  name            = "${var.project_name_prefix}-project"
  project_id      = "${var.project_name_prefix}-${random_id.project_id_suffix.hex}"
  folder_id       = var.folder_id
  billing_account = var.billing_account_id
}
```

### Key Concepts
- **`google_project`**: The resource that creates and manages a GCP project.
- **`random_id`**: A helper resource from the `random` provider that generates a random string. This is a best practice to avoid collisions with globally unique project IDs.
- **`folder_id`**: Placing projects into a folder hierarchy is critical for applying policies and organizing resources at scale.
- **`billing_account`**: A project must be linked to a billing account to enable paid APIs and services.