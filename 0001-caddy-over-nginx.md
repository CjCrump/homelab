# 0001: Reverse Proxy — Caddy over Nginx

**Status**: Accepted

## Context
Needed TLS termination and hostname-based routing for internal `.lan` services (password manager, git host) without a public domain or paid cert authority.

## Decision
Use Caddy with its `tls internal` directive to auto-generate and manage self-signed certs per hostname, rather than Nginx with manually managed self-signed certs or Let's Encrypt (not viable for `.lan`-only names).

## Consequences
- Zero manual cert renewal/management — Caddy handles issuance and rotation internally
- Client machines must install Caddy's local root CA to trust the certs without browser warnings
- Config is declarative and short (a `Caddyfile` block per host) compared to equivalent Nginx server blocks
