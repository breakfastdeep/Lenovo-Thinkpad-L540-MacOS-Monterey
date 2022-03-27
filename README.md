# macOS Monterey on Lenovo Thinkpad L540 

<img align="center" src="https://user-images.githubusercontent.com/69075167/160294481-bdc4d1c7-1e0d-4c2d-bf86-251f9851dd9a.png" alt="OpenCore Bootmenu on L540">


# Lenovo ThinkPad L540 - OpenCore Configuation

<img align="right" src="https://user-images.githubusercontent.com/69075167/160275522-296f6454-cea5-4831-99a9-97289d769a9a.jpg" alt="macOS Monterey running on the L540" width="500">

[![macOS](https://img.shields.io/badge/macOS-Monterey-brightgreen.svg)](https://developer.apple.com/documentation/macos-release-notes)
[![OpenCore](https://img.shields.io/badge/OpenCore-0.7.9-blue)](https://github.com/acidanthera/OpenCorePkg)
[![License](https://img.shields.io/badge/license-MIT-purple)](/LICENSE)

<p align="left">
   <strong>Status: Maintained</strong>
   <br />
   <strong>Version: </strong>1.0.0
   <br />
   <a href="https://github.com/breakfastdeep/L540-Monterey/releases"><strong>Download now »</strong></a>
   <br />
   <a href="https://github.com/breakfastdeep/L540-Monterey/issues">Report Bug</a>
   ·
   <a href="https://github.com/breakfastdeep/L540-Monterey/blob/main/CHANGELOG.md">View Changelog</a>
   ·
  </p>
</p>
</br>

## ⚠️ Disclaimer
This guide is only for the Lenovo ThinkPad L540. I am NOT responsible for any harm you cause to your device. This guide is provided "as-is" and all steps taken are done at your own risk.


## Introduction

<details>  
<summary><strong>📖 Important instructions</strong></summary>
</br>

**Migrate to Version 1.7.0 and higher**

The Version 1.7.0 changes the model from ```MacBookPro12,1``` to ```MacBookPro11,4```. You need to generate a new SMBIOS for the new model with [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS).


The reason: **ACPI Patches**

These ACPI patches are necessary for booting macOS. OpenCore doesn't only patch macOS, but also Windows and Linux. These operating systems handle the patches differently than macOS. If you decide to use OpenCore with other systems, you may expect some bugs like missing battery and WiFi issues.

**EFI folders**

This repo includes multiple EFI configuations for different macOS Versions.

| EFI               | Description                                              | Type      |
| ----------------- | -------------------------------------------------------- | --------- |
| `EFI - Monterey`  | Supports only macOS Monterey                             | `Stable`  |



<a href="https://github.com/breakfastdeep/L540-Monterey/blob/main/DIFFERENCES.md"><strong>
Differences to the EFI folders »</strong></a> <br/> <a href="https://github.com/OpenIntelWireless/HeliPort/releases"><strong>
Download HeliPort app »</strong></a>


</details>

<details>
<summary><strong>💻 My Hardware</strong></summary>
<br>
These are the Hardware component I use. But this OpenCore configuation **should still work** with your device, even if the components are not equal.

| Category  | Component                            |
| --------- | ------------------------------------ |
| CPU       | Intel Core i5-4300M                 |
| GPU       | Intel HD Graphics 4600               |
| SSD       | Trendmicro M2 SSD 240GB               |
| SSD       | Samsung EVO SSD 240GB               |
| SSD       | Intenso SSD PLUS 240GB               |
| Memory    | 8GB DDR3 1600Mhz                     |
| Camera    | 720p Camera                          |
| WiFi & BT | Intel Wireless-N 7260                |

</details>  
 
</details>

## Installation

<details>  
<summary><strong>📝 Requirements</strong></summary>
</br>

You must have the following items:
- Lenovo ThinkPad L540.
- Access to a working Windows machine or macOS with Python installed.
- A pendrive with more than 4 GB (Keep in mind, during the preperation we will format the disk to create the install media).
- an Internet connection via Ethernet.
- 1-2 hours of your time.

</details>

<details>  
<summary><strong>⚙️ Preperation</strong></summary>
</br>

### Create the install media for Windows

First of all, you will need the install media of macOS. I will use [macrecovery](https://github.com/acidanthera/OpenCorePkg) to download and create the macOS Install media.

With macrecovery, the process is the following:
- Download [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg) as a ZIP.
- Extract the OpenCorePkg-master.zip file.
- Open ```cmd.exe``` with Administrator privileges and change the directory to OpenCorePkg-master\Utilities\macrecovery.
- Enter the following command to download macOS:
```

# Monterey (12)
python macrecovery.py -b Mac-E43C1C25D4880AD6 -m 00000000000000000 download
```
- After the download succeeded, type ```diskpart``` and wait until you see ```DISKPART>```

- Plug-in your pendrive and type ```list disk``` to see your disk id.

- Select your pendrive by typing ```select disk <diskid>```

- Now we are gonna clean the pendrive and convert it to GPT. First, type ```clean``` and then ```convert gpt```.

>  **Note**: If an error occurred, try to convert again by typing ```convert gpt```.

- After the pendrive is clean and converted, we will create a new partition where we can put our files on. First, type ```create partition primary```, then select the new partition with ```select partition 1``` and format it ```format fs=fat32 quick```.

- Finally, mount your pendrive by typing ```assign letter=J```

- Now, close the Command Prompt and create the folder ```com.apple.recovery.boot``` on the pendrive. Copy ```OpenCorePkg-master\Utilities\macrecovery\BaseSystem.dmg``` and ```Basesystem.chunklist``` into that folder.

>  **Note**: If you can't find BaseSystem.dmg, use RecoveryImage.dmg and RecoveryImage.chunklist instead.

After the install media was created, we need to make the USB drive bootable.

### Create the install media for macOS

- Download OpenCore Legacy Patcher (https://github.com/dortania/OpenCore-Legacy-Patcher/releases)
- Follow the Steps on the official Page (https://dortania.github.io/OpenCore-Legacy-Patcher/INSTALLER.html#creating-the-installer)

### Configure and install OpenCore
Download the EFI folder from this repo, you will find the latest files under the release tab or just download the repo as it is. Move the folder to the root of your pendrive (e.g. J:\) and rename the folder to ```EFI```.

#### GenSMBIOS
We need a script, called [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS), to create fake serial number, UUID and MLB numbers. **This step is essential to have working iMessage, so do not skip it!**

The process is the following:

- Download GenSMBIOS as a ZIP, then extract it.
- Start GenSMBIOS.bat and use option 1 to download MacSerial.
- Choose option 2, to select the path of the config.plist file. It will be located in EFI -> OC folder.
- Choose option 3, and enter ```MacBookPro11,4``` as the machine type.
- Press Q to quit. Your config now should contain the requied serials.

#### Enter the proper ROM value
After adding serials to your config.plist, you have to add the computer's MAC address to the config.plist file. **This step is also essential to have a working iMessage, so do not skip it.** We need a Plist editior, to write the MAC address into the config.plist file. I used [ProperTree](https://github.com/corpnewt/ProperTree), since it works on Windows too. You have to change the MAC address value in the config.plist at

```PlatformInfo -> Generic -> ROM```

Delete the generic ```112233445566``` value, and enter your MAC address into the field, without any colons. Save the Plist file, and it is now ready to be written out to the EFI partition of your install media.

#### Default keyboard layout and language
The default keyboard layout and language is ```German```. To change the language, edit the value of ```NVRAM -> Add -> 7C436110-AB2A-4BBB-A880-FE41995C9F82 -> prev-lang:kbd``` to the value of your language. If your value contains an underscore ```_```, replace it with a hyphen ```-```. The value for English would be ```en-US:0```. You can find a list of all language values [here](https://github.com/acidanthera/OpenCorePkg/blob/master/Utilities/AppleKeyboardLayouts/AppleKeyboardLayouts.txt).


#### MultiBoot and Making Own Boot Entries
The default ScanPolicy value is ```0```. To make your own boot entries with own logos, edit the value of ```MISC -> Security -> ScanPolicy -> 19858179``` to the value of your boot order (shows only OS what you add in ```MISC ->  -> Entries -> ...```). You can find the a guide [here](https://dortania.github.io/OpenCore-Multiboot/oc/win.html) 

### Install OpenCore
After you've finished with the neccesary tweaks, you have to copy the EFI folder to the EFI partition of your pendrive.

</details>

<details>  
<summary><strong>🚚 Installation</strong></summary>
</br>

### Prepare BIOS
The bios must be properly configured prior to installing macOS.
In Security menu, set the following settings:

-  `Security > Security Chip`: must be **Disabled**
-  `Memory Protection > Execution Prevention`: must be **Enabled**
-  `Internal Device Access > Bottom Cover Tamper Detection`: must be **Disabled**
-  `Anti-Theft > Current Setting`: must be **Disabled**
-  `Anti-Theft > Computrace > Current Setting`: must be **Disabled**
-  `Secure Boot > Secure Boot`: must be **Disabled**

In Startup menu, set the following options:

  
-  `UEFI/Legacy Boot`: **Both**
-  `UEFI/Legacy Priority`: **UEFI First**
-  `CSM Support`: **Yes**

Now you can go through the install.

### Install macOS
1. Boot from USB, press ```SPACE``` and select the USB drive inside of OpenCore ```"NO NAME (DMG)" or similar```.
>  **Note:** The first boot may take up to 20 minutes.
2. Wait for the macOS Utilities screen.
3. Select Disk Utility, select your disk and click erase. Give a name and choose **APFS** with **GUID Partition Map**.
4. After erasing, go back and select **Reinstall macOS** and follow the steps on your screen. The installation make take up to **2 hours**.
>  **Note:** Your PC will restart multiple times. Just boot from USB and select your disk inside of OpenCore. (named macOS Installer or the disk name).
5. Once you see the `Region selection` screen, you are good to proceed.
6. Create your user accound and everything else.

</details>

<details>  
<summary><strong>♻️ Upgrade macOS / Switch EFI</strong></summary>
</br>

If you plan to upgrade your macOS (or updating the EFI / switching to HeliPort), you'll need a different OpenCore configuation (EFI). Please follow these steps:

> Note: Download the desired macOS version in the Settings before following these steps, if you are connected via WiFi.

1. Download the newest release & [ProperTree](https://github.com/corpnewt/ProperTree) and extract it.
2. Start ProperTree and load the ```Config.plist``` on your EFI partition. (File -> Open)
> Note: You can mount your EFI partition by pressing ```WIN + SPACE```, typing Terminal and enter the following command: ```sudo diskutil mountDisk disk0s1```.
3. Now also load the new configuration file from the repo for the desired macOS installation (or HeliPort config). 
4. You should now have 2 ProperTree-windows open on your screen.
5. Go in both windows to ```Root -> PlatformInfo -> Generic```. Transfer ```MLB, ROM, SystemProductName, SystemSerialNumber and SystemUUID``` to the new config. 
6. Save the new config (File -> Save) and close both windows.
7. Now delete your existing EFI folder from the EFI partition and copy the new one to it. (Make sure that the Directorys ```Boot and OC``` are in ```EFI```).

If you want to upgrade macOS, download the desired macOS version in the Settings app and perform the upgrade like on a real Mac.

</details>


## Post-install (optional)

<details>  
<summary><strong>💾 Install OpenCore to Hard drive</strong></summary>
</br>

1. Press `WIN + SPACE` and open terminal. Type `sudo diskutil mountDisk disk0s1` (where disk0s1 corresponds to the EFI partition of the main disk)
2. Open Finder and copy the EFI folder of your USB device to the main disk's EFI partition.
3. Unplug the USB device and reboot your laptop. Now you can boot macOS without your USB device.

</details>



<details>  
<summary><strong>✏️ Create a offline install media (Optional)</strong></summary>
</br>

In case of reinstalling macOS, a offline install media can save some time. You also don't need an Ethernet connection for the installation.
To create a offline install media, you need the following stuff: 

- macOS Installer from the App Store.
- A 16 GB pendrive (Keep in mind, during the preperation we will format the disk to create the install media).

Press `WIN + SPACE` and open Disk utility. Select your USB device and click erase. Name it `MyUSB` and choose **Mac OS Extended** with **GUID Partition Map**. After erasing the USB device, close Disk utility.

Now press `WIN + SPACE` and open terminal. Type the following command:

Monterey:
```sudo /Applications/Install\ macOS\ Monterey.app/Contents/Resources/createinstallmedia --volume /Volumes/MyUSB --downloadassets```

After creating the install media, copy your EFI folder to the EFI partition of your USB device.


</details>

## Status

<details>  
<summary><strong>✅ What's working</strong></summary>
</br>
 
- [X] Intel WiFi & Bluetooth (thanks to [itlwn](https://github.com/OpenIntelWireless/itlwm))
- [X] Brightness / Volume Control 
- [X] Battery Information
- [X] Audio (Apple ALC Speaker)
- [X] USB Ports & Built-in Camera
- [X] Graphics Acceleration
- [X] Trackpoint / Touchpad
- [X] Power management / Sleep
- [X] FaceTime / iMessage (iServices)
- [X] DisplayPort
- [X] DVD Drive
- [X] Dock USB / Display
- [X] Handoff / Universal Clipboard
- [X] Sidecar (Cable) / AirPlay to Mac
- [X] SIP / FireVault 2
- [X] Hotkeys via YOGASMC (need to download YogaSMCPane)


</details>

<details>  
<summary><strong>⚠️ What's not working</strong></summary>
</br>

- [ ] Safari DRM ```Use Chromium powered Browser or Firefox to watch Amazon Prime Video, Netflix, Disney+ and others```
- [ ] Audio Jack (noisy)
- [ ] VGA
- [ ] Sleep with Docking Station
- [ ] Realtek Card Reader ``` it works but shows a warning on statusbar because of unsigned class``` 

</details>

<details>  
<summary><strong>🔄 Not tested</strong></summary>
</br>

- [ ] Sidecar Wireless
- [ ] Apple Watch Unlock
- [ ] WWAN

</details>

## ⭐️ Feedback
Did you find any bugs or just have some questions? Feel free to provide your feedback using the Discussions tab.

## 📜 License

This repo is licensed under the [MIT License](https://github.com/valnoxy/t440p-oc/blob/main/LICENSE).

The following files are licensed under the [MIT License](https://github.com/valnoxy/t440p-oc/blob/main/LICENSE):
- FixShutdown-USB-SSDT ```(Fixing the Shutdown/Sleep Problems of USB```
- SSDT-ALS0 ```(Fake ambient light sensor)```
- SSDT-ECRW ```(ACPI driver for OEM hardware (YogaSMC))```
- SSDT-HPET ```(IRQ Conflicts fix (Needs _CRS to XCRS Rename))```
- SSDT-PLUG ```(Plugin type to 1 for CPU0/PR00)```
- SSDT-PNLF ```(PNLF device for WhateverGreen)```
- SSDT-THINK ```(ThinkVPC (YogaSMC))```
- UTPMap.kext (USB Mapping for ```MacBookPro11,4```)

OpenCore is licensed under the [BSD 3-Clause License](https://github.com/acidanthera/OpenCorePkg/blob/master/LICENSE.txt).


---
```Copyright (c) 2022 breakfastdeep. <muhteremking@gmail.de>```
