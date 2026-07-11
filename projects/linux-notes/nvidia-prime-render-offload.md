# NVIDIA PRIME Render Offload

## Purpose

This document records how I inspect and use the NVIDIA GPU on an ASUS ROG Strix G512LW running Fedora Kinoite.

## Hardware

- Intel integrated graphics
- NVIDIA GeForce RTX 2070 Mobile / Max-Q

Hybrid graphics can use the integrated GPU for the desktop while launching selected applications on the NVIDIA GPU.

## Confirming Hardware and Drivers

```bash
lspci -k | grep -EA3 'VGA|3D|Display'
lsmod | grep nvidia
nvidia-smi
```

Check the current OpenGL renderer:

```bash
glxinfo -B
```

## OpenGL Offload

```bash
__NV_PRIME_RENDER_OFFLOAD=1 \
__GLX_VENDOR_LIBRARY_NAME=nvidia \
APPLICATION_COMMAND
```

Test pattern:

```bash
__NV_PRIME_RENDER_OFFLOAD=1 \
__GLX_VENDOR_LIBRARY_NAME=nvidia \
glxinfo -B
```

## Vulkan Offload

```bash
__NV_PRIME_RENDER_OFFLOAD=1 \
__GLX_VENDOR_LIBRARY_NAME=nvidia \
__VK_LAYER_NV_optimus=NVIDIA_only \
APPLICATION_COMMAND
```

Example used for PCSX2:

```bash
__NV_PRIME_RENDER_OFFLOAD=1 \
__GLX_VENDOR_LIBRARY_NAME=nvidia \
__VK_LAYER_NV_optimus=NVIDIA_only \
pcsx2-qt
```

## Flatpak Pattern

```bash
flatpak run \
  --env=__NV_PRIME_RENDER_OFFLOAD=1 \
  --env=__GLX_VENDOR_LIBRARY_NAME=nvidia \
  --env=__VK_LAYER_NV_optimus=NVIDIA_only \
  APPLICATION_ID
```

## Monitoring GPU Use

```bash
watch -n 1 nvidia-smi
```

Launch the application and check whether its process appears.

## Troubleshooting Checklist

1. Confirm both GPUs appear in `lspci`.
2. Confirm NVIDIA modules are loaded.
3. Confirm `nvidia-smi` can communicate with the driver.
4. Compare renderer output with and without offload variables.
5. Check whether the program is native, Flatpak, or containerized.
6. Confirm whether the application uses OpenGL or Vulkan.

## Lessons Learned

- Hybrid graphics may not use NVIDIA automatically.
- OpenGL and Vulkan may require different variables.
- `nvidia-smi` confirms driver communication and active processes.
- Flatpak applications may require variables to be passed explicitly.
