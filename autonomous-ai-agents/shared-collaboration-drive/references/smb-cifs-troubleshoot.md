# SMB/CIFS Mount Troubleshoot

When `ping 192.168.10.108` and `ip neigh` show REACHABLE but `mount -t cifs ...` still returns `Operation now in progress`:

- Verify syntax: `vers=3.0` (not `ver3.0`), full `-o` options; `uid=$(id -u)`, `gid=$(id -g)`; collaboration target is `/mnt/shared` (never `/tmp/shared` for final I/O).
- Try both saved passwords (`Cedarhouse`, `P@55word`) separately.
- Check SMB server share name (`shared`) and protocol (`vers=2.1` fallback); server may listen on `.158` (CT100 LXC on Proxmox `PVE03`) rather than `.108`.
- Inspect `dmesg` for SMB errors; run `smbclient -L //IP/shared -U root --password=...` to test SMB service independently of mount; `nc` TCP 445; `nmap` if available.
- If network is reachable (`ping` OK, `ip neigh` REACHABLE) but SMB/SSH time out (`NT_STATUS_IO_TIMEOUT`, `Connection timed out`), SMB service may be filtered by source subnet (`.162` vs `.95`); compare routes/interfaces with working VM; fix routing (`ip route del/add`) or use working VM's path.
- Confirm `mount | grep shared` and `ls /mnt/shared` before any collaboration I/O; `/etc/fstab` saved only after verified mount; credentials in fstab are plaintext.
- Proxmox firewall (`vmbr0` bridge, `net0` NIC for `vm100`/`ct100`): `interface:` is NIC `net0`, not bridge `vmbr0` or port `445`. Example rule: `direction:in action:accept macro:SMB interface:net0`. `bridge:vmbr0` and `interface:net0` are separate fields.
- Mount syntax uses comma separator: `-o username=root,vers=3.0` (period breaks the option).
- If granted SSH to Proxmox (`PVE03`) to fix SMB/NFS, confirm target (`root@.108`), verify actual connection (timeout = still broken); run `systemctl status smbd`, `ss -tlnp | grep 445`, `cat /etc/samba/smb.conf`, check `/etc/exports` and `/etc/network/interfaces` (`vmbr0` bridge config); do not fabricate fixed status.
- Never fabricate mounted state or collaboration file contents; report `NOT MOUNTED` honestly.
