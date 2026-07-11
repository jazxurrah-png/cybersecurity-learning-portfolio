# Linux Diagnostic Command Reference

## Operating System

```bash
cat /etc/os-release
hostnamectl
uname -a
rpm-ostree status
```

## Hardware

```bash
lscpu
lsblk -f
lspci -k
lsusb
sudo dmidecode -t system
```

## Graphics

```bash
lspci -k | grep -EA3 'VGA|3D|Display'
nvidia-smi
lsmod | grep -E 'nvidia|nouveau|i915'
```

## Audio

```bash
wpctl status
aplay -l
pactl list short sinks
journalctl -b | grep -Ei 'audio|alsa|snd|hda|pipewire|wireplumber'
```

## Storage

```bash
lsblk -f
findmnt
df -h
sudo blkid
sudo btrfs filesystem show
```

## Memory and Processes

```bash
free -h
ps aux
top
systemd-cgtop
```

## Services

```bash
systemctl --failed
systemctl status SERVICE_NAME
systemctl --user status SERVICE_NAME
systemctl list-units --type=service --state=running
```

## Logs

```bash
journalctl -b
journalctl -b -1
journalctl -b -p err
journalctl -b -k
journalctl -f
```

## Networking

```bash
ip address
ip route
nmcli device status
resolvectl status
ping -c 4 1.1.1.1
ping -c 4 example.com
ss -tulpn
```

## Flatpak

```bash
flatpak list
flatpak info APPLICATION_ID
flatpak permission-show APPLICATION_ID
flatpak run APPLICATION_ID
```

## Git

```bash
git status
git log --oneline --decorate --graph --all
git remote -v
```

## Before Publishing Output

Remove or replace email addresses, tokens, unnecessary IP addresses, Wi-Fi names, serial numbers, filesystem UUIDs, usernames, and private paths.

## Documentation Habit

Capture the symptom, environment, commands, relevant output, changes tested, final result, and lessons learned.
