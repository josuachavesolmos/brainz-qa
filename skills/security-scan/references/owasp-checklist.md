# OWASP Top 10 Checklist for BrainzLab

Patterns to scan for in Rails and Node.js codebases.

## A01: Broken Access Control

### Rails
- Missing `before_action :authenticate_user!` on controllers
- `skip_before_action :verify_authenticity_token` without justification
- Direct object references without authorization: `Model.find(params[:id])` without Pundit/CanCanCan
- Exposed admin routes without role checks

### Node.js
- Missing auth middleware on routes
- JWT tokens without expiration
- Client-side only authorization checks

## A02: Cryptographic Failures

- Hardcoded secrets in source code
- `ENV` variables with default secret values
- HTTP URLs for API calls (should be HTTPS)
- Weak hashing algorithms (MD5, SHA1 for passwords)
- Missing `force_ssl` in production Rails config

## A03: Injection

### SQL Injection
- Rails: `where("name = '#{params[:name]}'")` — use `where(name: params[:name])` instead
- Rails: `Model.find_by_sql` with interpolated strings
- Node: string concatenation in SQL queries

### Command Injection
- `system()`, backticks, `exec()` with user input
- `child_process.exec()` with unsanitized input

### XSS
- Rails: `raw()`, `html_safe`, `<%== %>` on user input
- React: `dangerouslySetInnerHTML` with unsanitized content
- Missing Content-Security-Policy headers

## A04: Insecure Design

- Missing rate limiting on auth endpoints
- No account lockout after failed attempts
- Password reset tokens that don't expire
- Missing CAPTCHA on public forms

## A05: Security Misconfiguration

- `Rails.application.config.consider_all_requests_local = true` in production
- Debug mode enabled in production
- Default credentials in seed files
- Exposed `.env` files or config with secrets
- Missing security headers (X-Frame-Options, X-Content-Type-Options)

## A06: Vulnerable Components

- Dependencies with known CVEs (run `npm audit` / `bundle audit`)
- Outdated framework versions
- Abandoned dependencies (no updates in 2+ years)

## A07: Authentication Failures

- Session tokens in URL parameters
- Missing session expiration
- Passwords stored in plaintext or weak hashes
- Missing multi-factor authentication on admin accounts

## A08: Data Integrity Failures

- `YAML.load` instead of `YAML.safe_load`
- `Marshal.load` on untrusted data
- `eval()` or `instance_eval` with user input
- Deserializing untrusted JSON without schema validation

## A09: Logging & Monitoring Failures

- Sensitive data in logs (passwords, tokens, PII)
- Missing audit logs for admin actions
- No alerting on authentication failures

## A10: Server-Side Request Forgery (SSRF)

- User-provided URLs passed to `open-uri`, `Net::HTTP`, `fetch()`, `axios`
- Webhook URLs without allowlist validation
- Image/file download from user-provided URLs without domain filtering
