# 0002: Single Consumer Router over Dedicated Firewall + VLANs

**Status**: Accepted

## Context
Considered a split architecture (dedicated OPNsense/pfSense firewall + managed switch + the existing router demoted to access-point) to get proper VLAN segmentation between servers, trusted devices, and other household traffic.

## Decision
Stay on a single consumer router (Asuswrt-Merlin-class firmware) rather than move to a multi-component setup.

## Consequences
- Simpler to operate and troubleshoot solo, with a single admin UI instead of coordinating firewall + switch + AP configs
- No real network segmentation — all devices share one broadcast domain and trust zone
- Preserves an upgrade path: a Merlin-compatible router can later be demoted to AP-only role if a dedicated firewall is added in front of it, without a full re-buy
