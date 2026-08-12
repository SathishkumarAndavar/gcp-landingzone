---
title: "GCP Networking Patterns for Landing Zones"
date: 2026-08-14
tags: [landing-zone, gcp, networking, vpc, architecture]
summary: "Comparing key GCP networking patterns like Shared VPC, VPC Peering, and Private Service Connect for landing zone design."
---

# GCP Networking Patterns for Landing Zones

## Summary

Choosing the right networking model is fundamental to a landing zone's success, impacting security, scalability, and cost. For interviews, you must be able to articulate the differences between Shared VPC, VPC Peering, and Private Service Connect, and justify when to use each.

## Context

In a landing zone, you need to connect various projects and services securely and efficiently. A centralized networking model simplifies management and security policy enforcement, while a decentralized model can offer more autonomy. The choice depends on the organization's structure and governance requirements.

## Core Patterns & Comparison

### 1. Shared VPC
- **What it is**: A central host project owns the VPC network, and service projects can use subnets from that network. This is the most common pattern for enterprise landing zones.
- **Pros**: Centralized control over firewall rules, routes, and gateways. Simplified network administration. IAM controls at the subnet level provide granular permissions.
- **Cons**: Tightly coupled. Quotas and limits are shared across all service projects. Requires a dedicated network administration team.
- **Use Case**: The default choice for creating a centrally managed network foundation where different teams deploy workloads into separate service projects.

### 2. VPC Network Peering
- **What it is**: Connects two VPC networks, allowing private RFC 1918 communication between them. The networks remain administratively separate.
- **Pros**: Decentralized administration. No shared quotas. Good for connecting networks between different business units or organizations.
- **Cons**: Non-transitive (if A is peered with B, and B with C, A cannot talk to C). Can become complex to manage at scale due to peering mesh complexity. Each VPC maintains its own firewall rules.
- **Use Case**: Connecting a workload VPC to a partner's VPC, or linking two distinct environments that should not be part of the same Shared VPC.

### 3. Private Service Connect (PSC)
- **What it is**: A mechanism that allows a service *consumer* to privately access a managed service in a service *provider's* VPC. The consumer creates a local endpoint (a forwarding rule) in their own VPC that routes traffic to the service.
- **Pros**:
  - **Complete Decoupling**: The consumer and provider VPCs are completely isolated. They do not exchange routes, and their IP address spaces can overlap without issue. This is a major advantage over VPC Peering.
  - **One-Way Access**: It provides a secure, one-way "service injection" model. The consumer can access the service, but the service provider cannot initiate connections back into the consumer's network.
  - **Simplified Management**: The service provider exposes a single "Service Attachment," and any number of consumers can connect to it without complex network configurations on the provider's side.
- **Cons**: More complex to set up than peering for simple use cases. Primarily for a one-way service exposure model.
- **Use Case**: Securely exposing a managed service (like a database or an internal API) from a central services project to multiple consumer projects without using peering or external IPs.

## Common Interview Questions

- Q1: When would you choose Shared VPC over multiple peered VPCs for a new landing zone?
- Q2: You have a central project running a managed database. How would you allow 10 different application teams in their own projects to access it without exposing their networks to each other? (Answer: Private Service Connect).
- Q3: What are the limitations of VPC Peering, and how can they affect a large-scale network design?

## References
- Shared VPC Overview: https://cloud.google.com/vpc/docs/shared-vpc
- Private Service Connect: https://cloud.google.com/vpc/docs/private-service-connect