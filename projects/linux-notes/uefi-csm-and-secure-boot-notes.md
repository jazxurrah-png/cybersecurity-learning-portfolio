# UEFI, CSM, and Secure Boot Notes

## Purpose

A summary of firmware settings that can affect Linux installation, boot behavior, and third-party graphics drivers.

## UEFI

Check whether Linux booted in UEFI mode:

```bash
test -d /sys/firmware/efi && echo "Booted in UEFI mode" || echo "Booted in legacy mode"
```

List UEFI boot entries:

```bash
sudo efibootmgr -v
```

## CSM

Compatibility Support Module emulates older BIOS behavior. On a modern computer it can:

- Change which drives or boot entries appear
- Disable or complicate Secure Boot
- Mix legacy and UEFI boot methods
- Make bootloader troubleshooting harder

A consistent UEFI setup is generally easier to maintain than mixing UEFI and CSM modes.

## Secure Boot

```bash
mokutil --sb-state
```

Considerations:

- Third-party kernel modules may require signing.
- NVIDIA behavior can depend on how modules were installed.
- Disabling Secure Boot changes whether unsigned modules load, but is not a universal fix.
- Change one firmware option at a time and record the outcome.

## Boot Information to Record

```bash
cat /etc/os-release
uname -r
rpm-ostree status
lsblk -f
sudo efibootmgr -v
mokutil --sb-state
```

## Troubleshooting Method

1. Record current firmware settings.
2. Confirm UEFI or legacy boot mode.
3. Confirm the EFI System Partition exists.
4. Check Secure Boot state.
5. Check whether required kernel modules loaded.
6. Change one option at a time.
7. Restore the previous setting when a test fails.

## Lessons Learned

- UEFI, CSM, and Secure Boot are separate concepts.
- Mixing boot modes complicates diagnosis.
- Secure Boot can affect third-party modules.
- Firmware work should be documented carefully.
