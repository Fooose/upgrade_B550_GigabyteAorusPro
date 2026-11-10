Welcome to the b550-Aorus-Pro wiki!<br><br>
**Update: 2026/11/10 – OpenCore 1.0.8 · macOS Tahoe 26.7.3**

![B550 Aorus Pro](https://static.gigabyte.com/StaticFile/Image/Global/32e48fbb5b276a211279763bfab3d835/ProductRemoveBg/25268/webp/800)

* Motherboard: Gigabyte B550 Aorus Pro | rev. 1.0
* Bios: F17 (Mar 27, 2024)
* CPU: Ryzen 7 5800X3D
* GPU: Merc 319 Black RX 6950 XT XFX
* RAM: 32 GB 3600
* Festplatte: M2 2x2TB
* Audio Device: ALC 1220
* LAN: Realtek 2.5GbE (LucyRTL8125Ethernet)
* Wi-Fi / BT: Broadcom BCM94360CD
* SMBIOS: ~~iMacPro 1.1~~ MacPro7,1
* macOS: Tahoe 26.7.3 (SIP enabled, no root patch)

Hello everyone, my system now runs **macOS Tahoe** – everything works, including GPU acceleration (Metal 3), sound, Wi-Fi, Bluetooth, LAN, CPU temp, sleep. Download the latest EFI under [Releases](https://github.com/Fooose/upgrade_B550_GigabyteAorusPro/releases/latest). If you have tips and optimizations, feel free to share them with me!

Best regards Fooose

## Current EFI (OC 1.0.8)

**Kexts:** Lilu, VirtualSMC, AppleALC, RestrictEvents, NootRX, LucyRTL8125Ethernet, AppleMCEReporterDisabler, SimpleBCM

**Boot-args:** `-v debug=0x100 keepsyms=1 agdpmod=pikera -radcodec nrxgl=1 sbcmdelay=3000`

* GPU (RX 6950 XT) via **NootRX** – on Tahoe use the current CI build (the 2024 release doesn't load the framebuffer on Tahoe). ~~SSDT-6950XT-SPOOF and WG.kext~~ no longer used.
* Wi-Fi (BCM94360CD) via **SimpleBCM v1.2.1-alpha** – single kext, no IOSkywalk block, no OCLP root patch, SIP stays enabled.
* `sbcmdelay=3000` is required: without the delay SimpleBCM starts too early and the RX 6950 XT finds no display (black screen). Thanks to laobamac and fabiosun ([SimpleBCM issue #17](https://github.com/laobamac/SimpleBCM/issues/17)).
* Before a major macOS upgrade, disable SimpleBCM temporarily – the Tahoe installer got stuck with it enabled.
* Tahoe reboot loop fixed with AMD_Vanilla patch PR#215 (thread_invoke, Darwin 25.4+).
* **PlatformInfo is empty** – generate your own SMBIOS (MacPro7,1) with GenSMBIOS before booting.

Sleep works with SSDT. Thanks to @atanvarno.

LAN: ~~"sudo ifconfig en0 media 1000baseT mediaopt full-duplex" (thanks DSM2!) / LucyRTL8125Ethernet 1.1.0d8 lan fix~~ no longer required.

## BIOS settings

Major settings to be changed based on the default optimal settings:

* enable X.M.P. memory profile
* disable trusted computing
* disable wake and boot over LAN
* enable power loading in Platform Power settings (may contribute to a better stability)
* setup fan profiles
* ~~deactivate I2C and ESPI~~ / ~~set PCIe slot to Gen4~~ no longer necessary since BIOS 14c

Other relevant settings:

* Above 4G Decoding: enabled
* Re-Size BAR Support: Auto (BIOS → Advanced Mode → Settings)
* XHCI Hand-off: enabled
* HPET: enabled
* CSM support: disabled

## History

* OC 0.7.5: ~~USB declared via DSDT by [apfelnico](https://www.hackintosh-forum.de/user/34281-apfelnico/)~~ (until OC 0.8.4)
* OC 0.8.5: USB via SSDT (SSDT-B550-PTXH-TO-XHC2, SSDT-B550-XHC0-TO-XHC)
* OC 1.0.5: GPU via NootRX, new EFI from scratch
* OC 1.0.8: macOS Tahoe, Wi-Fi via SimpleBCM, WhateverGreen / AMDRadeonNavi2xExt / NVMeFix removed
