# Homelab Infrastructure

Self-hosted infrastructure for family services, dev workflows, and game servers. Built and maintained solo, documented for continuity and reproducibility.

## Architecture

```
                        ┌─────────────────────┐
                        │  Asus RT-AC86U       │
                        │  LAN gateway         │
                        │  DDNS: <redacted>    
                        └──────────┬───────────┘
                                   │
        ┌──────────────┬──────────────┬──────────────┬──────────────┐
        │              │              │              │              │
     Desktop        HP Node       Alienware      NAS-Node        Kid PCs
   Ryzen 5600G      i3-8145U      i7-11800H       i7-4790K     (game clients)
   RTX 5060 Ti     Docker host    RTX 3080         ONLINE
   daily driver     Caddy/TLS    daily driver   Authoritative NAS
                   Pi-hole(off)                Nextcloud+Tailscale
                   Vaultwarden                  Palworld server
                     Gitea
                   TeamSpeak
```
Both `desktop` and `alienware` are daily-driver machines (not primary/backup). `nas-node` is the authoritative NAS + Nextcloud/Tailscale/game-server host — fully online. `hp-node` remains the Docker services box for Pi-hole/Vaultwarden/Gitea/Caddy/TeamSpeak, with its NAS mount (`/mnt/nas`) now pointed at `nas-node`.

## Core Design Decisions

**Caddy over Nginx** — automatic internal TLS (`tls internal`) with zero manual cert management for LAN-only `.lan` hostnames. Tradeoff: clients need the Caddy root CA installed to trust it.

**ZFS mirror over hardware RAID** — bitrot detection + easy `zpool export/import` across hosts. Migrating drives between machines just means matching UIDs (`nora`=1001, `sawyer`=1002) for Samba ownership continuity.

**Docker for all services** — isolation and reproducibility on constrained/aging hardware (i3-8145U); easy migration path when `nas-node` comes online.

**SQLite for Gitea** — no separate DB service needed for a low-traffic internal git host; keeps the i3 node lightweight.

## Services

| Service | Host | Purpose | Notes |
|---|---|---|---|
| Samba NAS | nas-node | Family file shares | `ironwolf-mirror` ZFS mirror; per-user shares (chance/nora/sawyer/nikki) + `media` group share, setgid 2775 |
| Nextcloud | nas-node | File sync/sharing | Docker Compose + MariaDB, on separate `nextcloud-mirror` ZFS pool (2x1TB), port 8081 |
| Tailscale | nas-node | Remote access mesh | tailnet IP redacted |
| Palworld | nas-node | Game server | SteamCMD/systemd, family-friendly config, UDP 8211/8212 forwarded, external via DDNS |
| Pi-hole | hp-node | Network DNS/ad-block | **disabled** — too aggressive, breaks `.lan` resolution |
| Vaultwarden | hp-node | Password manager | nightly backup, 14-day retention |
| Gitea | hp-node | Git DR mirror (GitHub primary) | pull-mirror only |
| Caddy | hp-node | Reverse proxy / TLS termination | routes `vault.lan`, `gitea.lan` |
| TeamSpeak | hp-node | Voice server | no-account-needed for family |
| Ollama + AnythingLLM | desktop | Local LLM stack | 14B Q4_K_M @ 32K ctx on 16GB VRAM |

## Backup Strategy

Cron jobs in `/etc/cron.d/` on `hp-node`:
- Vaultwarden: 3am daily
- Gitea: 4am daily
- Retention: 14 days
- Target: `/mnt/nas/NAS/backups/hp-server-node/<service>/`

## Known Issues / Fixed Bugs

- **RTX 5060 Ti PCI I/O errors** — required Above 4G Decoding + Resizable BAR enabled, CSM disabled in BIOS. Reverts on any BIOS reset.
- **Pi-hole DNS breakage** — disabling ad-block broke network-wide `.lan` resolution since desktop netplan still points DNS at `hp-node`.
- **Alienware ACPI DSDT error** (`_TZ.ETMD`) — cosmetic firmware bug, fix pending Dell firmware update.
- **Custom GRUB theme conflict** — Pip-Boy theme writes directly to `grub.cfg`, silently overwriting `update-grub` changes.

## Roadmap

- [x] Bring `nas-node` online (Ubuntu Server)
- [x] Export/import `ironwolf-mirror` from desktop → `nas-node`
- [x] Repoint `hp-node`'s `/mnt/nas` mount to `nas-node`
- [x] Stand up second mirror pool + deploy Nextcloud + Tailscale
- [x] Stand up Palworld dedicated server on `nas-node`
- [ ] Re-enable Pi-hole with tuned blocklists
- [ ] Nightly `git clone --mirror` to IronWolf for real git DR
- [ ] Resolve Alienware ACPI DSDT boot error (pending Dell firmware update)

---
*Full hardware inventory and low-level config notes live in `CLAUDE.md`.*

- [Backup Strategy](docs/backup-strategy.md)
- [Network Design](docs/network-design.md)
- [Troubleshooting Log](docs/troubleshooting-log.md)
- Architecture Decisions:
  - [0001: Caddy over Nginx](docs/decisions/0001-caddy-over-nginx.md)
  - [0002: Single router over VLAN segmentation](docs/decisions/0002-single-router-vs-vlan-segmentation.md)
  - [0003: ZFS mirror over hardware RAID](docs/decisions/0003-zfs-mirror-over-hardware-raid.md)
  - [0004: Tailscale over port-forwarding](docs/decisions/0004-tailscale-over-port-forwarding.md)
- [Ansible Bootstrap](ansible/README.md)