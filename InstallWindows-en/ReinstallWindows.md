# Reinstall Windows on Xiaomi Mi 9

## Files/Tools Needed 📃

- You will need the following files from the [BSP Release page](https://github.com/woa-vayu-archive/POCOX3Pro-Releases/releases/latest):

UEFI Image:

| File Name                              | Target Device         |
|----------------------------------------|-----------------------|
| MuCepheusSecureBoot.img                | Xiaomi Mi 9           |

Windows Drivers:

| File Name                                       | Target Device         |
|-------------------------------------------------|-----------------------|
| XiaoMi9-Drivers-Desktop.zip                     | Xiaomi Mi 9           |
| XiaoMi9-Drivers-Desktop.7z                      | Xiaomi Mi 9           |

- TWRP image:

Twrp image supports Android™ 11 encryption

| File Name                                       | Target Device         |
|-------------------------------------------------|-----------------------|
| [twrp-cepheus.img](https://github.com/woacepheus/Port-Windows-11-XiaoMI-9/releases/download/recoveries/twrp-cepheus.img) | Xiaomi Mi 9 |

- [Platform Tools from Google (ADB and Fastboot)](https://developer.android.com/studio/releases/platform-tools)
- An ARM64 Windows build of your choice that meets the minimum system requirements (specifically the install.wim file). You can use [UUPMediaCreator](https://github.com/gus33000/UUPMediaCreator) for this. [Here's a guide on how to use it.](/InstallWindows/ISO/GetWindows.md)
- A Windows PC to build the Windows ISO, apply it onto the phone from mass storage, add drivers to the installation, configure ESP

## Disclaimers

> [!WARNING]
> - Don't create partitions from Mass Storage Mode on Windows (because ABL will break with blank/spaces in names), your phone may be irrecoverable otherwise
> - If you see a warning and/or error during the process, it is not normal. Contact us on telegram if you see anything odd, but do not continue or proceed on your own, you will break things further.
> - Don't rerun the commands if you interrupt the process. You may break your partition table.
> - Do not run all commands at once.
> - Do not commit *any* typo with *any* commands.
> - Be familiar with command line interfaces.

> [!IMPORTANT]
> **THIS WILL WIPE ALL YOUR WINDOWS DATA**
>
> We don't take any responsibility for any damage done to your phone. By following this guide, you agree to take full responsibility of your actions. We have done some testing,
>
> but this is **STILL IN PREVIEW** and things can go wrong.

**PLEASE READ AND BE SURE TO UNDERSTAND THE ENTIRE GUIDE BEFORE STARTING**

# Steps 🛠️

## Acquiring all files

<details>
    <summary>Here's how to acquire the Android SDK Platform Tools: <b>Click to expand</b></summary>
    <p>


First, start by going to the [Android Platform SDK download page](https://developer.android.com/studio/releases/platform-tools) on your computer.

![SDK-1-Top](https://github.com/WOA-Project/SurfaceDuo-Guides/assets/3755345/4c1c3762-24d8-4150-ac69-670738eb62c1)

Once on the page, scroll a little bit down til you see the link to download the platform tools for Windows.

![SDK-2-Mid](https://github.com/WOA-Project/SurfaceDuo-Guides/assets/3755345/cd14a232-4995-480f-a061-54507e83cf41)

Click on it, an EULA will open like below:

![SDK-3-EULA](https://github.com/WOA-Project/SurfaceDuo-Guides/assets/3755345/16d6b7df-ab56-414c-b1a5-561ec6b3ae4e)

Scroll all the way down (after reading it if that's your thing)

![SDK-4-EULA-Bottom](https://github.com/WOA-Project/SurfaceDuo-Guides/assets/3755345/1368b2b0-74b8-4a7c-9aff-df2ca25c2f42)

Tick "I have read and agree to above terms conditions"

![SDK-5-EULA-TICK (alt)](https://github.com/WOA-Project/SurfaceDuo-Guides/assets/3755345/02905fa2-64b8-426b-b42f-c1bb88eaa88a)

And click download

![SDK-5-EULA-TICK](https://github.com/WOA-Project/SurfaceDuo-Guides/assets/3755345/0983f27a-76e7-4fda-ac4d-adaa56702e90)

Save the file on your computer, and extract the zip file by opening it, and selecting extract all.

![SDK-6-DL](https://github.com/WOA-Project/SurfaceDuo-Guides/assets/3755345/adc1bba0-6118-418e-9005-e2db12860893)

  </p>
</details>

## Booting to TWRP

- Reboot your deivce into recovery

## Formatting the existing Windows partition

```batch
adb shell format
```

Now the Windows Partition on your Xiaomi Mi 9 should be empty. Let's go ahead and reinstall everything.

## Getting to Mass Storage Mode

- Please turn your phone and boot to TWRP again before proceding further

- Let's run the mass storage shell script in order to boot into Mass Storage from TWRP. You must decrypt your data if it asks you to.

```batch
adb shell msc
```

Xiaomi Mi 9 should now be in USB Mass Storage Mode.

## Installing Windows

- Make sure you are in Mass Storage Mode, that your Xiaomi Mi 9 is plugged into your PC
- Mount the partitions you have created using diskpart and assign them some letters:

```batch
⚠️ THESE ARE NOT ALL COMMANDS. DISKPART COMMANDS VARY A LOT, SO THESE ARE SOME ROUGH INSTRUCTIONS.
ACTUAL COMMANDS START WITH AN HASHTAG (which you will need to remove)
YOU DO NOT HAVE TO USE THE LETTERS WE USE AT ALL!!!, THEY ONLY NEED TO BE FREE LETTERS. IF LETTERS DON'T ASSIGN FINE, USE ANOTHER ONE.
IF ONE PARTITION IS ALREADY ASSIGNED, YOU ALSO DO NOT NEED TO ASSIGN IT AGAIN IF YOU DONT WANT TO.

# list disk
Find the Xiaomi Mi 9 Disk, and take note of the number.
# select disk <number>
# list partition
You will be able to recognize the partitions we made earlier by their size. take note of the ESP and WIN partition numbers.
# select partition <esp-partition-number>
# assign letter=<THE LETTER YOU WANT AS LONG AS IT IS NOT CURRENTLY IN USE IN FILE EXPLORER FOR ANOTHER DRIVE! (Example: X)>:
# select partition <win-partition-number>
# assign letter=<ANOTHER LETTER YOU WANT AS LONG AS IT IS NOT CURRENTLY IN USE IN FILE EXPLORER FOR ANOTHER DRIVE! (Example: Y)>:
```

- You will have two partitions loaded, one is the ESP partition, and the other is the Win partition. Take note of the letters you've used.

> [!WARNING]
From now on we will assume X: is the Win partition and that Y: is the ESP partition for all the commands. You very very likely used other letters, or have to use other letters. Replace them correctly with what you previously picked or you will lose data on your PC.

- We will need our install.wim file now. If you haven't it already, you can [use this guide](/InstallWindows-en/ISO/GetWindows.md). When you are ready, run these commands:

```batch
dism /apply-image /ImageFile:"<path to install.wim>" /index:1 /ApplyDir:X:\
```

This will take a bit of time. Go make some coffee ☕ or some tea 🍵.

- Once that is done:

```batch
rmdir /Q /S Y:\EFI
bcdboot X:\Windows /s Y: /f UEFI
```

Windows is now installed but has no drivers.

## Installing the drivers

- Download the latest driver package from https://github.com/woa-vayu/POCOX3Pro-Releases/releases/latest

Note: Here's a table of what to download if you're a bit lost:

| File Name                                      | Target Device         |
|------------------------------------------------|-----------------------|
| POCOX3Pro-Drivers-XXXX.XX-Desktop-Epsilon.zip  | Xiaomi Mi 9           |

- Extract the driver package, and go to the folder where you extracted it.

- Double click the ```OfflineUpdater.cmd``` file.

- Accept the User Account Control warning when prompted

- If asked enter the drive letter of the connected phone in mass storage, as we previously mentioned, for us it's currently ```X:```, but it may very well be different for you. In our example, we enter ```X:``` and then press enter.

- The process may take a while, once it is done, you will be prompted to press any key, press enter when that's the case.

Congratulations, you just installed your drivers!

- You can now reboot your phone using ```adb reboot bootloader```.

## Boot Windows 🚀

We are ready to boot!

You'll have two methods of booting Windows.

- Manual booting with a PC
    - Pros: You can freely update Android™
    - Cons: You will need a PC to boot to Windows

- Enabling Dual Boot
    - Pros: You'll be able to boot Windows directly from the device

In case you want the dual boot option, then follow [this guide](/InstallWindows-en/DualBoot.md)

---
<details>
  <summary>In case you want to manually boot each time: (<b>Click to expand</b>)</summary>
  <p>

Reboot your device to the Bootloader mode, using adb or from the recovery.

Let's boot the UEFI, from a command prompt:

```batch
fastboot boot uefi.img
```

This step above will be needed every time you will want to boot Windows and needs to be done from the Bootloader mode.

If you did everything right, Windows will now boot! Enjoy!

**Note:** If the Touch keyboard won't show up in OOBE, touch somewhere else (to let the text box loose focus) and then touch into the text box again. As an alternative, you can use the On-Screen Keyboard.
  </p>
</details>

---

_**© 2020-2024 The Duo WOA Authors**_

_Snapdragon is a registered trademark of Qualcomm Incorporated. Microsoft, the Microsoft Corporate Logo, Windows, Surface, Surface Duo, Windows Hello, Continuum, Hyper-V, and DirectX are registered trademarks of Microsoft Corporation in the United States. Android is a registered trademark of Google LLC. Miracast is a registered trademark of the Wi-Fi Alliance. Other binaries may be copyright Qualcomm Incorporated, Microsoft Surface and Xiaomi Inc._

_**Limited emergency calling**_

_Running Windows on your Xiaomi Mi 9 is not a replacement for a proper phone operating system and does not have emergency calling capabilities._

_**Hello from Seattle (US), France, Italy.**_