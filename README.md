# Hackintosh-Dell-G5-5590

## :computer: ​Laptop Specs

### Dell G5 5590 A70P/M70P/A80P/M80P [RTX 2060 or GTX 1660 Ti models]

Processor: Core i7-9750H 6C/12T (Coffee Lake Refresh) 
iGPU: Intel UHD 630 Graphics 
dGPU: ~~nVidia GeForce RTX 2060~~  (not supported)
Display: 15.6 1080p (1920x1080) 144hz  
Memory: 16GB DDR4 2666MHz (8GBx2)  
Storage: 512GB Intel NVMe SSD  
Audio: Realtek ALC3204-CG (ALC236)  
Wifi/Bluetooth: ~~Qualcomm QCA61x4A (DW1820)~~ (not supported, replaced with card described below:)
Ethernet: Killer GB E2500V2 10/100/1000 Mbps  
Thunderbolt 3 / Webcam / Microphone / Card Reader    

##### EXTRA HARDWARE USED:

Storage: 1tb SSD (Crucial BX500) (I recommend a second disk to dual boot without issues)  
Wifi/Bluetooth: Fenvi BCM94360NG (ordered m2 card at aliexpress from Fenvi Store)  



## INSTALLATION

Tested with RTX 2060 and GTX 1660 Ti versions, both [share same hardware specs](https://topics-cdn.dell.com/pdf/g-series-15-5590-laptop_setup-guide_pt-br.pdf) (Thunderbolt 3 port)

### BIOS VERSION: Tested with **1.13.2** and **1.14.0** (Check your BIOS version before anything)

- Read [official guide](https://dortania.github.io/OpenCore-Install-Guide/) to understand stuff, not needed, but it's nice to understand what you're doing



#### BIOS SETUP:

- General -> Advanced Boot Options -> Uncheck "Enable Legacy Options ROMs"
- General -> UEFI Boot Path Security -> Check "Never"
- System Configuration -> Integrated NIC -> Uncheck "Enable UEFI Network Stack". Check option "Enabled"
- System Configuration -> SATA Operation -> Check "AHCI"
- System Configuration -> SMART Reporting -> Check "Enable SMART Reporting"
- System Configuration -> USB Configuration -> Check "Enable USB Boot Support" and "Enable External USB Port"
- System Configuration -> Thunderbolt Adapter Configuration -> Check "Enable Thunderbolt Technology Support" and "Security Level - No Security"
- System Configuration -> Thunderbolt Auto Switch -> Uncheck "Auto Switch" and Check "BIOS Assist Enumeration"
- System Configuration -> USB Power Share -> Uncheck "Enable USB PowerShare"
- Security -> Computrace -> Check "Deactivate"
- Secure Boot -> Secure Boot Enable -> Uncheck "Secure Boot Enable"
- Intel Software Guard Extensions -> Intel SGX Enable -> Check "Disabled"
- Performance -> Intel Speedstep -> Check "Enable Intel SpeedStep"
- Performance -> C-States Control -> Check "C states"
- Performance -> Intel TurboBoost -> Check "Enable Intel TurboBoost"
- Performance -> HyperThread control -> Check "Enable"
- Power Management -> Enable Intel Speed Shift Technology -> Check "Enable Intel Speed Shift Technology"
- Power Management -> USB Wake Support -> Uncheck "Enable USB Wake Support"
- Virtualization Support -> Virtualization -> Check "Enable Intel Virtualization Technology"
- Virtualization Support -> VT for Direct I/O -> Uncheck "Enable VT for Direct IO"



#### OPENCORE SETUP:

- Choose EFI folder:
  - Debug: OpenCore will load in debug mode and print every step on screen and on file (located on EFI partition of USB disk)
  - Verbose: OpenCore with text only partition picker
  - OpenCanopy: OpenCore with GUI partition picker
  - OpenCanopy AudioDxe: OpenCore with GUI partition picker and boot chime sound on boot
  - Legacy BOOTx64: Use this if you don't want to change OpenCore entry on BIOS. I recommend using a version without BOOTx64.efi, loading OpenCore.efi directly. **Read section Updating from OpenCore 0.6.8**
- Open config.plist and make some changes:
  - If you want to enable verbose mode during installation, go to NVRAM->Add->7C436110-AB2A-4BBB-A880-FE41995C9F82, and insert **-v** on **boot-args**. To disable verbose mode, just remove -v parameter.
  - Generate your MacBookPro15,2 serials using [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS/) and insert it into your config.plist (under PlatformInfo->Generic). You need to update MLB, SystemSerialNumber and SystemUUID. 
  - It's recommended to [disable CFG Lock in bios using MobGrubShell](https://dortania.github.io/OpenCore-Post-Install/misc/msr-lock.html). If you disabled it, don't change the lines below. In case you don't want to mess with it, you have to change 2 properties under Kernel->Quirks:
    - AppleCpuPmCfgLock to **YES** or **1** 
    - AppleXcpmCfgLock to **YES** or **1**
    - If you want to disable CFG Lock and don't know how to dump your BIOS, use **setup_var_3 0x5C4 0x00** during the steps of [disabling CFG Lock in bios using MobGrubShell](https://dortania.github.io/OpenCore-Post-Install/misc/msr-lock.html). **only use it if your BIOS is in version 1.13.2 or 1.14.0, Dell could change this location on future updates.** 
    - **PLEASE DON'T USE THIS COMMAND IF YOU DON'T HAVE A DELL G5 5590 WITH THE SPECS LISTED ABOVE, YOU COULD BRICK YOUR MACHINE.**
  - After installation you can make the default OpenCore selection by pressing Ctrl+Enter on the partition you want
  - You can also disable OpenCore boot picker under Misc->Boot->ShowPicker (change to NO).
  - **Remember to keep an USB copy of your EFI folder.** Normally I use one pen drive for tests, one pen drive with a working OpenCore version + macOS installer and last stable version on my SSD EFI Folder.

### Updating from OpenCore 0.6.8

If you are updating OpenCore fom 0.6.5 and below to 0.6.6 and above, some extra steps need to be made in order to boot. OpenCore is now standalone and don't use Bootstrap.efi anymore. Also Bootstrap.efi has been replaced with LauncherOption. Check [Updating Bootstrap in 0.6.6](https://dortania.github.io/OpenCore-Post-Install/multiboot/bootstrap.html#updating-bootstrap-in-0-6-6) for more information.

With these changes, BOOTx64.efi is no longer necessary. This is great news, other OSs will no longer mess with OpenCore by replacing BOOTx64.efi. With OpenCore 0.6.8 I kept BOOTx64.efi. With 0.6.9 release I made some fine tunes and removed the BOOT folder.

In order to boot the new version for the first time, follow the steps below:

- Enter BIOS
- Go to General -> Boot Sequence
- Select current OpenCore boot option and click "Delete Boot Option"
- Click "Add Boot Option"
- In the first field (Boot Option Name), put the name you want to identify during boot (Ex.: OpenCore)
- The box in the middle (File System List) show all your partitions. Click on "...". The first partition will appear. Look for an EFI folder in the left. If you don't see it, select another drive on File System selector.
- If you find an EFI partition, navigate to EFI\OC and select OpenCore.efi. If you don't see the OC folder, select another drive on File System selector. After selecting OpenCore.efi, click Ok.
- Click OK again to confirm. Check if the File Name points to \EFI\OC\OpenCore.efi and if the correct partition is selected on File System List
- You will see the OpenCore option at the boot list. You can rearrange and put OpenCore at the top.
- Click Apply and Exit.
- Pressing F12 during Dell logo will show OpenCore. Select it to boot.
- **If you reset NVRAM, the steps above will be erased and you may need to add OpenCore.efi again**

If you don't feel confident to erase BOOTx64.efi, i'm packing an EFI folder with it (EFI Legacy), but recommend you follow the steps above.



## KNOWN ISSUES

- Music.app don't work with DRM videos.
- Sometimes unplugging/replugging quickly causes laptop to crash. To avoid this issue, put laptop to sleep before plug or unplug the power chord. I'm revisiting all ACPI settings, but no luck so far.



## WORKING

:white_check_mark: iGPU Acceleration    
:white_check_mark: Native brightness control (Thanks [@caiomascarin](https://github.com/caiomascarin))   
:white_check_mark: 144hz display   
:white_check_mark: Apple Services  
:white_check_mark: Keyboard with backlight (RGB backlight works, but had to setup at Windows 10 Alienware Command Center. When boot at macOS the config remains, including color. I'm able to work with Alienware Command Center using VMware Fusion to boot Windows 10 partition (as Boot Camp) and connecting Alienware AW-ALC in Virtual Machine -> USB & Bluetooth. Had to reboot a few times in order to work for the first time.  
:white_check_mark: Trackpad with multitouch gestures  
:white_check_mark: Speakers  
:white_check_mark: Microphone  
:white_check_mark: Webcam  
:white_check_mark: USB 2/3/C ports   
:white_check_mark: Card reader  
:white_check_mark: CFG Lock disabled  
:white_check_mark: [NVRAM](https://dortania.github.io/OpenCore-Post-Install/misc/nvram.html#verifying-if-you-have-working-nvram)  
:white_check_mark: Bluetooth - Using Fenvi BCM94360NG (Original card worked with bluetooth out of box too)  
:white_check_mark: USB-C video out  (using generic USB-C to HDMI adapter)  
:white_check_mark: Ethernet (Thanks [@radaelilucca](https://github.com/radaelilucca))  
:white_check_mark: :warning: Apple Communications (Continuity, airdrop, etc) - Using Fenvi BCM94360NG (Original card didn't provide support)  
:white_check_mark: :warning: Wi-Fi - Using Fenvi BCM94360NG (Original card don't work on macOS)    
:white_check_mark: :warning: Thunderbolt 3 (can see device in Hackintosh but don't have any Thunderbolt peripheral to test)



If you like this guide and want to help with any value, please buy me a coffee :coffee:

[![paypal](https://www.paypalobjects.com/en_US/i/btn/btn_donateCC_LG.gif)](https://www.paypal.com/cgi-bin/webscr?cmd=_donations&business=N7EY58HHR9RUQ)



## CHANGELOG

**May 03 2021** (v2.6)

- Updated to OpenCore 0.6.9 and corresponding kexts (https://dortania.github.io/hackintosh/updates/2021/05/03/acidanthera-may.html). 
- Removed BOOTx64.efi from EFI folder. I put a "legacy" EFI folder with BOOT available
- Cleaned up some unnecessary framebuffer patches
- Kexts_Extra folder (containing CPUFriendFriend profiles) inside OC moved to UTIL folder. If you want a different profile, just replace CPUFriendDataProvider.kext inside OC/Kexts
- Removed some unnecessary old stuff in UTIL folder
- Removed some unnecessary kexts
- Made some changes on ACPI patches
- Add information about upgrade OC and run without BOOTx64.efi
- Updated USBPorts.kext with Thunderbolt 3 ports
- Add BIOS setup options

**April 28 2021** (v2.5.3)

- Fixed audio not playing on boot issue. You may need to reset NVRAM the first time booting with AudioDxe EFI in order to set the correct volume.
- Added OpenCore 0.6.8 DEBUG version

**April 28 2021** (v2.5.2)

- Added AudioDxe to play boot chime
- Split EFI into 3 versions: Verbose (text boot picker), OpenCanopy (GUI boot picker) and OpenCanopy + AudioDxe (GUI boot picker and play boot chime like a real mac)
- Download for each version is avaliable on [Releases](https://github.com/leocg/Hackintosh-Dell-G5-5590/releases/) tab

**April 27 2021** (v2.5.1)

- Updated VoodooI2C, VoodooInput and VoodooPS2Controller to latest version
- Added OpenCanopy to render visual boot picker
- Updated macOS from 11.1 (20C69) to 11.3 (20E232) without issues using Apple Software Update and OpenCore 0.6.8 EFI.

**April 27 2021** (v2.5)

- Updated to OpenCore 0.6.8 and corresponding kexts (https://dortania.github.io/hackintosh/updates/2021/04/05/acidanthera-april.html). 
- Updated CPUFriend to 1.2.3
- Kept Voodoo files (VoodooI2C, VoodooInput and VoodooPS2Controller) on older versions cause newer one is causing issues with trackpad. I'll update the repo when I solve the problem.

**April 27 2021** (v2.4)

- Updated to OpenCore 0.6.7 and corresponding kexts (https://dortania.github.io/hackintosh/updates/2021/03/01/acidanthera-march.html). 

**April 27 2021** (v2.3)

- Updated to OpenCore 0.6.6 and corresponding kexts (https://dortania.github.io/hackintosh/updates/2021/02/01/acidanthera-february.html). 

**April 27 2021** (v2.2)

- Updated to OpenCore 0.6.5 and corresponding kexts (https://dortania.github.io/hackintosh/updates/2021/01/04/acidanthera-january.html). 
- Added enable-backlight-registers-fix property to fix backlight issues with new WhateverGreen
- Updated broken links from instructions (Thanks [@rafaeldgoliveira](https://github.com/rafaeldgoliveira)))

**December 09 2020** (v2.1)

- Updated to OpenCore 0.6.4 and kexts to latest version.
- Fixed native brightness control (Thanks [@caiomascarin](https://github.com/caiomascarin)) 
- Added SSDT-Swap-CommandOption.aml because VoodooInput removed this quirk in latest version
- Added HibernationFixup.kext because I'm testing some issues waking up on battery (sometimes CPU fixes on max clock)

**November 19 2020** (v2.0)

- Updated to OpenCore 0.6.3 and kexts to latest version. Tested with Big Sur for 2 weeks without issues.

**September 12 2020** (v1.7.0)

- Fixed dGPU issues after wake, keeping it disabled at all times
- Fixed video out using USB-C to HDMI adapter changing SMBIOS to MacBookPro15,2
- Added custom CPU profile tuned for high performance (there's another kext with CPU profile focused on power savings inside Kexts_Extra folder. Replace the version you want with original CPUFriendDataProvider.kext, remember to rename)
- Removed OpenCore bootpicker. After Dell logo go directly to Apple logo. Change Misc->Boot->ShowPicker to YES if you need the bootpicker.
- Updated VoodooI2C (2.4.4) and VoodooPS2Controller (2.1.6)
- Updated Kexts:
  - VoodooPS2Controller-2.1.6-RELEASE
  - VoodooI2C-2.4.4

**September 07 2020** (v1.6)

- Updated OpenCore to version 0.6.1 RELEASE
- Updated Kexts:
  - WhateverGreen-1.4.2-RELEASE
  - Lilu-1.4.7-RELEASE
  - VirtualSMC-1.1.6-RELEASE
  - AppleALC-1.5.2-RELEASE

**September 04 2020** (v1.5)

- Working Ethernet using RealtekRTL8111.kext (Changed OSBundleRequired from Network-Root to Root inside kext's Info.plist)
- Some kexts are development version instead of release cause resolved some issues. Next week I'll put release version when Acidanthera commit.
- I'm not using current Voodol2C/VoodooPS2Controller kext because latest version broken multitouch support with this laptop.

**August 03 2020** (v1.4) 

- Removed NullEthernet.kext (don't need anymore cause installed Fenvi Wi-Fi card)
- Updated OpenCore to version 0.6.0
- Updated Kexts:
  - NoTouchID-1.0.4-RELEASE
  - WhateverGreen-1.4.1-RELEASE
  - Lilu-1.4.6-RELEASE
  - VirtualSMC-1.1.5-RELEASE
  - AppleALC-1.5.1-RELEASE
  - VoodooPS2Controller-2.1.6-RELEASE
  - NVMeFix-1.0.3-RELEASE

**August 02 2020** (v1.3) 

- Made some tweaks all around. Laptop is stable, good battery life and performance. 

**July 31 2020** (v1.2) 

- Switched stock wifi card with Fenvi BCM94360NG, worked OOB with Catalina 10.15.6.

**July 23 2020** (v1.2) 

- Added Dell Sensors combatibility with VirtualSMC (using compiled version, not release). Use with HWMonitorSMC2 to view all sensors and fan speed. iStat Menus don't display any information.

**July 19 2020** (v1.2) 

- Minor adjustments

**July 17 2020** (v1.1) 

- Fixed USB mapping with USBInjectAll.kext + SSDT-UIAC.dsl generated by Hackintool

**July 16 2020** (v1.1) 

- USB mapped
- Fixed error that disconnecting device from USB-C port cause high usage of kernel_task. USB-C is not working with USB3 for now.
- I think dGPU is turning on after sleep cause of temperature changes and estimated battery. Before sleep idles between 45-50. After wake idles at 55-60 degrees.

**July 14 2020** (v1.0) - STABLE

- Disabled dGPU using [Optimus Method](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/laptop-disable.html#optimus-method)
- Fixed double call for VoodooInput
- Removed SSDT-AWAC, DSDT already had variable and errors were shown on boot
- Removed -wegnoegpu in order to utilize Optimus Method. Battery life up from 50m to 3h.
- Removed NVMeFix.kext. Trying to run as vanilla as possible.
- Remapped all FN keys using Karabiner Elements
- Testing different CPU Friend configs. So far the Vanilla one is the best option
- Removed -v argument and disable all OpenCore debug tools
- Linked Windows bootloader cause already lost windows EFI twice during hackintosh tweaks.

**July 13 2020** (v0.4)

CFG Lock disabled in bios and removed Quirks from config.plist

**July 13 2020** (v0.3)

Fixed trackpad issues. Preparing machine for disabling CFG Lock and USB Mapping. 

**July 09 2020** (v0.2)

Fake ethernet to make iCloud work, fixed battery status.

**July 09 2020**

First version fast and functional. AML files fixed to match notebook specs. Still working with drivers, need to bring back battery status.

**July 07 2020 - Initial**







## About

This is my "Hackintosh Diary", will be using it to maintain a triple boot Dell G5 5590 a80p between macOS, Arch Linux and Windows.  

Don't directly use my EFI folder, your computer won't boot cause it has CFG Lock disabled and other post install stuff.

Vanilla Boot could be accomplished using OPENCORE USB BOOT folder. Use this folder to make a bootable USB disk with macOS Catalina (tested with 10.5.5).

Used [Vanilla Laptop Guide](https://dortania.github.io/vanilla-laptop-guide/) from [Dortania](https://dortania.github.io/), but laptop and desktop guide was merged into [OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/).

Most work is done post installation, be prepared to read a lot. CFG Lock is difficult to understand but very simple to execute. Disabling CFG Lock and dGPU using [Optimus Method](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/laptop-disable.html#optimus-method) has major impact on battery life.

Follow [Vanilla Laptop Guide](https://dortania.github.io/vanilla-laptop-guide/) and use MacBookPro16,1 SMBIOS (remember to insert your generated data at platform info section). Setup config.plist using [Coffee Lake Plus](https://dortania.github.io/vanilla-laptop-guide/OpenCore/config-laptop.plist/coffee-lake-plus.html). Read every guide very carefully BEFORE start and know what you're going to do beforehand.

## Config

**ACPI changes:**

- SSNC-PLUG - CPU0 into PP00 -> Processor ID on this machine is PP00

**DELL SPECIFICS config.plist:**

- UpdateSMBIOSMode = Custom
- CustomSMBIOSGuid = True
- Audio layout-id = 15 (other channels worked, but 15 offered better stability and sound quality overall)

**Patching CFG Lock**

- Download BIOS from Dell website (version must be the same, variable can change after updates). 
- Dump bios with DecompNewDell.py (used Python3)
- Open dumped bios with UEFITool
- Search for "CFG Lock, VarStoreInfo (VarOffset/VarName)". Variable name come just after (in my case, using BIOS 1.13.2, was 0x5C4)

`One Of: CFG Lock, VarStoreInfo (VarOffset/VarName): 0x5C4, VarStore: 0x1 [...]

The steps above I followed to find the correct CFG Lock variable name. After that, simply boot into Modified GRUB Shell and change variable from 0x5C4 to 0x00:

- Prepare EFI Boot Disk using [Disabling CFG Lock](https://dortania.github.io/OpenCore-Desktop-Guide/extras/msr-lock#disabling-cfg-lock) instructions and patch using **setup_var_3 0x5C4 0x00**

**If you don't feel confortable messing with bios setting, just change the settings below in your config.plist:**

- AppleCpuPmCfgLock -> YES 
- AppleXcpmCfgLock -> YES 

**Generating serial numbers:**

Use [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS/) script to generate your device serial number. Put it into PlatformInfo->Generic (MLB, SystemSerialNumber and SystemUUID).