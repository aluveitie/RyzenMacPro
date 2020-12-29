# Ryzen Mac Pro - OpenCore EFI for Asus Strix X570-I

This repository provides the basic EFI folder to run macOS Catalina on an Asus ROG Strix X570-I Gaming motherboard. The default provided currently using a Ryzen 9 3950X 16 Core CPU and a Radeon VII with MacPro7,1 SMBIOS. For a short guide to using different CPU, GPU and SMBIOS see below.
This is intended as a reference and to share improvements for similar build, not as an out of the box EFI to download. It is highly recommended to start with a vanilla OpenCore and following OpenCore Vanilla Guide first.

## Ryzen Mac Pro build

**Prozessor:** AMD Ryzen 9 3950X  
**Cooler:** Custom Water Cooling  
**Motherboard:** Asus ROG Strix X570-I Gaming  
**WIFI/BT**: Broadcom BCM94360NG  
**Memory:** Kingston HyperX Predator (2x 32GB) DDR4-3600  
**Storage:** Corsair MP600 (1000GB) M.2 NVMe PCIe 4.0  
**Video Card:** XFX Radeon VII 16GB  
**Power Supply:** Corsair SF600 Platinum  
**Case:** Phanteks Enthoo Evolv Shift (Mini-ITX)  

### Notes
I've heavily modded the case to fit the Radeon VII with 3 120mm radiators in it.
Also I've replaced the integrated Intel AX200 module with a BCM94360NG that is natively supported by macOS. 

## Versions
**BIOS:** 3001  
**OpenCore:** 0.6.4  
**macOS:** 11.2 Beta

## Content

### ACPI

The following SSDT files are for setting up HPET and EC.

- SSDT-EC.aml
- SSDT-HPET.aml
- SSDT-PLUG.aml
- SSDT-SBRG.aml

The following SSDT files are for USB Power. 

- SSDT-USBX.aml

The following SSDT files fixing SMBUS support. Note: Using this SSDT prevents the Machine from entering sleep.

- SSDT-SBUS-MCHC.aml

### Kexts

Besides the default kexts the following are noteworthy:

For enabling the integrated Intel Bluetooth/Wifi you can use the kexts from OpenIntelWireless. Though Bluetooth is working mostly perfect, some things like audio input (Bluetooth Mic from AirPods for example) do not work.

The SMCAMDProcessor.kext is used to provide CPU temperature and frequency information, the AMD Power Gadget can be downloaded from https://github.com/trulyspinach/SMCAMDProcessor/releases. Other monitoring tools can also access and display this information.
AMDRyzenCPUPowerManagement-ES.kext is a custom compiled version with a few minor differences: Upon boot low power state (P2) is enabled and Core Performance Boost is desabled by default. Additionally the adjustment has been tuned down a little to not immediately go to the highest power state and go down again faster. This with the intent to not have the CPU going full power for just minor loads like opening an empty browser tab. Only use this kext if you know what you are doing as it requires P2 to work correctly on your CPU. 

The VoodooTSCSyncAMD kext is used to sync the cores and required the correct number of threads (cores * 2). Either update the Info.plist of the kext or create a new one with the VoodooTSCSync configurator.

The AGPMInjector kext is used to inject proper power management and can be created with Pavo-IM's generator and is specific to MacPro7,1 SMBIOS and the Radeon VII. It is recommended to create a custom kext.

The USBPorts kext is depending on the SMBIOS and can be created with the Hackintool. I've modified it according to Aleksandar's guide to use `IOKitPersonalities_x86_64` instead of `IOKitPersonalities` generated by the Hackintool.


## Setup

### BIOS settings

Everything is tested with Asus' latest BIOS 3001:

- CSM: disabled
- Above 4G decoding: enabled
- RezisableBAR: disabled
- Fast boot: disabled


## Known issues

The integrated Intel Wifi is supported by [itlwmx](https://github.com/OpenIntelWireless/itlwm) though missing support for AirDrop etc.

Microphone is not yet working through integrated audio codec.

## Notes

Use at your own risk.
See branch [asrock-x570-itx](https://github.com/aluveitie/RyzenMacPro/tree/asrock-x570-itx) with the EFI for the old build.

Discussions:
* [AMD OS X](https://forum.amd-osx.com/index.php?threads/ryzen-9-3900x-asrock-x570-itx-tb3-sapphire-rx-5500-pulse-catalina.19/)
* [Hackintosh Forum](https://www.hackintosh-forum.de/forum/thread/46160-ryzen-9-3900x-asrock-x570-itx-radeon-rx-5500-xt/)

Cedits and links:
* [OpenCore Desktop Guide](https://github.com/dortania/OpenCore-Desktop-Guide)
* [Aleksandar's USB mapping Guide](https://aplus.rs/2020/usb-mapping-why/)
* [OpenIntelWireless](https://github.com/OpenIntelWireless)
* [AGPM Injector](https://github.com/Pavo-IM/AGPMInjector)
* [trulyspinach's SMCAMDProcessor](https://github.com/trulyspinach/SMCAMDProcessor)
* [khronokernel's SmallTree](https://github.com/khronokernel/SmallTree-I211-AT-patch)
* [Hackintool](https://www.hackintosh-forum.de/forum/thread/38316-hackintool-ehemals-intel-fb-patcher/)
* [VoodooTSCSync Configurator](https://www.insanelymac.com/forum/files/file/744-voodootscsync-configurator/)
