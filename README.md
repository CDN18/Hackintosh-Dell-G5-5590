# Hackintosh-Dell-G5-5590

## Laptop Specs

### Dell G5 5590 a80p 

Processor: Core i7-9750H 6C/12T (Coffee Lake Plus / Coffee Lake Refresh) 
iGPU: Intel UHD 630 Graphics (Video Out using a **USB Type-C to DisplayPort adapter** into the USB 3.1 Gen 2 Type-C/DisplayPort 1.2/Thunderbolt 3 port)  
~~dGPU: nVidia GeForce RTX 2060~~ ~~(HDMI 2.0b port /mini DisplayPort 1.4)~~  
Display: 15.6 1080p (1920x1080) 144hz  
Memory: 16GB DDR4 2666MHz (8GBx2)  
Storage: 512GB Intel NVMe SSD + 1tb SSD (Crucial BX500)  
Audio: Realtek ALC3204-CG  
Wifi/Bluetooth: ~~Qualcomm QCA61x4A (DW1820)~~ Fenvi BCM94360NG (waiting for arrival)  
Ethernet: Killer GB E2500V2 10/100/1000 Mbps  
USB 3.1  
Thunderbolt    
Webcam + Microphone  

## About

This is my "Hackintosh Diary", will be using it to maintain a triple boot Dell G5 5590 a80p between macOS, Arch Linux and Windows.  

Using [Vanilla Laptop Guide](https://dortania.github.io/vanilla-laptop-guide/) from [Dortania](https://dortania.github.io/)

## INSTALLATION

Follow [Vanilla Laptop Guide](https://dortania.github.io/vanilla-laptop-guide/) and use MacBookPro16,1 SMBIOS (remember to insert your generated info at platform info section). Setup config.plist using [Coffee Lake Plus](https://dortania.github.io/vanilla-laptop-guide/OpenCore/config-laptop.plist/coffee-lake-plus.html)

**ACPI changes:**

- SSNC-PLUG - CPU0 into PP00 -> Processor ID on Dell G5 5590 is PP00

**DELL SPECIFICS config.plist:**

- RebuildAppleMemoryMap = False
- UpdateSMBIOSMode = Custom
- CustomSMBIOSGuid = True
- Audio layout-id = 15 

**Tweaks**

- At first kernel_task was using 100% CPU. The issue resolved when turned on Thunderbolt3 in BIOS settings. Need to activate thunderbolt in macOS, no drivers detected at system profiler.

## TODO

- Fix trackpad not working after sleep
- Fix USB3 ports not delivering more power than 500. Only Thunderbolt3/USB-C port delivers full power.
- SSD sometimes get very slow write speed. Looking for a fix (or maybe migrate macOS installation to NVME)
- Install Fenvi BCM94360NG Wi-Fi/Bluetooth card (waiting for arrival, using TP Link USB dongle for now)
- Review CPU frequencies. Right now minimum is 1.2ghz and maximum seems to be 4.0ghz
- Fix backlight keyboard control (only working via Settings -> Display)
- Quicktime and iTunes show artifacts while full screen. IINA runs fine, tested mp4, mkv and ts movies, maybe something to do with Apple decoding

## Future reading

Some features need improvement. I'm waiting for Fenvi BCM94360NG airport adapter to unlock remaining features.

- [ ] Backlight debug: follow [this](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/backlight-methods/manual.html)
- [ ] Share Bluetooth pairing between windows and mac: follow [this](https://www.reddit.com/r/hackintosh/comments/hjwu43/howto_share_a_bluetooth_pairing_headphones_etc/) (optional)
- [ ] Thunderbolt 3 Video out fix: follow [this](https://www.tonymacx86.com/threads/dell-g5-5590-thunderbolt-display-need-help.293776/)
- [ ] Follow [vanilla laptop guide post install](https://dortania.github.io/vanilla-laptop-guide/post-install/) to fix possible issues

## WORKING / NOT WORKING

- [x] iGPU Acceleration 
- [x] Backlight 
- [x] 144hz display 
- [ ] Thunderbolt 3 video out (Don't have a Thunderbolt->DisplayPort adapter yet)
- [ ] Ethernet
- [x] Bluetooth
- [ ] Apple Communications (Continuity, airdrop, airplay, etc)
- [ ] Wi-Fi
- [x] Apple Services
- [x] Keyboard
- [x] Keyboard backlight (RGB backlight works, but had to setup at Windows 10 Alienware Command Center. When boot at macOS the config remais, including color. Cannot turn off keyboard backlight in macOS for now. I found some information regarding sending information via USB to keyboard backlight, looking for a solution to inject colors)
- [x] Trackpad
- [x] Audio
- [x] Microphone
- [x] Webcam
- [x] USB3 ports 
- [ ] Card reader
- [x] Apple bootloader (OpenCanopy)

## TOOLS

- [OpenCore 0.5.9](https://github.com/acidanthera/OpenCorePkg)
- [gibMacOS](https://github.com/corpnewt/gibMacOS) - Download macOS vanilla install
- [MountEFI](https://github.com/corpnewt/MountEFI) - Mount EFI partitions
- [ProperTree](https://github.com/corpnewt/ProperTree) - Generate config.plist based on OC folder
- [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS) - Generate Mac serials

## File History

**July 09 2020** (v2)

Fake ethernet to make iCloud work, fixed battery status.

**July 09 2020**

First version fast and functional. AML files fixed to match notebook specs. Still working with drivers, need to bring back battery status.

**July 07 2020 - Initial**

### Recovery operating system 

- macOS Catalina 10.5.5 (full install)

### KEXTs

- AppleALC.kext ([1.5.0](https://github.com/acidanthera/AppleALC/releases))
- Lilu.kext ([1.4.5](https://github.com/acidanthera/Lilu/releases))
- NoTouchID.kext ([1.0.3](https://github.com/al3xtjames/NoTouchID/releases))
- VirtualSMC.kext ([1.1.4](https://github.com/acidanthera/virtualsmc/releases))
  - SMCBatteryManager.kext (1.0)
  - SMCLightSensor.kext (1.0)
  - SMCProcessor.kext (1.1.4)
  - SMCSuperIO.kext (1.1.4)
- VoodooI2C.kext ([2.4.3](https://github.com/VoodooI2C/VoodooI2C/releases))
  - VoodooI2CHID.kext (1.0)
- VoodooPS2Controller.kext ([2.1.5](https://github.com/acidanthera/VoodooPS2/releases))
- WhateverGreen.kext ([1.4.0](https://github.com/acidanthera/whatevergreen/releases))

### ACPI

- SSDT-AWAC.aml [SSDT-PNLF-CFL.dsl](https://github.com/acidanthera/OpenCorePkg/blob/master/Docs/AcpiSamples/SSDT-AWAC.dsl)
- SSDT-EC-USBX.aml [SSDT-EC-USBX.dsl](https://github.com/acidanthera/OpenCorePkg/blob/master/Docs/AcpiSamples/SSDT-EC-USBX.dsl)
- SSDT-GPI0.aml [SSDT-GPI0.dsl](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/decompiled/SSDT-GPI0.dsl)
- SSDT-PLUG.aml [SSDT-PLUG.dsl](https://github.com/acidanthera/OpenCorePkg/blob/master/Docs/AcpiSamples/SSDT-PLUG.dsl)
- SSDT-PNLF-CFL.aml [SSDT-PNLF-CFL.dsl](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/decompiled/SSDT-PNLF-CFL.dsl)

All dsl files downloaded and compiled 07 07 2020. 

