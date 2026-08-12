---
title: "Workload Identity Federation (WIF)"
date: 2026-08-13
tags: [landing-zone, gcp, iam, security, wif]
summary: "Using Workload Identity Federation to grant external workloads access to GCP resources without service account keys."
---

# Workload Identity Federation (WIF)

## Summary

Workload Identity Federation (WIF) allows you to grant identities from an external identity provider (IdP) — such as AWS, Azure, Okta, or any OIDC/SAML 2.0 provider — access to your GCP resources. This is done without needing to create and manage service account keys, which significantly improves security posture. For interviews, this shows you know modern, secure access patterns.

## Context

In a landing zone, workloads often run outside of GCP (e.g., in on-premises data centers, other clouds, or CI/CD systems like GitHub Actions). These workloads need to access GCP APIs. The traditional, less secure method was to use long-lived service account keys. WIF provides a secure, keyless alternative by exchanging a trusted external token for a short-lived GCP access token.

## Architecture / Components

- **Workload Identity Pool**: A GCP entity that represents a group of external identities.
- **Workload Identity Provider**: A configuration within a pool that specifies the details of the external IdP (e.g., AWS account ID, OIDC issuer URI).
- **Attribute Mapping**: Rules that map assertions from the external credential (like the repository name from a GitHub token) to attributes on the GCP principal. This allows for fine-grained access control.
- **IAM Principal Identifier**: The format used in IAM policies to grant roles to federated identities: `principalSet://iam.googleapis.com/projects/<project-number>/locations/global/workloadIdentityPools/<pool-id>/<subject-attribute-value>`

## Best Practices

- **Prefer WIF over Service Account Keys**: Always use WIF where possible to eliminate key management overhead and risk.
- **Use Granular Attribute Conditions**: Restrict which external identities can impersonate a service account. For example, only allow a specific GitHub repository (`assertion.repository`) to access a specific service account.
- **One Pool per Environment/Trust Boundary**: Create separate pools for different trust domains (e.g., dev vs. prod, GitHub vs. on-prem).

## Common Interview Questions

- Q1: How would you give a CI/CD pipeline running in GitHub Actions access to deploy to Cloud Run?
- Q2: What are the security benefits of using Workload Identity Federation instead of service account keys?
- Q3: Explain the relationship between a Workload Identity Pool, a provider, and a service account.

## References

- Workload Identity Federation Docs: https://cloud.google.com/iam/docs/workload-identity-federation