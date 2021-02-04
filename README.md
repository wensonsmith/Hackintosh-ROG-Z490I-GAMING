# Hackintosh - Asus ROG Strix Z490-I Gaming (OpenCore)

[![OpenCore](https://img.shields.io/badge/OpenCore-0.6.6-yellowgreen)](https://github.com/acidanthera/OpenCorePkg/releases/tag/0.6.6)
[![macOS](https://img.shields.io/badge/macOS-11.2-orange)](https://www.apple.com/macos/big-sur/)
[![MODEL](https://img.shields.io/badge/Model-Z490I-blue)](https://www.asus.com/Motherboards/ROG-STRIX-Z490-I-GAMING/)
[![BIOS](https://img.shields.io/badge/BIOS-0707-brightgreen)](#)

This repository is for Hackintosh builds with the **Asus ROG Strix Z490-I Gaming** motherboard using Intel's 10th Gen processors (Comet Lake). 

Everything in terms of the hardware used in this build is working as expected.

Anyone who has the same motherboard can use the EFI of this repository. 

Don’t forget to edit the `EFI/OC/config.plist` file and to generate your own SMBIOS info. See: [Comet Lake Config Guide](https://dortania.github.io/OpenCore-Install-Guide/config.plist/comet-lake.html#platforminfo) for detailed instructions on how to do that. 

I highly recommended that you read the [OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/) before you get started.

## Hardware

* **Motherboard:** [Asus ROG Strix Z490-I Gaming](https://www.asus.com/Motherboards/ROG-STRIX-Z490-I-GAMING/)
    * **Ethernet:** Intel I225-V 2.5Gbit
    * **Wi-Fi/BT:** Intel AX201NGW (can used in macOS)
    * **Audio:** Realtek ALCS1220A
* **CPU:** [Intel Core i7-10700K](https://ark.intel.com/content/www/us/en/ark/products/199335/intel-core-i7-10700k-processor-16m-cache-up-to-5-10-ghz.html)
* **GPU:** Intel UHD630 (integrated)
* **RAM:** [G.SKILL Trident Z Neo DDR4-3200MHz 32GB(16GB×2)](https://www.gskill.com/product/165/326/1562838932/F4-3200C16D-32GTZNTrident-Z-NeoDDR4-3200MHz-CL16-18-18-38-1.35V32GB-(2x16GB))
* **SSD:** [WD-Black SN750 500GB M.2 NVMe](https://shop.westerndigital.com/products/internal-drives/wd-black-sn750-nvme-ssd#WDS250G3X0C)
* **WIFI/BT:** Onboard CNVI Intel AX201NGW

## Software

* Bootloader: OpenCore 0.6.6-RELEASE
* OS: macOS Big Sur 11.2 (20D64)

## What's working

- [x] Intel UHD630 (iGPU)
- [x] Audio Realtek ALCS1220A
- [x] Intel I225-V 2.5Gb Ethernet
- [x] Wi-Fi, only works after restart
- [x] BT
- [x] USB
- [x] Restart/Shutdown
- [ ] Sleep/Wake
- [x] Power Management (Native support)

## Details
DeviceProperties: 
![device properties](./Screenshots/device-properties.png)

### GPU
#### Intel UHD630

HDMI/DP display and audio outputs are all working.

Working with:

```xml
<key>PciRoot(0x0)/Pci(0x2,0x0)</key>
<dict>
  <key>AAPL,ig-platform-id</key>
  <data>BwCbPg==</data>
  <key>framebuffer-patch-enable</key>
  <data>AQAAAA==</data>
  <key>framebuffer-stolenmem</key>
  <data>AAAwAQ==</data>
</dict>
```

### Audio

Working with:

* AppleALC.kext
* layout-id=01000000

DeviceProperties: 

```xml
<key>PciRoot(0x0)/Pci(0x1f,0x3)</key>
<dict>
  <key>layout-id</key>
  <data>AQAAAA==</data>
</dict>
```

### Ethernet 

Working with:

* FakePCIID.kext
* FakePCIID_Intel_I225-V.kext
* device-id=`F2150000`

DeviceProperties: 

```xml
<key>PciRoot(0x0)/Pci(0x1C,0x4)/Pci(0x0,0x0)</key>
<dict>
    <key>device-id</key>
    <data>8hUAAA==</data>
</dict>
```

`FakePCIID_Intel_I225-V.kext` is from **jergoo**'s repository, details in issue [2.5Gbit Ethernet (Intel I225-V) Don't work #8](https://github.com/SchmockLord/Hackintosh-Intel-i9-10900k-Gigabyte-Z490-Vision-D/issues/8).

### Wi-Fi/BT

Thanks to [IntelBluetoothFirmware](https://github.com/OpenIntelWireless/IntelBluetoothFirmware) , the onboard wireless network card (Intel AX201NGW) can works without AirDrop & Handoff. 

For me, my Magic Mouse and Airpods pro works fine, it's enough.

> The onboard wireless network card (Intel AX201NGW) uses the m.2 E-Key slot and CNVi protocol. Replacing the card with the BCM94360NG does not work due to the CNVi protocol, so don't even bother trying.

### Sleep/Wake

Works with DP output and power button. GPRW Patch is used to disable the USB device instant wake.

**Known issues:**
1. Bluetooth has a delay of about 7 seconds after the display is turned on.
2. When using HDMI, the display cannot be woken up.
3. Without enabling GPRW, a keyboard press or mouse click can wake up the display as well, but a second press or click is needed when the light is on, I tried to fix it by following [Keyboard Wake Issues Guide](https://dortania.github.io/USB-Map-Guide/misc/keyboard.html), but didn't work. So my choice is to just use the power button, disable `SSDT-GPRW` if you want to use a keyboard or mouse to wake up.

### F1 Boot Error

Add patch to `Kernel -> Patch`:

```xml
<dict>
    <key>Base</key>
    <string></string>
    <key>Comment</key>
    <string>F1 Startup patch</string>
    <key>Count</key>
    <integer>1</integer>
    <key>Enabled</key>
    <true/>
    <key>Find</key>
    <data>dTMPtw==</data>
    <key>Identifier</key>
    <string>com.apple.driver.AppleRTC</string>
    <key>Limit</key>
    <integer>0</integer>
    <key>Mask</key>
    <data></data>
    <key>MaxKernel</key>
    <string></string>
    <key>MinKernel</key>
    <string></string>
    <key>Replace</key>
    <data>6zMPtw==</data>
    <key>ReplaceMask</key>
    <data></data>
    <key>Skip</key>
    <integer>0</integer>
</dict>
```

### BIOS Settings

> Version: 0707

#### ❌ Disable

* Fast Boot
* VT-d
* CSM
* Intel SGX
* CFG Lock (no option in BIOS, Asus Z490 motherboards are factory unlocked. The `AppleCpuPmCfgLock` and `AppleXcpmCfgLock` quirks are not necessary)

#### ✅ Enable

* VT-x (no option in BIOS, it's enabled by default)
* Above 4G decoding
* Hyper-Threading
* EHCI/XHCI Hand-off
* OS type: Windows UEFI Mode (Clear Secure Boot Keys or choose `Other` type)
* DVMT Pre-Allocated(iGPU Memory): 64MB

### EFI

#### SSDTs

Compiled by following the [Dortania's ACPI Guide](https://dortania.github.io/Getting-Started-With-ACPI/), the `.dls` SSDT files can be found in SSDTS folder. 

* DSDT.aml
* SSDT-EC.aml
* SSDT-PLUG.aml
* SSDT-AWAC.aml
* SSDT-USB-RESET.aml

#### Kexts

All kexts with a version tag are downloaded from original repositories.

* AirportItlwm `1.2.0`
* VirtualSMC.kext `1.2.0`
* SMCProcessor.kext `1.2.0`
* SMCSuperIO.kext `1.2.0`
* Lilu.kext `1.5.1`
* WhateverGreen.kext `1.4.7`
* AppleALC.kext `1.5.1`
* NVMeFix.kext `1.0.5`
* FakePCIID.kext (from RehabMan `2018-1027`)
* FakePCIID_intel_I225-V.kext (from SchmockLord)

## Misc

### Installation

The installation guide in the [OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/) are quite clear and easy, so there will be no detailed installation tutorials here. Give it some patience and you can build your own EFI.

### Required Tools/Utilities

* [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS) for generating SMBIOS info
* [Hackintool](https://github.com/headkaze/Hackintool) for a lot of things
* [MaciASL](https://github.com/acidanthera/MaciASL) for compiling SSDTs
* [MountEFI](https://github.com/corpnewt/MountEFI) for mounting EFI system partition
* [ProperTree](https://github.com/corpnewt/ProperTree) for editing plist file
* [SSDTTime](https://github.com/corpnewt/SSDTTime) for dumping DSDT

<!-- TODO -->
<!-- ### Benchmarks

| Item | Score |
|---|---|
| CPU - Geekbench | [Single / Multi-Core](https://browser.geekbench.com/v5/cpu/2750529): 1218 / 8909 |
| Intel UHD630 - Geekbench | [OpenCL](https://browser.geekbench.com/v5/compute/1092240) / [Metal](https://browser.geekbench.com/v5/compute/1120839): 4826 / 4790 |
| AMD Vega 64 - Geekbench | [OpenCL](https://browser.geekbench.com/v5/compute/1121010) / [Metal](https://browser.geekbench.com/v5/compute/1121020): 75925 / 85089 | -->

### Screenshots
Geekbench CPU:
![Geekbench CPU](./Screenshots/geekbench-cpu.png)

Geekbench GPU:
![Geekbench CPU](./Screenshots/geekbench-gpu.png)

## Credits

* Acidanthera for [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg)
* Dortania for [OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/)
* SchmockLord for [Hackintosh-Intel-i9-10900k-Gigabyte-Z490-Vision-D](https://github.com/SchmockLord/Hackintosh-Intel-i9-10900k-Gigabyte-Z490-Vision-D)
* jergoo for [Hackintosh-ROG-STRIX-Z490I](https://github.com/jergoo/Hackintosh-ROG-STRIX-Z490I)
* All contributors to the hackintosh system

