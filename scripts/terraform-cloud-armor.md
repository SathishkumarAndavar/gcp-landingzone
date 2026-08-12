# Terraform: Basic Cloud Armor Security Policy

This snippet shows how to create a Google Cloud Armor security policy. This policy can be attached to a backend service behind an external load balancer to provide WAF and DDoS protection.

## Terraform Snippet

```terraform
resource "google_compute_security_policy" "armor_policy" {
  project     = "your-gcp-project-id"
  name        = "webapp-security-policy"
  description = "Standard WAF and geo-blocking policy"

  # Rule 1: Block traffic from a specific country (e.g., for compliance)
  rule {
    action   = "deny(403)"
    priority = 1000
    match {
      expr {
        expression = "origin.region_code == 'CU'" # Block traffic from Cuba
      }
    }
    description = "Block traffic from sanctioned regions"
  }

  # Rule 2: Apply Google's pre-configured WAF rules for SQL Injection
  rule {
    action   = "deny(403)"
    priority = 1100
    match {
      expr {
        # Use Google's pre-configured expression set for SQLi
        expression = "evaluatePreconfiguredExpr('sqli-stable')"
      }
    }
    description = "OWASP Top 10: SQL Injection Protection"
  }
}
```

### Key Concepts
- **`google_compute_security_policy`**: The main resource for a Cloud Armor policy.
- **`rule`**: A block that defines a match condition and an action (e.g., `allow`, `deny(status_code)`). Rules are evaluated in order of `priority` (lower number = higher priority).
- **`evaluatePreconfiguredExpr(...)`**: A powerful function that leverages Google-managed rulesets. This is a best practice for getting broad protection against common attacks (like `xss-stable`, `lfi-stable`, etc.) without writing complex expressions yourself.