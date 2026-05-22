# Moto G5 Plus XT1687 / potter: Bootloader Unlock, TWRP, crDroid Android 11, GApps, and Magisk

This runbook is for installing **crDroid Android 11** on **Motorola Moto G5 Plus XT1687**, codename **potter**, and then installing **Magisk** for root.

Use only files intended for **Moto G5 Plus / potter**. Do **not** use files for Moto G5 / cedric.

---

## 0. What you need

### On the PC

Install:

- Google Android Platform Tools: `adb.exe` and `fastboot.exe`
- Motorola USB drivers, if using Windows

Recommended folders:

```text
C:\adb
C:\MotoG5
```

### On the microSD card

Copy these files to the microSD card before starting:

```text
crDroid Android 11 ROM ZIP for potter
Android 11 ARM64 GApps ZIP
Magisk APK, optional for later root
```

Correct GApps type:

```text
Architecture: ARM64
Android version: 11
```

Do not flash Android 12/13 GApps on Android 11.

### TWRP image on PC

Download TWRP for Moto G5 Plus / potter:

```text
https://dl.twrp.me/potter/
```

Example filename:

```text
twrp-3.7.0_9-0-potter.img
```

Put it here:

```text
C:\MotoG5\twrp-3.7.0_9-0-potter.img
```

---

## 1. Enable Developer Options

On the phone:

```text
Settings
→ About phone
→ tap Build number 7 times
```

Then enable:

```text
Settings
→ Developer options
→ OEM unlocking: ON
→ USB debugging: ON
```

If **OEM unlocking** is missing or greyed out, the device may not be eligible for bootloader unlocking.

---

## 2. Reboot to bootloader

Connect phone to PC.

From Windows Command Prompt:

```bat
cd C:\adb
adb devices
adb reboot bootloader
```

Or power off the phone and hold:

```text
Power + Volume Down
```

You should see Motorola fastboot / bootloader mode.

Check fastboot connection:

```bat
fastboot devices
```

Expected:

```text
XXXXXXXX    fastboot
```

If nothing appears, fix the Windows driver in Device Manager. The phone should appear as **Android Bootloader Interface**.

---

## 3. Get Motorola unlock data

Run:

```bat
fastboot oem get_unlock_data
```

You will see several lines like:

```text
(bootloader) 3A95720615784488#5441383833304C
(bootloader) 47325600585431303332000000#1F53
(bootloader) 000000000000000000000000000000
```

Copy only the text after `(bootloader)` and join it into one single line with:

```text
no spaces
no line breaks
```

Submit it to Motorola's official unlock page:

```text
https://en-us.support.motorola.com/app/standalone/bootloader/unlock-your-device-b
```

Motorola will email an unlock key if the device is eligible.

---

## 4. Unlock bootloader

Warning: this will factory reset the phone.

Run:

```bat
fastboot oem unlock YOUR_UNLOCK_KEY
```

On some Motorola devices, run it twice:

```bat
fastboot oem unlock YOUR_UNLOCK_KEY
fastboot oem unlock YOUR_UNLOCK_KEY
```

Confirm on the phone if prompted.

Verify unlock status:

```bat
fastboot getvar unlocked
```

Expected:

```text
unlocked: yes
```

The phone may show an unlocked bootloader warning at every boot. This is normal.

---

## 5. Flash TWRP

Make sure the phone is still in bootloader / fastboot mode.

Run:

```bat
fastboot flash recovery C:\MotoG5\twrp-3.7.0_9-0-potter.img
```

You may see:

```text
Image not signed or corrupt
```

This is normal on Motorola phones with custom recovery. Success looks like:

```text
Sending 'recovery' OKAY
Writing 'recovery' OKAY
Finished
```

---

## 6. Boot directly into TWRP

Very important: do **not** boot Android normally after flashing TWRP.

On the phone bootloader screen:

```text
Use Volume buttons → select Recovery Mode
Press Power
```

The phone should boot into TWRP.

If Android boots normally by mistake, stock recovery may overwrite TWRP. If that happens, flash TWRP again and immediately boot to **Recovery Mode**.

If TWRP asks:

```text
Keep System Read Only?
```

choose:

```text
Swipe to Allow Modifications
```

---

## 7. Optional backup

If this phone has data or partitions you want to preserve, make a backup now.

In TWRP:

```text
Backup
→ select Boot, System, Data
→ select EFS / Persist if available
→ Swipe to Backup
```

For a clean install where you do not need old data, you can skip this.

---

## 8. Format Data

This removes old encryption and user data. It is recommended for a clean ROM install.

In TWRP:

```text
Wipe
→ Format Data
→ type: yes
→ Confirm
```

This erases internal phone storage, but should not erase the microSD card.

After Format Data finishes:

```text
Reboot
→ Recovery
```

Wait for TWRP to restart.

---

## 9. Advanced Wipe

After TWRP restarts:

```text
Wipe
→ Advanced Wipe
```

Select:

```text
Dalvik / ART Cache
System
Data
Cache
```

Do **not** select:

```text
Micro SDCard
USB OTG
Internal Storage
EFS
Persist
Modem
Vendor, if shown
```

Then:

```text
Swipe to Wipe
```

---

## 10. Flash crDroid Android 11

In TWRP:

```text
Install
→ Select Storage
→ Micro SDCard
→ OK
```

Select the crDroid ROM ZIP for potter.

Example:

```text
crDroidAndroid-11.x-potter.zip
```

Then:

```text
Swipe to Confirm Flash
```

Wait until it finishes.

---

## 11. Flash Android 11 ARM64 GApps

Do this immediately after the ROM, before first reboot.

In TWRP:

```text
Install
→ Select Storage
→ Micro SDCard
→ select Android 11 ARM64 GApps ZIP
→ Swipe to Confirm Flash
```

Correct package:

```text
ARM64
Android 11
```

Wrong package examples:

```text
Android 12 GApps
Android 13 GApps
ARM 32-bit GApps
```

If the GApps installer says wrong Android version, stop and use the correct Android 11 package.

---

## 12. First boot without Magisk

For easier troubleshooting, do not flash Magisk yet.

After ROM and GApps finish:

```text
Wipe cache/Dalvik
→ Swipe
→ Reboot System
```

If TWRP asks to install the TWRP app:

```text
Do Not Install
```

First boot can take several minutes.

Complete Android setup and verify the ROM works.

---

## 13. Install Magisk after ROM boots successfully

Download official Magisk from:

```text
https://github.com/topjohnwu/Magisk/releases
```

File example:

```text
Magisk-vXX.X.apk
```

For TWRP flashing, rename it to ZIP.

On Windows:

```bat
ren Magisk-vXX.X.apk Magisk-vXX.X.zip
```

Example:

```bat
ren Magisk-v29.0.apk Magisk-v29.0.zip
```

Copy the ZIP to the microSD card.

Boot into TWRP:

```text
Power off
Power + Volume Down
Recovery Mode
```

In TWRP:

```text
Install
→ Select Storage
→ Micro SDCard
→ select Magisk-vXX.X.zip
→ Swipe to Confirm Flash
→ Reboot System
```

After Android boots, install/open the original Magisk APK if the Magisk app is not already installed.

If Magisk asks for additional setup, allow it and reboot.

---

## 14. Verify root

From PC:

```bat
adb shell su -c id
```

The phone should show a Magisk prompt for **Shell**. Tap:

```text
Grant
```

Expected output:

```text
uid=0(root) gid=0(root)
```

Or interactive test:

```bat
adb shell
su
```

Prompt should change from:

```text
$
```

to:

```text
#
```

---

## 15. Recommended installation order summary

```text
1. Enable OEM unlocking and USB debugging
2. Boot to fastboot
3. Get Motorola unlock data
4. Unlock bootloader with Motorola unlock key
5. Flash TWRP recovery
6. Boot directly into TWRP
7. Format Data
8. Reboot Recovery
9. Advanced Wipe: Dalvik / ART Cache, System, Data, Cache
10. Flash crDroid Android 11 potter ZIP
11. Flash Android 11 ARM64 GApps ZIP
12. Wipe cache/Dalvik
13. Reboot System
14. Complete Android setup
15. Reboot to TWRP
16. Flash Magisk ZIP
17. Reboot System
18. Open Magisk app and verify root
```

---

## 16. Common problems

### Fastboot does not see the phone

Try:

```text
Use another USB cable
Use another USB port
Avoid USB hubs
Install Motorola USB drivers
In Device Manager select Android Bootloader Interface
Use current Google platform-tools
```

Check:

```bat
fastboot devices
```

### TWRP flash shows Image not signed or corrupt

This is normal for unlocked Motorola devices. If writing recovery says `OKAY`, continue.

### Phone boots stock recovery instead of TWRP

You probably booted Android normally after flashing TWRP. Flash TWRP again and immediately choose **Recovery Mode** from bootloader.

### GApps flash fails

Most common cause: wrong Android version or wrong architecture.

For crDroid Android 11 on Moto G5 Plus:

```text
ARM64
Android 11
```

### Stuck at boot logo

Return to TWRP and do a clean reinstall:

```text
Format Data
Reboot Recovery
Advanced Wipe: Dalvik / ART Cache, System, Data, Cache
Flash ROM
Flash Android 11 ARM64 GApps
Reboot System
```

Do not flash Magisk until the ROM boots successfully.

### VoLTE does not work

LTE data can work even if VoLTE does not. On custom ROMs for older Qualcomm phones, VoLTE depends on working IMS support. If `adb shell dumpsys ims` says `Can't find service: ims`, the ROM likely does not expose a working Android IMS service.

### crDroid Android 11 ROM
https://crdroid.net/potter/7
This is crDroid 7 based on Android 11 for Moto G5 Plus / potter.

### TWRP for Moto G5 Plus / potter
Device page: https://twrp.me/motorola/motorolamotog5plus.html
Downloads: https://dl.twrp.me/potter/
TWRP lists the Moto G5 Plus device codename as potter.

### GApps for Android 11 ARM64
MindTheGapps Android 11 ARM64:
https://github.com/MindTheGapps/11.0.0-arm64/releases

Alternative FlameGApps Android 11 ARM64:
https://sourceforge.net/projects/flamegapps/files/arm64/android-11/
