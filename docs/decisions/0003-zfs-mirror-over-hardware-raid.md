# 0003: ZFS Mirror over Hardware RAID

**Status**: Accepted

## Context
Needed redundant storage for family NAS data across two 8TB drives, with the expectation that drives/pools would eventually migrate to different physical hosts as hardware was repurposed.

## Decision
Use ZFS mirrored pools (software-managed) instead of a hardware RAID controller.

## Consequences
- Built-in checksumming catches silent data corruption (bitrot) that hardware RAID typically can't detect
- Pools are portable across hosts via `zpool export` / `zpool import` — no controller lock-in, unlike proprietary hardware RAID metadata
- Requires care during migration: users/groups must be recreated with matching UID/GID on the new host or file ownership breaks even though the pool imports cleanly
- No hardware RAID card to fail as a single point of failure
