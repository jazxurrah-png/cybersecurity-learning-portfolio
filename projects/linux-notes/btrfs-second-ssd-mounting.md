# Permanently Mounting a Btrfs Second SSD

## Objective

Mount a second Btrfs-formatted SSD automatically at boot using its UUID.

## Safety

Before editing `/etc/fstab`, confirm the correct partition, filesystem, UUID, and mount point. A malformed entry can interrupt boot.

## Identify the Drive

```bash
lsblk -f
sudo blkid
```

Record:

- Device path
- Filesystem type
- UUID
- Intended mount point

## Create the Mount Point

```bash
sudo mkdir -p /mnt/games
```

## Back Up fstab

```bash
sudo cp /etc/fstab /etc/fstab.backup
```

## Add the Entry

```bash
sudoedit /etc/fstab
```

Example:

```fstab
UUID=REPLACE-WITH-ACTUAL-UUID /mnt/games btrfs defaults,noatime,compress=zstd:1 0 0
```

Do not copy the example UUID literally.

## Test Before Rebooting

```bash
sudo mount -a
findmnt /mnt/games
df -h /mnt/games
findmnt -no FSTYPE /mnt/games
```

Expected filesystem output:

```text
btrfs
```

## Permissions

```bash
ls -ld /mnt/games
```

For a single-user data drive:

```bash
sudo chown "$USER":"$USER" /mnt/games
```

Avoid recursive ownership changes unless the effect on existing files is understood.

## Reboot Verification

```bash
systemctl reboot
findmnt /mnt/games
```

## Recovery

Comment out the new `/etc/fstab` line from a recovery shell or TTY, or restore the backup:

```bash
sudo cp /etc/fstab.backup /etc/fstab
```

## Lessons Learned

- The declared filesystem must match the actual filesystem.
- UUIDs are more reliable than device names.
- `sudo mount -a` should be run before rebooting.
- `findmnt` clearly verifies the active mount.
