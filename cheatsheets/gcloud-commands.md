# Cheatsheet: Common gcloud CLI Commands

A quick reference for useful `gcloud` commands related to landing zone management and inspection.

## 1. Configuration and Authentication

```bash
# See your current configuration (account, project, region)
gcloud config list

# Log in with your user account
gcloud auth login

# Set your default project for subsequent commands
gcloud config set project [PROJECT_ID]
```

## 2. Resource Hierarchy

```bash
# List projects within a specific folder
gcloud projects list --filter="parent.id=[FOLDER_ID] AND parent.type=folder"

# Describe an organization's details
gcloud organizations describe [ORGANIZATION_ID]
```

## 3. IAM and Policies

```bash
# Show the IAM policy for a project
gcloud projects get-iam-policy [PROJECT_ID]

# Show the organization policy for a given constraint on a project
gcloud resource-manager org-policies describe [CONSTRAINT_NAME] --project=[PROJECT_ID]
# Example: gcloud resource-manager org-policies describe iam.allowedPolicyMemberDomains --project=my-project
```

## 4. Networking

```bash
# List VPC networks in the current project
gcloud compute networks list

# List subnets in a VPC network
gcloud compute networks subnets list --network=[VPC_NAME]
```