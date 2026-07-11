# ASUS ROG Strix G512LW Linux Compatibility Guide

This document records compatibility notes, troubleshooting steps, and fixes for running Linux on the ASUS ROG Strix G512LW.



 I wanted to share this because I spent way too long chasing this issue, and maybe it saves someone else the headache.

This was tested on an:

ASUS ROG Strix G512LW / G512LW_G512LW
Realtek ALC294 internal audio
Intel Comet Lake-H UHD graphics
NVIDIA RTX 2070 Mobile / Max-Q

The problem

The laptop’s internal speakers were detected in Linux, but they were silent.

Symptoms I ran into:

Built-in audio shows up in sound settings
Speaker-test runs but no sound comes out
Headphones/HDMI may show separately
PipeWire/WirePlumber sees the device
Internal speakers may randomly break again after updates/reboots

The important thing I learned is that this fix is not just distro-specific. The same laptop can need a slightly different snd-hda-intel model= order depending on which HDA audio controller Linux detects first.

This laptop has both:

Intel PCH / Realtek ALC294 analog audio
NVIDIA HDMI audio

The model= options are position-based, so the order matters.
Step 1: Check your audio card order

Run:

aplay -l

Look for something like this:

card 0: PCH [HDA Intel PCH], device 0: ALC294 Analog
card 1: NVidia [HDA NVidia], HDMI devices

or the reverse order, where NVIDIA is card 0 and PCH/ALC294 is card 1.

That card order decides which fix to use.
If PCH / ALC294 is card 0 and NVIDIA is card 1

This was the working order on my Fedora install.

Use:

options snd-hda-intel model=asus-zenbook,auto

Create the config:

sudo tee /etc/modprobe.d/rog-audio.conf >/dev/null <<'EOF'
options snd-hda-intel model=asus-zenbook,auto
EOF

If NVIDIA is card 0 and PCH / ALC294 is card 1

This was the working order on my Kubuntu install.

Use:

options snd-hda-intel model=auto,asus-zenbook

Create the config:

sudo tee /etc/modprobe.d/rog-audio.conf >/dev/null <<'EOF'
options snd-hda-intel model=auto,asus-zenbook
EOF

Ubuntu / Kubuntu

After creating the config file, rebuild initramfs:

sudo update-initramfs -u -k all

Then fully power off:

systemctl poweroff

Leave it off for about 30 seconds, then boot again.
Fedora

On normal Fedora, after creating the config file, rebuild initramfs with dracut:

sudo dracut --force /boot/initramfs-$(uname -r).img $(uname -r)

Then fully power off:

systemctl poweroff

Leave it off for about 30 seconds, then boot again.
Fedora Atomic / Kinoite / rpm-ostree style installs

My Fedora install did not have dnf or dnf5, so I used kernel args with rpm-ostree.

For Fedora where PCH/ALC294 is card 0 and NVIDIA is card 1:

sudo rpm-ostree kargs --delete-if-present='snd_hda_intel.model=auto,asus-zenbook'
sudo rpm-ostree kargs --append-if-missing='snd_hda_intel.model=asus-zenbook,auto'

Then power off:

systemctl poweroff

Wait about 30 seconds, then boot again.
Step 2: Verify the quirk loaded

After reboot, run:

cat /sys/module/snd_hda_intel/parameters/model

For Fedora in my case, I wanted to see:

asus-zenbook,auto

For Kubuntu in my case, I wanted to see:

auto,asus-zenbook

There may be a bunch of extra (null) entries after it. That is fine.
Step 3: Reset PipeWire to analog stereo

Run:

CARD=$(pactl list cards short | awk '/00_1f.3/ {print $2; exit}')
echo "$CARD"

pactl set-card-profile "$CARD" output:analog-stereo+input:analog-stereo
pactl set-default-sink alsa_output.pci-0000_00_1f.3.analog-stereo

Then unmute the ALSA controls:

PCH=$(aplay -l | awk '/HDA Intel PCH|PCH/ {gsub(":","",$2); print $2; exit}')
echo "$PCH"

amixer -c "$PCH" set Master 100% unmute || true
amixer -c "$PCH" set Speaker 100% unmute || true
amixer -c "$PCH" set Headphone 100% unmute || true
amixer -c "$PCH" set PCM 100% unmute || true
amixer -c "$PCH" set 'Auto-Mute Mode' Disabled || true

sudo alsactl store

Restart PipeWire/WirePlumber:

systemctl --user restart pipewire pipewire-pulse wireplumber

Test sound:

pw-play /usr/share/sounds/alsa/Front_Center.wav

or:

speaker-test -D plughw:PCH,0 -c 2 -t wav

What finally fixed it for me

The big “aha” moment was realizing that this:

options snd-hda-intel model=auto,asus-zenbook

worked on Kubuntu because my Realtek/PCH audio was the second HDA controller there.

But Fedora detected the cards in the opposite order, so Fedora needed this instead:

options snd-hda-intel model=asus-zenbook,auto

So the short rule is:

If PCH / ALC294 is card 0 and NVIDIA is card 1:
options snd-hda-intel model=asus-zenbook,auto

If NVIDIA is card 0 and PCH / ALC294 is card 1:
options snd-hda-intel model=auto,asus-zenbook

That fixed the internal speakers for me on the ASUS ROG Strix G512LW with Realtek ALC294 and RTX 2070 Mobile / Max-Q.

Hopefully this helps someone else with the same cursed little audio gremlin.I wanted to share this because I spent way too long chasing this issue, and maybe it saves someone else the headache.This was tested on an:ASUS ROG Strix G512LW / G512LW_G512LW
Realtek ALC294 internal audio
Intel Comet Lake-H UHD graphics
NVIDIA RTX 2070 Mobile / Max-QThe problemThe laptop’s internal speakers were detected in Linux, but they were silent.Symptoms I ran into:Built-in audio shows up in sound settings
Speaker-test runs but no sound comes out
Headphones/HDMI may show separately
PipeWire/WirePlumber sees the device
Internal speakers may randomly break again after updates/rebootsThe important thing I learned is that this fix is not just distro-specific. The same laptop can need a slightly different snd-hda-intel model= order depending on which HDA audio controller Linux detects first.This laptop has both:Intel PCH / Realtek ALC294 analog audio
NVIDIA HDMI audioThe model= options are position-based, so the order matters.Step 1: Check your audio card orderRun:aplay -lLook for something like this:card 0: PCH [HDA Intel PCH], device 0: ALC294 Analog
card 1: NVidia [HDA NVidia], HDMI devicesor the reverse order, where NVIDIA is card 0 and PCH/ALC294 is card 1.That card order decides which fix to use.If PCH / ALC294 is card 0 and NVIDIA is card 1This was the working order on my Fedora install.Use:options snd-hda-intel model=asus-zenbook,autoCreate the config:sudo tee /etc/modprobe.d/rog-audio.conf >/dev/null <<'EOF'
options snd-hda-intel model=asus-zenbook,auto
EOFIf NVIDIA is card 0 and PCH / ALC294 is card 1This was the working order on my Kubuntu install.Use:options snd-hda-intel model=auto,asus-zenbookCreate the config:sudo tee /etc/modprobe.d/rog-audio.conf >/dev/null <<'EOF'
options snd-hda-intel model=auto,asus-zenbook
EOFUbuntu / KubuntuAfter creating the config file, rebuild initramfs:sudo update-initramfs -u -k allThen fully power off:systemctl poweroffLeave it off for about 30 seconds, then boot again.FedoraOn normal Fedora, after creating the config file, rebuild initramfs with dracut:sudo dracut --force /boot/initramfs-$(uname -r).img $(uname -r)Then fully power off:systemctl poweroffLeave it off for about 30 seconds, then boot again.Fedora Atomic / Kinoite / rpm-ostree style installsMy Fedora install did not have dnf or dnf5, so I used kernel args with rpm-ostree.For Fedora where PCH/ALC294 is card 0 and NVIDIA is card 1:sudo rpm-ostree kargs --delete-if-present='snd_hda_intel.model=auto,asus-zenbook'
sudo rpm-ostree kargs --append-if-missing='snd_hda_intel.model=asus-zenbook,auto'Then power off:systemctl poweroffWait about 30 seconds, then boot again.Step 2: Verify the quirk loadedAfter reboot, run:cat /sys/module/snd_hda_intel/parameters/modelFor Fedora in my case, I wanted to see:asus-zenbook,autoFor Kubuntu in my case, I wanted to see:auto,asus-zenbookThere may be a bunch of extra (null) entries after it. That is fine.Step 3: Reset PipeWire to analog stereoRun:CARD=$(pactl list cards short | awk '/00_1f.3/ {print $2; exit}')
echo "$CARD"

pactl set-card-profile "$CARD" output:analog-stereo+input:analog-stereo
pactl set-default-sink alsa_output.pci-0000_00_1f.3.analog-stereoThen unmute the ALSA controls:PCH=$(aplay -l | awk '/HDA Intel PCH|PCH/ {gsub(":","",$2); print $2; exit}')
echo "$PCH"

amixer -c "$PCH" set Master 100% unmute || true
amixer -c "$PCH" set Speaker 100% unmute || true
amixer -c "$PCH" set Headphone 100% unmute || true
amixer -c "$PCH" set PCM 100% unmute || true
amixer -c "$PCH" set 'Auto-Mute Mode' Disabled || true

sudo alsactl storeRestart PipeWire/WirePlumber:systemctl --user restart pipewire pipewire-pulse wireplumberTest sound:pw-play /usr/share/sounds/alsa/Front_Center.wavor:speaker-test -D plughw:PCH,0 -c 2 -t wavWhat finally fixed it for meThe big “aha” moment was realizing that this:options snd-hda-intel model=auto,asus-zenbookworked on Kubuntu because my Realtek/PCH audio was the second HDA controller there.But Fedora detected the cards in the opposite order, so Fedora needed this instead:options snd-hda-intel model=asus-zenbook,autoSo the short rule is:If PCH / ALC294 is card 0 and NVIDIA is card 1:
options snd-hda-intel model=asus-zenbook,auto

If NVIDIA is card 0 and PCH / ALC294 is card 1:
options snd-hda-intel model=auto,asus-zenbookThat fixed the internal speakers for me on the ASUS ROG Strix G512LW with Realtek ALC294 and RTX 2070 Mobile / Max-Q.Hopefully this helps someone else with the same cursed little audio gremlin. 

