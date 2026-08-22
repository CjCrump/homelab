# 0004: Tailscale over Port-Forwarding for Remote Service Access

**Status**: Accepted

## Context
Needed remote access to internal services (Nextcloud, internal dashboards) from outside the LAN, without a static public IP or comfort exposing internal ports directly to the internet.

## Decision
Use Tailscale (WireGuard-based mesh VPN) for all remote access to internal services. Reserve direct port-forwarding only for the game server, where the client software has no VPN-awareness and needs a plain UDP endpoint.

## Consequences
- No inbound firewall ports opened for Nextcloud or other internal services — attack surface stays limited to the router's admin UI and the two explicitly forwarded game-server ports
- Every device needing access must install the Tailscale client and join the tailnet — extra setup step compared to "just hit the IP"
- Game server remains reachable by a wider audience (friends without Tailscale) via the router's Dynamic DNS, which was an explicit tradeoff for that specific service
