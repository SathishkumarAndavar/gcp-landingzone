---
title: "GCP Landing Zone — Overview"
date: 2026-08-12
tags: [landing-zone, gcp, architecture]
summary: "Core concepts and components of a GCP landing zone for interview prep."
---

# GCP Landing Zone — Overview

## Summary

A GCP landing zone is a prescriptive environment that provides an initial, secure, and scalable foundation for workloads across an organization.

## Core Components

- Organization structure and folders
- Projects pattern (infra, shared services, workloads, security)
- Identity and Access Management (IAM) and groups
- Organization Policies and constraints
- Billing structure and billing accounts
- Network design: VPCs, subnets, Shared VPC, Cloud NAT
- Security: SCC, Cloud Armor, logging, audit sinks
- CI/CD and infrastructure as code (Terraform, Cloud Build)
- Monitoring and incident response (Stackdriver/Cloud Monitoring)

## Best Practices

- Use least-privilege IAM and group-based roles
- Enforce Org Policies for constraints (e.g., allowed regions)
- Separate projects for isolation and billing
- Use Shared VPC for central network management
- Automate with Terraform and use a remote state backend

## Interview Questions

- How would you design projects and folders for a global org?
- When to use Shared VPC vs service project VPCs?
- How do Org Policies help in a landing zone?
- Describe the IAM model you'd recommend for platform engineers.

## References

- https://cloud.google.com/architecture
- https://cloud.google.com/architecture/cloud-foundations
