---
name: shared-collaboration-drive
description: "Mount shared drives. Confirm real state."
version: 1.0
---

# Shared Collaboration Drive

Always-on rules for Tim's shared agent collaboration (Inc2Biz, FLSource, GETZOT, YouTube, personal projects):

- Read/write collaboration files ONLY to `/mnt/shared`. `/tmp/shared` is only for temporary/debug mounts — never treat it as the collaboration target; confirm with `ls /mnt/shared` after mounting to `/mnt/shared`.
- Confirm ACTUAL mount/status with `mount | grep shared` and `ls /mnt/shared` — NEVER claim mounted when unmounted.
- Never fabricate file contents or "mounted" confirmation.
- Confirm network (`ping`, `ip neigh`, `dmesg`) before concluding share unreachable.
- Use `vers=3.0` (not `ver3.0` typo) for CIFS.
- Save fstab entry only after verified mount; note `Cedarhouse` / `P@55word` are the share credentials (confirm which with user). Credentials visible in `/etc/fstab` will be plaintext.

Procedure:
1. Check network: `ip addr`, `ip route`, `ping 192.168.10.108`, `ip neigh`.
2. Mount with `vers=3.0`, `uid=$(id -u)`, `gid=$(id -g)`.
3. Confirm: `mount | grep shared` + `ls /mnt/shared`.
4. Save `/etc/fstab` only when confirmed working.
5. Read/write collaboration files; confirm results.

Pitfalls:
- `ver3.0` typo (missing `=`) causes mount failure — always use `vers=3.0`.
- `Operation now in progress` can persist even when `ping`/`ip neigh` show reachability; diagnose with `dmesg`, try both `Cedarhouse` and `P@55word`, and check SMB protocol/auth before concluding failure.
- Unmounted `/mnt/shared` is still a directory (`ls -la` shows empty `.`/`..`) — never write collaboration files or claim they exist on the share until `mount | grep shared` confirms.
- Confirm which password (`Cedarhouse` vs `P@55word`) is active before saving fstab; credentials in `/etc/fstab` are plaintext.
- `sudo` must cover the full `mount` and `ip route add` command; partial `sudo` (e.g., `sudo cmd1; cmd2`) leaves cmd2 unprivileged.
- SMB service reachable by ping/ARP may still time out on SMB ports (`smbclient` 445 timeout, 139 NetBIOS disabled) or SSH (`Connection timed out`) — network reachability does not guarantee SMB/SSH service availability; verify with `smbclient` and `ssh` probes before assuming service health.
- Proxmox firewall (`Datacenter > Firewall`): `interface:` must be the VM's NIC name (`net0`/`eth0`), NOT the bridge (`vmbr0`) or the port number (`445`). Example for `vm100` (`ct100`): `direction:in action:accept macro:SMB interface:net0`. Confirm `bridge:vmbr0` and `interface:net0` separately — they are different fields.
- Mount option separator is a comma (`username=root,vers=3.0`); a period (`username=root.vers=3.0`) is an invalid syntax that will fail.
- If a working VM on the same Proxmox box mounts `.158` with identical `vers=3.0`/credentials but this VM fails, the blocker is VM-specific (source IP/subnet `.162` vs `.95`, routing, or SMB service filtering) — fix the network/interface or use the working VM's target, not retrying the same failed command indefinitely.
- Proxmox VM (`vmbr0`) switch affects SMB reachability: compare current MAC (`cat /sys/class/net/ens18/address`) to `qm config 120` (`virtio=...`) — MAC can change after `qm reboot`, breaking PVE firewall and SMB service policies. Restore original MAC (`qm set 120 --net0 virtio=<MAC>,bridge=vmbr0,...`) and update PVE03 firewall (`interface:net0`, not `vmbr0`) plus file server `.17` policies together.
- If user grants remote SSH access to fix SMB, confirm the target (`root@192.168.10.108`), credential (`Cedarhouse`), and verify actual connection; timeouts mean the service/network is still broken — do not fabricate fixed status.

References: `references/smb-cifs-troubleshoot.md` for SMB protocol/auth/debug steps when network is reachable but mount still fails.
