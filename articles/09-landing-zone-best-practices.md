---
title: "GCP Landing Zone: Consolidated Best Practices"
date: 2026-08-18
tags: [landing-zone, gcp, architecture, best-practices, security, networking]
summary: "A consolidated list of best practices for designing, building, and operating a secure and scalable GCP Landing Zone."
---

# GCP Landing Zone: Consolidated Best Practices

This guide summarizes the most critical best practices for a GCP Landing Zone, covering governance, security, networking, and automation. In an interview, being able to articulate these principles demonstrates a mature understanding of cloud platform design.

## 1. Governance and Resource Hierarchy

- **Use a Scalable Folder Structure**: Design your folder hierarchy to match your organizational structure (e.g., by business unit, environment, or application). A common pattern is `Organization -> Environments (prod/dev) -> Business Units -> Applications`.
- **Isolate Environments**: Use separate folders or projects for production, development, and testing environments to provide strong isolation boundaries for security and policy enforcement.
- **Centralize Shared Services**: Create dedicated projects for shared resources like networking (Shared VPC host), security tools (log sinks, SCC), and CI/CD infrastructure.
- **Enforce Guardrails with Organization Policies**: Use Organization Policies to programmatically enforce security and governance constraints across your entire resource hierarchy (e.g., restrict resource locations, disable public IPs, enforce uniform bucket-level access).

## 2. Identity and Access Management (IAM)

- **Follow the Principle of Least Privilege**: Grant roles at the most specific resource level possible (e.g., on a specific Cloud Storage bucket rather than the whole project).
- **Use Groups for Permissions**: Always assign IAM roles to Google Groups, not individual user accounts. Managing group membership is far simpler and less error-prone than managing hundreds of individual IAM bindings.
- **Prefer Predefined Roles over Primitive Roles**: Avoid using primitive roles (`owner`, `editor`, `viewer`). Use granular, predefined roles (e.g., `roles/compute.instanceAdmin`) or create custom roles if a predefined one doesn't fit the need.
- **Eliminate Service Account Keys with WIF**: Use **Workload Identity Federation (WIF)** to allow external systems (like GitHub Actions, on-prem servers, or other clouds) to access GCP resources without long-lived service account keys.

## 3. Networking and Connectivity

- **Centralize Network Control with Shared VPC**: For most enterprise use cases, use a Shared VPC model. This allows a central network team to manage VPCs, subnets, firewall rules, and connectivity in a host project, while application teams consume the network in service projects.
- **Choose the Right Connectivity Model**: Use **Cloud Interconnect** (Partner or Dedicated) for reliable, high-bandwidth hybrid connectivity. Use **HA VPN** for lower-bandwidth needs or as a backup to Interconnect.
- **Use Private Service Connect (PSC) for Service Exposure**: To securely expose a managed service from one VPC to another without IP overlap or peering, use PSC. It's the modern standard for private service consumption.

## 4. Security

- **Implement Defense in Depth**: Layer security controls. Use **Cloud Armor** at the edge (for WAF/DDoS), VPC firewall rules for internal segmentation, and **VPC Service Controls** to create data exfiltration perimeters.
- **Centralize Logging and Auditing**: Create an aggregated **Log Sink** at the organization or folder level to route all audit logs and critical service logs to a central, immutable destination like BigQuery or Cloud Storage for analysis and retention.
- **Manage Secrets Securely**: Use **Cloud Secret Manager** to store, manage, and rotate secrets like API keys, passwords, and certificates. Grant IAM permissions to specific principals to access specific secrets.

## 5. Automation and Operations (Infrastructure as Code)

- **Automate with a Project Factory**: Implement a **Project Factory** using Terraform and a CI/CD pipeline to automate the creation of new, compliant GCP projects. This ensures every project starts with the correct billing, folder placement, networking, and API enablement.
- **Manage Everything as Code (IaC)**: Use Terraform to manage all aspects of your landing zone, from organization policies and folders down to firewall rules and IAM bindings. Store your code in a Git repository as the single source of truth.
- **Enable APIs as Part of Provisioning**: Don't let developers enable APIs manually. Include the `google_project_service` resource in your project factory to enable a baseline set of APIs by default.