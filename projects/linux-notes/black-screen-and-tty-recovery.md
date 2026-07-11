# Black Screen and TTY Recovery

## Purpose

A diagnostic process for occasional black screens, failed graphical sessions, and boot problems on Fedora Kinoite with KDE Plasma and NVIDIA graphics.

## Try a TTY

```text
Ctrl+Alt+F3
```

Other TTYs may be available through `F2` to `F6`. Log in with the normal Linux account.

## Basic System State

```bash
uptime
df -h
free -h
systemctl --failed
```

## Display Manager and Logs

```bash
systemctl status display-manager
journalctl -b -p warning
journalctl -b -p err
journalctl -b | grep -Ei 'nvidia|nouveau|drm|gpu|kwin|plasma|sddm'
```

## Restarting the Graphical Login

Warning: this closes the current graphical session and can discard unsaved GUI work.

```bash
sudo systemctl restart display-manager
```

Return to the graphical session with `Ctrl+Alt+F1` or `Ctrl+Alt+F2`; the exact key can vary.

## Previous-Boot Logs

```bash
journalctl -b -1 -p warning
journalctl -b -1 -k
journalctl -b -1 | grep -Ei 'nvidia|drm|gpu|suspend|resume|sddm|kwin|plasma'
```

## Kinoite Rollback

```bash
rpm-ostree status
sudo rpm-ostree rollback
systemctl reboot
```

A previous deployment can also be selected from the boot menu.

## Incident Notes to Record

- Date and time
- Whether it followed an update
- Whether it happened at boot, wake, shutdown, or login
- Whether the keyboard responded
- Whether a TTY was accessible
- Kernel and deployment version
- Relevant log excerpts
- Recovery action
- Whether the problem returned

## Lessons Learned

- A black screen does not always mean the entire system crashed.
- TTY access separates graphical failure from full system failure.
- Previous-boot logs are valuable after forced restarts.
- Atomic deployments offer a practical recovery path.
