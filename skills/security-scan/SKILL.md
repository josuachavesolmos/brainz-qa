---
name: security-scan
description: >
  Scan for security vulnerabilities in dependencies and source code. Use when
  the user asks to "run a security scan", "check for vulnerabilities", "audit
  dependencies", "OWASP check", "find security issues", "CVE scan", or needs
  help identifying and fixing security problems in the codebase.
metadata:
  version: "0.1.0"
---

Perform security scanning across dependencies and source code for BrainzLab services.

## Dependency Audit

### Node.js Projects

Run `npm audit --json` or `yarn audit --json` and parse the results.

Categorize findings by severity: **critical**, **high**, **moderate**, **low**.

For each vulnerability:
1. Package name and version
2. CVE identifier
3. Severity and CVSS score
4. Whether a patched version exists
5. Recommended fix: `npm audit fix` or manual version bump

### Ruby Projects

Run `bundle audit check --update` (requires `bundler-audit` gem).

Parse output for known CVEs in Gemfile dependencies. Cross-reference with `references/owasp-checklist.md` for Rails-specific patterns.

## Source Code Scanning

Analyze source code for OWASP Top 10 vulnerabilities. Focus on patterns detailed in `references/owasp-checklist.md`.

### Scan Process

1. **Identify attack surface** — routes, controllers, API endpoints, form handlers
2. **Check each OWASP category** against the codebase:
   - SQL injection: raw SQL queries, string interpolation in queries
   - XSS: unescaped user input in views, `html_safe` / `raw` usage
   - CSRF: missing authenticity tokens, exposed API endpoints
   - Auth issues: hardcoded credentials, weak session config
   - Sensitive data exposure: secrets in code, unencrypted PII
   - Insecure deserialization: `Marshal.load`, `eval`, `YAML.load`
   - Mass assignment: unpermitted params, missing strong parameters

3. **Search for secrets** — scan for API keys, tokens, passwords in code:
   ```
   Patterns: /api[_-]?key/i, /secret/i, /password\s*=\s*["']/i, /token\s*=\s*["']/i, /BEGIN.*PRIVATE KEY/
   ```

## Report Format

```
## Security Scan Report

**Project:** <service-name>
**Date:** <timestamp>

### Dependency Vulnerabilities
| Package | Severity | CVE | Fix Available |
|---------|----------|-----|---------------|
| ...     | ...      | ... | ...           |

### Code Vulnerabilities
| File:Line | Category | Severity | Description |
|-----------|----------|----------|-------------|
| ...       | XSS      | High     | ...         |

### Summary
- Critical: N
- High: N
- Moderate: N
- Low: N

### Recommended Actions
1. (Prioritized list of fixes)
```

## Additional Resources

- **`references/owasp-checklist.md`** — Detailed OWASP Top 10 patterns for Rails and Node.js
