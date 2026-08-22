# Troubleshooting Log

Notable issues diagnosed and resolved, kept as a reference for recurring or subtle problems.

## GPU: PCI I/O region invalid on RTX 5060 Ti
**Symptom**: System POSTs fine, boots normally, but `nvidia-smi` fails and the GPU is unusable.
**Cause**: BIOS settings for Above 4G Decoding and Resizable BAR were not enabled (or CSM was left enabled), which the card requires.
**Fix**: Enable Above 4G Decoding + Resizable BAR, disable CSM. Note: any BIOS reset (e.g. after a firmware flash) reverts these and reproduces the symptom.

## Samba shares invisible/unreliable in Windows File Explorer
**Symptom**: Shares don't reliably appear or connect via File Explorer's network browsing, with no clear error.
**Cause**: Windows network discovery/SMB browsing is unreliable for LAN-only Samba hosts.
**Fix**: Map the share explicitly from Command Prompt: `net use Z: \\<host>\<share> /user:<user> /persistent:yes`. Reliable every time; skips discovery entirely.

## Pi-hole breaking network-wide DNS
**Symptom**: After enabling aggressive blocklists, multiple devices lost access to legitimate sites and internal `.lan` hostnames became unreliable.
**Cause**: Overly broad blocklists combined with Pi-hole being the sole DNS resolver for the network.
**Resolution**: Disabled Pi-hole entirely rather than tune blocklists under time pressure. `.lan` hostname resolution network-wide is a known current gap until this is revisited with a lighter blocklist config.

## ZFS pool migration between hosts
**Symptom**: Moving a mirrored ZFS pool from one machine to another risked import failures and broken file ownership.
**Cause**: ZFS pools must be cleanly exported before physical drive removal; Samba UID/GID mappings must match exactly on the new host or file ownership breaks even though the pool imports fine.
**Fix**: Always `zpool export <pool>` before disconnecting drives. On the new host, recreate local users/groups with matching UID/GID values before importing.

## ACPI BIOS error on laptop boot (`_TZ.ETMD` not found)
**Symptom**: Recurring ACPI BIOS error at boot referencing an unresolved thermal-zone symbol; boot proceeds but slower than expected.
**Cause**: Firmware-level DSDT table bug — a thermal zone method the table references doesn't exist. Cosmetic/non-functional in practice.
**Status**: Monitoring after a vendor firmware update; if unresolved, a GRUB `acpi_osi=` kernel parameter is the next step. Complicated by a custom GRUB theme that writes directly to the compiled `grub.cfg`, which can silently overwrite `update-grub`-managed changes.

## Palworld "Missing configuration" on first launch
**Symptom**: Dedicated server binary fails immediately after SteamCMD install completes.
**Cause**: Missing 32-bit compatibility runtime libraries required by the server binary on a 64-bit-only Ubuntu Server install.
**Fix**: Install the missing 32-bit libc/libstdc++ compatibility packages before first launch.
