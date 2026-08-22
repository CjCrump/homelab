# Network Design

## Topology
Single flat LAN behind a consumer router (Asus RT-AC86U) acting as gateway, DHCP, and DNS. All hosts hold static IP reservations off a shared subnet.

## Node Roles
| Role | Function |
|---|---|
| Desktop | Daily driver, dev workstation, local LLM stack (Ollama/AnythingLLM) |
| HP Node | Always-on Docker host: DNS/ad-block, password manager, git DR mirror, reverse proxy, voice server |
| Alienware | Daily driver / dev workstation |
| NAS Node | Always-on authoritative storage (ZFS mirrors), Nextcloud, Tailscale exit, game servers |
| Kid PCs | Game clients only, no services hosted |

## External Access
- **Game servers**: direct UDP port-forwarding on the router, addressed via the router's built-in Dynamic DNS
- **Everything else** (Nextcloud, internal dashboards): Tailscale mesh VPN — no inbound ports opened, devices join the tailnet and reach services by Tailscale IP

## Internal DNS
Pi-hole was deployed on HP Node for network-wide ad-blocking and `.lan` hostname resolution (e.g., `vault.lan`, `gitea.lan` routing through Caddy). It's currently **disabled** — the blocklists were too aggressive and broke legitimate traffic. This means `.lan` hostnames no longer resolve network-wide; affected hosts fall back to raw LAN IPs until blocklists are retuned and Pi-hole is re-enabled.

## Considered Alternatives
A dedicated router/firewall split (OPNsense/pfSense + managed switch + AP) was evaluated for proper VLAN segmentation but rejected in favor of staying on a single consumer router (Asuswrt-Merlin-class) for simplicity — see `decisions/0002-single-router-vs-vlan-segmentation.md`.

## Known Gaps
- No VLAN segmentation — IoT/kid devices, servers, and trusted devices all share one broadcast domain
- Pi-hole disabled means no network-wide ad-blocking or reliable internal DNS currently
