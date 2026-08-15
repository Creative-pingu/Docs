# ADR-002: No Backend Principle

## Status
- Accepted

## Context
NutriTrack is a personal app designed for offline use. Initial principle was strict no-backend.
However, Notion sync requires: credential storage, CORS handling, credential security.

## Decision
Relax no-backend principle ONLY for:
- Credential storage and management
- CORS handling for external APIs
- Stateless proxy services

Implementation: Cloudflare Worker as stateless proxy for Notion API.

Constraints:
- Worker must be stateless
- Encrypted environment variables
- Read-only operations only
- Origin allowlist enforcement
- No other backend without explicit decision

## Consequences

### Positive
- Credentials secured in env vars
- CORS issues resolved
- Read-only access (no data loss risk)
- Stateless = no server maintenance
- Free tier sufficient (100k requests/day)

### Negative
- Dependency on Cloudflare Worker
- Single point of failure (mitigated by paste fallback)
- Origin allowlist not true security (can be spoofed)

## Alternatives Considered

### Strict No Backend
- Approach: Embed credentials in JSX
- Pros: Truly no backend
- Cons: Credentials exposed in public repo - SECURITY RISK
- Decision: Rejected

### Full Backend Service
- Approach: Node.js server with database
- Pros: More features possible
- Cons: Maintenance overhead, hosting costs, overkill
- Decision: Rejected

### Browser Extension
- Approach: Use extension for credential management
- Pros: Credentials stored in extension
- Cons: iOS Safari poor extension support
- Decision: Rejected

## Security Model

### Current: Origin Allowlist Only
- Mechanism: Check Origin header against allowlist
- Security Level: Stops casual abuse from browsers on other origins
- Limitation: Origin header can be spoofed by non-browser clients

Rationale:
- Recipe data not sensitive
- Notion integration read-only
- Client-side secrets would be exposed in public repo

### Upgrade Path (if threat model changes)
1. Move Pages to private repo (GitHub Pro, ~4/month)
2. Add AUTH_SECRET env var to Worker
3. Add WORKER_AUTH_SECRET to JSX
4. Re-enable header check on requests
5. Re-deploy
Estimated Effort: ~30 minutes
All scaffolding remains in place for clean re-enable