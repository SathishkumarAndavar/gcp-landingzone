---
title: "API Management with Apigee"
date: 2026-08-15
tags: [landing-zone, gcp, apigee, api, networking]
summary: "Setting up Apigee for secure API management within a GCP landing zone."
---

# API Management with Apigee

## Summary

Apigee is Google Cloud's platform for full lifecycle API management. It allows organizations to design, secure, analyze, and scale APIs. In an interview, discussing Apigee demonstrates your understanding of how to manage and govern API-driven architectures, which are common in modern enterprises.

## Context

In a landing zone, Apigee typically resides in a central shared services project. It acts as a secure gateway or facade for backend services running in various workload projects. This decouples API consumers from backend implementations and provides a single point for applying security policies, traffic management, and analytics.

## Architecture / Components

- **Apigee X**: The fully managed version of Apigee, which is the recommended choice. It requires a VPC network and uses VPC Peering for its service networking.
- **Environments**: Runtime execution contexts for API proxies (e.g., `dev`, `test`, `prod`).
- **API Proxies**: The core of Apigee. They sit in front of your backend services and enforce policies like security (OAuth2, API Keys), traffic management (rate limiting, quotas), and mediation (request/response transformation).
- **API Products**: Bundles of API resources that are exposed to developers. This is how you control access to your APIs.
- **Developer Portal**: A customizable portal for developers to discover, learn about, and get keys for your APIs.
- **Networking**: Apigee X peers with a VPC in your project. You expose the Apigee runtime to consumers via a Global External Load Balancer, which should be protected by Cloud Armor.

## Best Practices

- **Use Apigee X**: Prefer the managed service to offload infrastructure management.
- **Centralize in a Shared Project**: Deploy Apigee in a dedicated project to serve multiple backend application teams.
- **Secure the Endpoint**: Place Cloud Armor in front of the load balancer that exposes your Apigee instance to protect against WAF and DDoS attacks.
- **Govern with API Products**: Don't give direct access to proxies. Use API Products to control which developer apps can access which APIs.

## Common Interview Questions

- Q1: How would you expose a set of internal microservices to external partners in a secure and scalable way?
- Q2: What is the role of Apigee in a landing zone compared to a simple load balancer?
- Q3: Explain the relationship between an Apigee API Proxy, an API Product, and a Developer App.

## References
- Apigee Documentation: https://cloud.google.com/apigee/docs