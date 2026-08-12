# Terraform: Attaching a Service Project to a Shared VPC

This snippet shows how to attach a newly created service project to a central Shared VPC host project. This allows resources in the service project to use the network resources from the host project.

## Terraform Snippet

```terraform
variable "host_project_id" {
  description = "The ID of the Shared VPC host project."
  type        = string
}

variable "service_project_id" {
  description = "The ID of the service project to attach."
  type        = string
}

# 1. Enable the Shared VPC host project (if not already enabled)
# This only needs to be done once on the host project.
resource "google_compute_shared_vpc_host_project" "host" {
  project = var.host_project_id
}

# 2. Attach the service project to the host project.
# This resource depends on the host being enabled first.
resource "google_compute_shared_vpc_service_project" "service" {
  host_project    = google_compute_shared_vpc_host_project.host.project
  service_project = var.service_project_id
}
```

### Key Concepts
- **`google_compute_shared_vpc_host_project`**: Designates a project as a Shared VPC host.
- **`google_compute_shared_vpc_service_project`**: Attaches a service project to the host, creating the link that allows network sharing. This is a fundamental pattern for centralized network governance in a landing zone.