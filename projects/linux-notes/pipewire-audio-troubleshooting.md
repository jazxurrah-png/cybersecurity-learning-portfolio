# PipeWire and ALSA Audio Troubleshooting

## Purpose

A repeatable process for investigating Linux audio problems on the ASUS ROG Strix G512LW.

## Environment

- Fedora Kinoite 44
- KDE Plasma
- Realtek ALC294
- PipeWire, WirePlumber, and ALSA

## Investigation Order

1. Hardware detection
2. ALSA device availability
3. PipeWire and WirePlumber state
4. Selected profile, sink, and port
5. Mixer controls
6. Application routing

## Hardware and Kernel Detection

```bash
lspci -k | grep -A4 -Ei 'audio|multimedia'
journalctl -b -k | grep -Ei 'snd|hda|audio|alc294'
```

## ALSA Checks

```bash
aplay -l
arecord -l
amixer
amixer -c 1
speaker-test -c 2 -t wav
```

Test a specific device only after confirming its card and device numbers:

```bash
speaker-test -D hw:CARD,DEVICE -c 2 -t wav
```

## PipeWire and WirePlumber

```bash
wpctl status
wpctl inspect OBJECT_ID
pactl list cards
pactl list sinks
pactl list short sinks
pactl list short sink-inputs
```

## Service Status

```bash
systemctl --user status pipewire pipewire-pulse wireplumber
systemctl --user restart pipewire pipewire-pulse wireplumber
wpctl status
```

## Profiles, Ports, and Muting

Inspect for HDMI being selected instead of internal speakers, unavailable profiles, muted sinks, muted mixer controls, inactive ports, or applications routed to the wrong sink.

```bash
pactl set-default-sink SINK_NAME
pactl set-sink-mute SINK_NAME 0
pactl set-sink-volume SINK_NAME 75%
```

## Logs

```bash
journalctl --user -b -u pipewire -u pipewire-pulse -u wireplumber
journalctl -b | grep -Ei 'pipewire|wireplumber|alsa|snd|hda|audio'
```

## Write-Up Method

For every test, record:

- The symptom
- The relevant command output
- One change tested
- The immediate result
- Whether the change survived reboot

## Lessons Learned

- ALSA detection does not guarantee correct PipeWire routing.
- Profile, sink, and port selection all matter.
- Logs should be captured before restarting services.
- One change at a time makes the final guide reproducible.
