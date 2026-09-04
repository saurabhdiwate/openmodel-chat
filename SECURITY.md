# Security Policy

## Supported Versions

| Version | Supported |
|---------|-----------|
| 0.2.x   | Yes       |

Security fixes are applied to the latest release on the `main` branch.

## Reporting a Vulnerability

If you discover a security vulnerability in OpenModel Chat, please report it responsibly:

1. **Do NOT** open a public GitHub issue for security vulnerabilities.
2. Use [GitHub's private vulnerability reporting](https://github.com/saurabhdiwate/openmodel-chat/security/advisories/new) to submit a report.
3. Include a description of the vulnerability, steps to reproduce, and potential impact.
4. Allow reasonable time for a fix before any public disclosure.

We will acknowledge receipt within 72 hours and provide an estimated timeline for a fix.

## Security Architecture

OpenModel Chat implements the following security controls:

### Authentication & Authorization
- **Argon2** password hashing (memory-hard, resistant to GPU/ASIC attacks)
- **Session-based authentication** via HTTP-only cookies
- **Role-based access control** (RBAC) with three roles: `admin`, `member`, `readonly`
- First registered user automatically becomes admin; subsequent registration is disabled

### API Security
- **Zod validation** at every API boundary — no untrusted input reaches the database
- **Rate limiting** on authentication endpoints (5 failed attempts per window)
- **CORS policy** restricting origins in production
- **Security headers** (X-Content-Type-Options, X-Frame-Options, CSP, Referrer-Policy, Permissions-Policy)
- **Request body size limits** (50 MB max)

### Data Protection
- **AES-256-GCM encryption** for stored provider API keys
- API keys are never returned in plaintext; masked versions shown in admin UI
- **Session cleanup** runs periodically to remove expired sessions

### Network Security
- **SSRF protection** — DNS-level validation and private-IP blocking on all outbound URL fetching
- Cloud metadata endpoints (169.254.169.254, etc.) are explicitly blocked
- Provider base URLs validated against private network ranges

### File Upload Security
- Dangerous file types rejected at upload: SVG/XSS, executables, legacy Office formats
- File size limits enforced
- Filename sanitization (path traversal, null bytes, leading dots)
- Content-Disposition headers set appropriately (attachment for risky types)
- Image dimension validation to prevent decompression bombs

### Database
- SQLite with WAL mode in production
- Foreign key constraints enforced
- Parameterized queries throughout (no SQL string concatenation)

## Secret Handling

- **Never commit** API keys, encryption keys, or credentials to the repository.
- Production secrets must be supplied via environment variables or a secrets manager.
- The `API_KEY_ENCRYPTION_KEY` is auto-generated on first run if not provided.
- See `server/.env.example` for all required and optional configuration keys.

## Scope

This policy covers the OpenModel Chat application code in this repository. It does not cover:
- Third-party AI provider APIs
- Deployment platform security (Fly.io, Render, Railway)
- User-managed infrastructure
