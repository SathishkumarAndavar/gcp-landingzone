---
title: "GCP Networking: Cloud NAT"
date: 2026-08-21
tags: [landing-zone, gcp, networking, cloud-nat, security]
summary: "Providing secure outbound internet connectivity for private resources using Cloud NAT."
---

# GCP Networking: Cloud NAT

## Summary

Cloud NAT (Network Address Translation) is a managed GCP service that allows resources in a private subnet (e.g., VMs, GKE nodes, Cloud Run instances without external IPs) to access the internet for tasks like downloading updates, pulling container images, or calling external APIs. It does this without requiring the resources to have their own public IP addresses, which is a critical security best practice.

## Context

In a secure landing zone, most compute resources should not be directly exposed to the internet. However, they often need to initiate outbound connections. Cloud NAT provides a secure, scalable, and highly available gateway for this outbound traffic, acting on behalf of your private resources.

## Architecture / Components

- **NAT Gateway**: The core managed resource that performs the network address translation. It is configured on a Cloud Router.
- **Cloud Router**: A Cloud NAT gateway is associated with a Cloud Router in the same region and VPC. The router itself doesn't route the NAT traffic but serves as a control plane anchor for the gateway.
- **IP Address Allocation**:
  - **Automatic (Dynamic)**: Cloud NAT automatically allocates and scales the number of ephemeral public IP addresses based on the number of VMs using the gateway.
  - **Manual (Static)**: You can reserve a set of static public IP addresses and assign them to the NAT gateway. This is essential when you need a predictable set of source IPs to be whitelisted by an external service or partner.
- **Port Allocation**: Cloud NAT allocates source ports for each VM to handle concurrent connections. Proper monitoring of port usage is important to avoid exhaustion in high-traffic scenarios.
- **NAT Logging**: When enabled, Cloud NAT can log successful translations and errors to Cloud Logging, which is vital for security auditing and troubleshooting.

## Best Practices

- **Use Static IPs for Whitelisting**: If your applications need to connect to external services that use IP whitelisting, configure your NAT gateway with a reserved set of static IP addresses.
- **Enable Logging**: Always enable NAT logging to have a clear audit trail of outbound connections from your private network.
- **Monitor Port Usage**: For large-scale deployments, monitor NAT port usage in Cloud Monitoring to prevent port exhaustion, which can lead to outbound connectivity failures.
- **Regional by Design**: A Cloud NAT gateway is a regional resource. It can serve all subnets in its region. For multi-region deployments, you must deploy a NAT gateway in each region.

## Common Interview Questions

- Q1: A VM in a private subnet needs to download a security patch from a repository on the internet. How do you enable this without assigning a public IP to the VM?
- Q2: An application needs to call a partner's API, and the partner requires a list of all possible source IP addresses to add to their firewall allowlist. How do you solve this?
- Q3: What are the main differences between Cloud NAT and a self-managed NAT instance on a VM? (Answer: Cloud NAT is a managed service, highly available, scalable, and requires no software maintenance, unlike a self-managed instance).