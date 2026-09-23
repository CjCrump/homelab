# Homelab Infrastructure

Self-hosted infrastructure for family services, dev workflows, and game servers, built and maintained solo.

## What it does

Runs a small fleet of home servers that handle family file shares, remote access, a game server, password management, internal git hosting, and a local LLM stack. The repo documents the architecture, the reasoning behind each design decision (Caddy over Nginx, ZFS mirrors over hardware RAID, Docker for isolation on aging hardware), the backup strategy, and a running troubleshooting log, so the setup stays reproducible even solo.

## Tech stack

- Docker (all services, for isolation and easy migration)
- ZFS mirrored pools (bitrot detection, portable across hosts)
- Caddy (reverse proxy and automatic internal TLS)
- Samba and Nextcloud (family file shares and sync)
- Tailscale (remote access mesh)
- Vaultwarden (password manager)
- Gitea (internal git host, pull-mirror of GitHub)
- Ollama and AnythingLLM (local LLM stack)
- Ansible (bootstrap playbook)

## Screenshot

No screenshot yet. This is infrastructure documentation rather than a UI, so an architecture diagram image would fit better than a screenshot. One can be added here once available.
