# Gigabyte Z790 Aero G + i9 13900K + W6600 Pro + Fenvi T919

![about-this-mac](https://raw.githubusercontent.com/kilinrax/gigabyte-z790-aero-g-efi/main/about-this-mac.png)

**Latest working macOS**: 13.2.1
<br>
**Current OpenCore**: 0.9.2

## Complete hardware specs
- Intel i9 13900K @ 5.2GHz P-cores / 4.2GHz E-cores / 1.2V (all cores activated)
- Gigabyte Z790 Aero G @ BIOS F2
- EVGA FTW3 3090 in slot 1 (disabled under macOS via DeviceProperties)
- Radeon W6600 Pro in slot 2 (spoofed as 6600 via SSDT)
- 2x 32Gb DDR5 5600MT/s Kingston Fury Beast RAM running at CL36 enhanced Hynix XMP profile
- Stock Intel i225-V WiFi/BT unused, replaced with Fenvi T919 / BCM94360CD in slot 3 (works OOB)
- WD SN750 NVMe
- Lian Li LConnect 3 fan/RGB controller
- Fractal Define Define 7 case

## What works
- macOS Ventura, Monterey, probably Catalina and Big Sur
- Audio
- DisplayPort @ 4K120
- All USB ports
- Everything iCloud related (Drive, unlock with Apple Watch, Handoff in both directions)
- Temperature/power/fan monitoring for CPU, system fan and W6600 (temperature only)
- DRM content (Apple TV+)
- Minor macOS releases can be installed directly, without using a USB stick
- Sleep/wake barring minor issues below
- Bluetooth, AirPods work without stuttering or cutouts

## What doesn't work
- Continuity camera - macOS bug, doesn't work unless the device also has an inbuilt camera (iMac, MacBook)
- Sidecar - hardware limitation, no compatible iGPU or T2 chip
- Broadcom Bluetooth driver is broken under Windows 11 (must use Intel, needs an extra antenna, annoying)

## Minor issues
- Bluetooth sometimes needs rebooting on wake
- USB sticks sometimes disconnect on sleep/wake
- Fan rpm sensors can stop reporting on sleep/wake
- Display sometimes takes a while to reappear after Apple logo

## Kexts used:
- AppleALC.kext
- CPUFriend.kext
- CPUFriendDataProvider.kext
- CpuTopologyRebuild.kext
- Lilu.kext
- RestrictEvents.kext
- SMCProcessor.kext
- SMCSuperIO.kext
- USBMap.kext
- USBPower.kext
- USBToolBox.kext
- USBWakeFixup.kext
- VirtualSMC.kext
- WhateverGreen.kext
- Z790-XHCI-unsupported.kext

Note NVMeFix.kext is (currently) disabled.  See [Choosing a Compatible NVMe SSD for your macOS Boot Drive](https://www.tonymacx86.com/threads/choosing-a-compatible-nvme-ssd-for-your-macos-boot-drive.323479/) for advice that led to using the WD SN750.

## SSDTs used:
- SSDT-AWAC.aml
- SSDT-EC-USBX.aml
- SSDT-GPU-SPOOF.aml
- SSDT-PLUG-ALT.aml
- SSDT-RHUB.aml
- SSDT-SBUS-MCHC.aml
- SSDT-USBW.aml

Note that GPU spoofing is necessary to avoid 'Failed Power Play Resume' errors from AmdRadeonController.cpp on wake.  Yes, this is strange: a W6600 Pro is closer than an AIB partner 6600 to the W6600X found in a real Mac Pro.

## Issues solved

### Verbose mode

If `boot-args` includes `-v`, the '*display sometimes takes a while to appear*' problem turns into a '*the display never activates on some reboots*' problem.

### Errors in DeviceProperties

PCI paths for any device being incorrect will cause boot loops.

### Nvidia dGPU not disabled

Causes constant problems with Bluetooth.

### NVMeFix.kext

Causes root partition to be mounted read-only, startup hangs.

### SetupVirtualMap

Must be `True` to boot, counter-intuitively.

### EFI partition damaged (happened twice, cause unknown)

Locate it with `diskutil list`, fix with `sudo fsck_msdos`.

## Benchmarks

![R23 multi-core: 37104, single-core: 1879](https://raw.githubusercontent.com/kilinrax/gigabyte-z790-aero-g-efi/main/cinebench_r23.png)

**Multi-core**: +32% vs Xeon W-3275M (top spec Mac Pro), +70% vs M1 Ultra
<br>
**Single-core**: +10% vs M2

Scores are ~39k and ~2.2k in Windows, so CpuTopologyRebuild.kext isn't perfect - it seems to select an E-core for every Cinebench single-core run.

## Thanks/Credits
- [Opencore Team](https://dortania.github.io/getting-started/)
- [Using Alder Lake or Raptor Lake](https://chriswayg.gitbook.io/opencore-visual-beginners-guide/advanced-topics/using-alder-lake)
- [BASE EFI for Intel Raptor Lake](https://github.com/luchina-gabriel/BASE-EFI-INTEL-DESKTOP-13THGEN-RAPTOR-LAKE)
- [SSDT GPU spoofing guide](https://elitemacx86.com/threads/how-to-spoof-graphics-on-macos-intel-amd-nvidia.1008/)
- [The PCI ID Repository](https://pci-ids.ucw.cz/read/PC/1002)
- AlexFullmoon, RehabMan
