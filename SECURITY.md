# Security Policy

## Reporting Security Vulnerabilities

If you discover a security vulnerability, **please do not open a public issue**. Instead:

1. Email security concerns to the maintainer
2. Include details about the vulnerability
3. Allow time for a patch before public disclosure

## Security Practices

### Secrets Management

- **Never commit secrets** to the repository
- Use GitHub Actions secrets for sensitive data
- Use Ansible vault for local secret storage
- All examples use placeholder variables (e.g., `{{ vault_* }}`)

### Authentication

- GitHub runner tokens use fine-grained PATs with minimal scopes
- Proxmox API tokens are stored in secrets, never in code
- SSH keys are managed externally (not in repo)

### Code Review

- All PRs require review before merge
- Security-related changes have additional scrutiny
- Dependencies are kept current (Dependabot enabled)

## Compliance

This project targets IEC 62443 ML4 compliance and follows secure SDLC principles:
- All changes tracked in git with audit trail
- Reproducible builds via pinned versions
- SPDX headers on all source files

For more details, see the RUNE platform's [security documentation](https://github.com/lpasquali/rune-docs).
