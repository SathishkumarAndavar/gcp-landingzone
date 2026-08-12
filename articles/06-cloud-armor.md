---
title: "GCP Security: Cloud Armor"
date: 2026-08-15
tags: [landing-zone, gcp, security, networking, cloud-armor]
summary: "Using Cloud Armor for WAF and DDoS protection at the edge of your GCP environment."
---

# GCP Security: Cloud Armor

## Summary

Google Cloud Armor is a network security service that provides defense against Distributed Denial-of-Service (DDoS) attacks and offers a Web Application Firewall (WAF) to protect applications from common web-based threats. In an interview, discussing Cloud Armor shows you know how to secure applications at the edge, which is a critical part of a landing zone's security posture.

## Context

In a landing zone, applications are often exposed to the internet via a Global External Load Balancer. Cloud Armor integrates directly with the load balancer to inspect traffic before it reaches your backend services. It's a key component of the "Secure" theme, providing a first line of defense against malicious external traffic.

## Architecture / Components

- **Security Policies**: The core component of Cloud Armor. A policy is a set of rules that are evaluated in order of priority.
- **Rules**: Define the logic for allowing, denying, or throttling traffic based on attributes like IP address, region code, request headers, and more.
- **Pre-configured WAF Rules**: Google-curated rulesets that map to common web attack vectors like the OWASP Top 10 (e.g., SQL injection, Cross-Site Scripting).
- **Backend Services**: Cloud Armor policies are attached to backend services that are used by an external HTTP(S) load balancer.
- **Managed Protection Plus**: A subscription tier that provides advanced DDoS protection, telemetry, and access to Google's DDoS response team.

## Best Practices

- **Attach to Load Balancers**: Always place Cloud Armor policies on internet-facing load balancers.
- **Start in Preview Mode**: Deploy new rules in "preview" mode to log potential actions without actually blocking traffic. This helps validate rules before enforcement.
- **Use Pre-configured Rules**: Enable the pre-configured WAF rules to get immediate protection against common vulnerabilities.
- **Integrate with Logging**: Monitor Cloud Armor logs in Cloud Logging to analyze traffic patterns and tune security rules.

## Common Interview Questions

- Q1: How would you protect a web application hosted on GKE from SQL injection attacks?
- Q2: What is the difference between a GCP Firewall Rule and a Cloud Armor policy? (Firewall rules operate at L3/L4 within the VPC; Cloud Armor operates at L7 at the edge).
- Q3: How can you test a new, potentially restrictive Cloud Armor rule without causing an outage?

## References
- Cloud Armor Documentation: https://cloud.google.com/armor