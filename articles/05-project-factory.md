---
title: "Automating with a Project Factory"
date: 2026-08-14
tags: [landing-zone, gcp, terraform, automation, iac]
summary: "Using a project factory to automate the creation of secure and compliant GCP projects at scale."
---

# Automating with a Project Factory

## Summary

A project factory is an automated, template-driven process for vending new GCP projects. It ensures that every new project is created with the correct baseline configuration, security policies, networking, and IAM permissions from day one. In an interview, this topic shows you can think about governance and efficiency at an enterprise scale.

## Context

As an organization grows, manually creating and configuring projects becomes slow, error-prone, and inconsistent. A project factory, typically implemented as an Infrastructure-as-Code (IaC) pipeline, solves this by treating project creation as a managed service for development teams. It's a core component of the "Scale" theme in the Cloud Adoption Framework.

## Architecture / Components

- **IaC Module**: A reusable Terraform (or other IaC tool) module that defines the standard project configuration. This includes:
  - Attaching to a billing account.
  - Placing the project in the correct folder.
  - Enabling necessary APIs (e.g., `compute.googleapis.com`, `run.googleapis.com`). This is a critical step to ensure developers can use services without hitting permission errors.
  - Attaching to a Shared VPC network (if applicable).
  - Applying baseline IAM permissions for project owners.
  - Applying labels for cost tracking and automation.
- **CI/CD Pipeline**: A pipeline (e.g., using Cloud Build, GitHub Actions, or Jenkins) that invokes the IaC module. It can be triggered by a Git commit, an API call, or a service catalog request.
- **Source of Truth**: A Git repository is typically used to store the desired state of all projects. A new project is requested by submitting a pull request that adds a new configuration file or entry.

## Best Practices

- **Use a Centralized Module**: Maintain a single, versioned Terraform module for project creation to ensure consistency.
- **GitOps for Control**: Use a Git-based workflow with pull requests for auditing and approval of new projects.
- **Parameterize for Flexibility**: Allow requesters to specify parameters like project name and cost center labels, but enforce non-negotiable settings like folder placement and network attachment.
- **Create Project Templates**: Offer different "flavors" of projects with different sets of APIs enabled by default (e.g., a "data-science" template vs. a "web-app" template) to provide teams with what they need out of the box.

## Common Interview Questions

- Q1: How would you enable hundreds of developers to create new GCP projects without giving them `resourcemanager.projectCreator` permissions at the org level?
- Q2: What key components would you include in a Terraform module for a project factory?
- Q3: Describe the CI/CD workflow for provisioning a new project using a project factory.
- Q4: Why is managing API enablement as part of a project factory important?