---
title: "GCP Governance: Organization Policies"
date: 2026-08-22
tags: [landing-zone, gcp, governance, security, org-policy]
summary: "A deep dive into using Organization Policies to enforce security and governance guardrails."
---

# GCP Governance: Organization Policies

## Summary

The Organization Policy Service gives you centralized, programmatic control over your organization's cloud resources. It allows you to define and enforce constraints on how specific services can be used. In an interview, a deep understanding of Organization Policies shows you can build a secure, compliant, and well-governed cloud environment from the top down.

## Context

While IAM policies answer "who can do what," Organization Policies answer "what is allowed?" They are the primary tool for enforcing broad guardrails across your entire resource hierarchy. For example, you can use an Organization Policy to ensure that no resources are ever created outside of approved geographic regions, regardless of a user's IAM permissions.

## Architecture / Components

- **Constraint**: The definition of a specific restriction. Constraints are defined by Google and target a specific service or resource (e.g., `constraints/compute.vmExternalIpAccess`).
- **Policy**: The implementation of a constraint on a specific resource in your hierarchy (Organization, Folder, or Project). The policy specifies the desired behavior (e.g., `allow` or `deny` a list of values, or `enforce: true`).
- **Policy Types**:
  - **List Constraints**: These check a resource's property against a list of allowed or denied values (e.g., `gcp.resourceLocations`).
  - **Boolean Constraints**: These enforce or disable a specific behavior (e.g., `iam.disableServiceAccountKeyCreation`).

## Best Practices

- **Apply High, Inherit Down**: Set policies at the highest level possible (Organization or top-level folders) to ensure consistent enforcement. Policies are inherited by all child resources.
- **Use "Dry Run" Mode**: Before enforcing a new, restrictive policy, you can set it in "dry run" mode. This will log any violations in Cloud Audit Logs without actually blocking the action, allowing you to assess the impact first.
- **Start with Critical Guardrails**: Don't try to implement every possible policy at once. Start with a core set of security and cost-control policies.

## Common & Important Constraints for a Landing Zone

1.  **`constraints/gcp.resourceLocations`**
    - **What it does**: Restricts the physical location where resources can be created.
    - **Use Case**: Essential for data residency and compliance requirements (e.g., ensuring all data stays within the EU).

2.  **`constraints/compute.vmExternalIpAccess`**
    - **What it does**: Prevents VMs from being created with public IP addresses.
    - **Use Case**: A critical security guardrail. It forces developers to use secure ingress methods like Load Balancers or IAP instead of exposing VMs directly to the internet.

3.  **`constraints/iam.allowedPolicyMemberDomains`**
    - **What it does**: Restricts which Google Cloud Identity or Google Workspace customer IDs can be added to IAM policies.
    - **Use Case**: Prevents users from accidentally granting access to personal accounts (e.g., `@gmail.com`) or users outside your organization.

4.  **`constraints/storage.uniformBucketLevelAccess`**
    - **What it does**: Enforces uniform bucket-level access for all Cloud Storage buckets, disabling legacy object-level ACLs.
    - **Use Case**: Simplifies and strengthens storage permissions by relying solely on IAM, which is easier to manage and audit.

## Common Interview Questions

- Q1: How would you prevent any developer in your organization from creating a VM with a public IP address?
- Q2: A company must ensure that no data is ever stored outside of North America. How would you enforce this?
- Q3: What is the difference between an IAM policy and an Organization Policy?

## References
- Organization Policy Constraints: https://cloud.google.com/resource-manager/docs/organization-policy/org-policy-constraints