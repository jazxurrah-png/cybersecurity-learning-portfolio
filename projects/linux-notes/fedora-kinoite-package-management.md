# Fedora Kinoite Package Management

## Purpose

This document records the package-management model I use on Fedora Kinoite 44. Kinoite is an Atomic Desktop, so host changes are handled differently from traditional Fedora Workstation.

## Environment

- Distribution: Fedora Kinoite 44
- Desktop environment: KDE Plasma
- Hardware: ASUS ROG Strix G512LW
- Package methods: Flatpak, Toolbx, and `rpm-ostree`

## Inspecting the Deployment

```bash
rpm-ostree status
```

This shows the active and previous deployments, layered packages, kernel version, and pending changes.

## Updating the Operating System

```bash
sudo rpm-ostree upgrade
systemctl reboot
```

## Installing a Host-Level Package

Example: GitHub CLI.

```bash
sudo rpm-ostree install gh
systemctl reboot
```

Host-level layering should be used selectively.

## Removing a Layered Package

```bash
sudo rpm-ostree uninstall PACKAGE_NAME
systemctl reboot
```

## Flatpak Applications

```bash
flatpak list
flatpak search APPLICATION_NAME
flatpak install flathub APPLICATION_ID
flatpak update
flatpak uninstall APPLICATION_ID
flatpak uninstall --unused
```

## Toolbx

```bash
toolbox create
toolbox enter
sudo dnf install PACKAGE_NAME
exit
```

Inside Toolbx, traditional Fedora package commands can be used without layering packages onto the host.

## Rollback

```bash
rpm-ostree status
sudo rpm-ostree rollback
systemctl reboot
```

A previous deployment can also be selected from the boot menu.

## Lessons Learned

- `dnf` is not the normal host package workflow on Kinoite.
- Flatpak is preferred for desktop applications.
- Toolbx is useful for development tools and temporary experiments.
- `rpm-ostree` is appropriate for software that must integrate with the host.
- Atomic deployments retain a known-working system state.
