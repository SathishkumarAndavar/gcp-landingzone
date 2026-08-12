# Terraform: Centralized Log Sink

This snippet shows how to create an aggregated log sink at the organization or folder level. This is a best practice for centralizing all audit and application logs into a single destination (like BigQuery or Cloud Storage) for security analysis, compliance, and long-term retention.

## Terraform Snippet

```terraform
variable "log_sink_parent_id" {
  description = "The ID of the parent resource (organization or folder) for the sink."
  type        = string
  # Example: "organizations/123456789012" or "folders/234567890123"
}

variable "log_destination_project_id" {
  description = "The project ID where the BigQuery dataset for logging exists."
  type        = string
}

# Create a log sink at the organization/folder level
resource "google_logging_organization_sink" "central_audit_sink" {
  name   = "central-audit-log-sink"
  parent = var.log_sink_parent_id

  # Destination: A BigQuery dataset in a central logging project
  destination = "bigquery.googleapis.com/projects/${var.log_destination_project_id}/datasets/audit_logs"

  # Filter: Export all admin activity and data access audit logs
  filter = "logName:\"/logs/cloudaudit.googleapis.com%2Factivity\" OR logName:\"/logs/cloudaudit.googleapis.com%2Fdata_access\""

  # Include logs from all child projects and folders
  include_children = true
}
```

### Key Concepts
- **`google_logging_organization_sink`**: The resource for creating a sink at a high level in the resource hierarchy. A similar resource exists for folders (`google_logging_folder_sink`).
- **`destination`**: The target for the logs. This should be a resource in a dedicated, locked-down logging project.
- **`filter`**: Specifies which logs to export. Filtering for audit logs is a common security requirement.
- **`include_children = true`**: This is the key property that makes the sink "aggregated," as it automatically collects logs from all resources created under the parent.