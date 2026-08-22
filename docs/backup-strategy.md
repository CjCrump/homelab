# Backup Strategy

## Scope
Covers `hp-node` services (Vaultwarden, Gitea) and `nas-node` primary storage.

## Schedule (hp-node, `/etc/cron.d/`)
| Service | Time | Retention |
|---|---|---|
| Vaultwarden | 3:00 AM daily | 14 days |
| Gitea | 4:00 AM daily | 14 days |

Target path convention: `/mnt/nas/NAS/backups/hp-node/<service>/<timestamp>/`

## Storage
Backups land on `nas-node`'s `ironwolf-mirror` ZFS pool (2-drive mirror), mounted on `hp-node` at `/mnt/nas`.

## Git DR
GitHub is the primary remote for all dev repos. Gitea (on `hp-node`) serves as a pull-mirror DR copy. A nightly `git clone --mirror` job to the IronWolf pool is planned but not yet implemented — this is the current gap in the backup chain.

## Restore Path
1. Stop the affected service's container
2. Copy the desired dated backup folder into the service's data volume
3. Restart the container
4. Verify data integrity (login test for Vaultwarden, repo browse for Gitea)

## Known Gaps
- No automated restore testing — backups are taken but not periodically verified by restoring
- No off-site/off-network copy — single point of failure if the physical location is lost
