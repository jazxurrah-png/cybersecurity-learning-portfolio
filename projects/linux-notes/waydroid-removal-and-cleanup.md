# Waydroid Removal and Cleanup

## Purpose

A careful process for removing Waydroid and its local data from Fedora Kinoite.

## Warning

The cleanup commands can permanently delete Android container data, installed applications, and files stored inside Waydroid. Back up anything important first.

## Inspect the Installation Method

```bash
rpm-ostree status
flatpak list | grep -i waydroid
systemctl status waydroid-container
```

## Stop and Disable the Container

```bash
sudo systemctl stop waydroid-container
sudo systemctl disable waydroid-container
```

If the service does not exist, continue.

## Remove a Layered Package

Only when `rpm-ostree status` shows Waydroid as layered:

```bash
sudo rpm-ostree uninstall waydroid
systemctl reboot
```

## Remove a Flatpak Build

Only when `flatpak list` shows one:

```bash
flatpak uninstall APPLICATION_ID
```

## Remove Remaining Data

Destructive: inspect each path first.

```bash
rm -rf ~/.local/share/waydroid
sudo rm -rf /var/lib/waydroid
```

Find other possible directories before removing them:

```bash
find ~/.config ~/.local/share -maxdepth 2 -iname '*waydroid*' -print
```

## Verify Removal

```bash
command -v waydroid
systemctl status waydroid-container
rpm-ostree status
```

## Lessons Learned

- Removal depends on the original installation method.
- Package removal and data removal are separate.
- Destructive cleanup should follow inspection and backups.
- Layered-package changes on Kinoite may require rebooting.
