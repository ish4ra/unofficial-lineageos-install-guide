# Unofficial LineageOS Install Guide — POCO M3 (citrus)

> A practical installation and troubleshooting guide for running an **unofficial LineageOS build on the Xiaomi POCO M3 (citrus)**, based on the device and workflow I actually use.

![POCO M3](https://img.shields.io/badge/Device-POCO%20M3-black?style=flat-square)
![citrus](https://img.shields.io/badge/Codename-citrus-orange?style=flat-square)
![Snapdragon 662](https://img.shields.io/badge/SoC-Snapdragon%20662-blue?style=flat-square)
![Android 15](https://img.shields.io/badge/Android-15-green?style=flat-square)
![Unofficial](https://img.shields.io/badge/LineageOS-Unofficial-red?style=flat-square)

## ⚠️ Disclaimer

This repository is **not affiliated with LineageOS, Xiaomi, POCO, Google, or any ROM maintainer**.

Unlocking the bootloader and flashing custom software can wipe your data, break DRM/banking functionality, create boot loops, or brick the phone if you flash an image intended for the wrong device.

**Always follow the release notes for the exact ROM build you are installing.** Recovery, firmware, `boot`, `vendor_boot`, `dtbo`, encryption, and partition requirements can change between builds.

---

## My device / tested context

| Item | Details |
|---|---|
| Device | Xiaomi POCO M3 |
| Device codename | `citrus` |
| Model family | M2010J19xx |
| SoC | Qualcomm Snapdragon 662 / SM6115 |
| GPU | Adreno 610 |
| RAM | 4 GB / 6 GB depending on variant |
| Original software generation | Android 10 / MIUI 12 |
| Bootloader | Unlocked |
| PC connection | ADB + Fastboot working |
| Previous custom ROM | PixelOS Android 13 |
| Current ROM family | Unofficial LineageOS |
| LineageOS generation used around this setup | LineageOS 22.2 / Android 15 era |

The POCO M3 may also appear in unified Android device trees under names such as **juice** or **chime**, but the POCO M3-specific codename is **`citrus`**.

---

## Contents

1. [Before you start](#1-before-you-start)
2. [Files you need](#2-files-you-need)
3. [Back up the phone](#3-back-up-the-phone)
4. [Install ADB and Fastboot](#4-install-adb-and-fastboot)
5. [Unlock the bootloader](#5-unlock-the-bootloader)
6. [Enter and verify Fastboot](#6-enter-and-verify-fastboot)
7. [Flash recovery](#7-flash-recovery)
8. [Format data](#8-format-data)
9. [Install LineageOS](#9-install-lineageos)
10. [Install GApps — optional](#10-install-gapps--optional)
11. [First boot checklist](#11-first-boot-checklist)
12. [Updating later](#12-updating-later)
13. [Returning to stock MIUI](#13-returning-to-stock-miui)
14. [Troubleshooting](#14-troubleshooting)
15. [Notes from my POCO M3](#15-notes-from-my-poco-m3)
16. [Credits and references](#16-credits-and-references)

---

# 1. Before you start

### You need

- Xiaomi POCO M3 / `citrus`
- an unlocked bootloader
- Windows, Linux, or macOS PC
- a reliable USB data cable
- Android Platform Tools
- a compatible recovery
- a ROM build that explicitly supports your device
- required firmware/vendor package, if specified by the ROM maintainer
- optional GApps package if the ROM does not include Google apps

### Recommended

- battery above 60%
- full backup
- original stock ROM available in case you need to recover
- SHA256/checksum verification when the maintainer publishes hashes

---

# 2. Files you need

A typical clean install needs:

```text
platform-tools/
├── adb
├── fastboot
├── recovery.img
├── lineage-22.2-YYYYMMDD-UNOFFICIAL-citrus.zip
└── optional-gapps.zip
```

During my own setup I used an **Android 15 / LineageOS 22.2-era unofficial `citrus` build**.

I also prepared POCO M3 Global firmware from the **MIUI V14.0.2.0.SJFMIXM** generation during the process.

> That firmware version is part of my setup history, **not a universal requirement**. Use whatever firmware the maintainer of your exact ROM build specifies.

### Device-name check

Use packages intended for:

```text
POCO M3
codename: citrus
SoC: Snapdragon 662 / SM6115
```

Do **not** confuse it with:

- POCO M3 Pro / `camellia`
- another POCO/Redmi device with a similar product name
- a unified `juice`/`chime` image unless its maintainer explicitly lists POCO M3 support

---

# 3. Back up the phone

A clean flash normally includes **Format Data**, which erases internal storage.

Back up:

- photos and videos
- Downloads
- documents
- WhatsApp/Signal backups
- authenticator recovery codes
- contacts
- game saves
- app exports
- anything stored only in internal storage

Also make sure you still know the credentials for banking and 2FA-protected accounts before wiping the device.

---

# 4. Install ADB and Fastboot

Download the latest **Android Platform Tools** from Google:

https://developer.android.com/tools/releases/platform-tools

Extract them and open a terminal inside the folder.

Check ADB:

```bash
adb version
```

With Android running and USB debugging enabled:

```bash
adb devices
```

Accept the authorization prompt on the phone.

Reboot to Fastboot:

```bash
adb reboot bootloader
```

---

# 5. Unlock the bootloader

> Unlocking the bootloader wipes the phone.

Use Xiaomi's official bootloader-unlock process and Mi Unlock tooling where required.

After unlocking, the phone may show an unlocked-bootloader warning during startup. That is normal.

Do not relock the bootloader while an unofficial ROM is installed.

---

# 6. Enter and verify Fastboot

Power off the phone, then hold:

```text
Volume Down + Power
```

Connect the USB cable.

Verify the connection:

```bash
fastboot devices
```

You should see a device serial.

Optional:

```bash
fastboot getvar product
```

If nothing appears:

- try another USB port
- try another cable
- avoid unreliable USB hubs
- check Windows Device Manager
- install/fix the Android Bootloader Interface driver
- retry `fastboot devices`

Do not continue until Fastboot works reliably.

---

# 7. Flash recovery

Put the recovery image in the Platform Tools directory and rename it to something simple:

```text
recovery.img
```

A common recovery-flash flow is:

```bash
fastboot flash recovery recovery.img
fastboot reboot recovery
```

### Important

Some ROMs use a different recovery layout and may require commands involving `boot`, `vendor_boot`, or other partitions.

If the maintainer gives different commands, **use the maintainer's commands instead of this generic example**.

---

# 8. Format data

For a clean installation, use recovery's equivalent of:

```text
Factory Reset
→ Format Data
```

or:

```text
Wipe
→ Format Data
```

Formatting data is different from only wiping cache.

This step removes encryption state and user data from the previous ROM so the new system can start cleanly.

---

# 9. Install LineageOS

A common modern recovery flow is:

```text
Apply Update
→ Apply from ADB
```

Then on the PC:

```bash
adb sideload lineage-22.2-YYYYMMDD-UNOFFICIAL-citrus.zip
```

Replace the filename with the actual ROM file you downloaded.

### ADB sideload seems stuck?

Some recoveries stop showing progress around the middle of the transfer while installation continues on-device.

Do not judge success only by the PC percentage.

Read the final status shown by the recovery.

---

# 10. Install GApps — optional

LineageOS generally does not bundle Google apps unless the unofficial build specifically does.

If you want Google Play Services and your ROM requires a separate package:

1. Do **not** boot Android after flashing the ROM.
2. Return to **Apply Update → Apply from ADB**.
3. Sideload the recommended GApps package.

Example:

```bash
adb sideload gapps-package.zip
```

Make sure:

- Android version matches
- CPU architecture matches
- the package is compatible with the ROM
- the ROM maintainer recommends/supports it

If you want a Google-free installation, skip this section.

---

# 11. First boot checklist

Choose:

```text
Reboot system now
```

The first boot can take several minutes.

After setup, test:

- [ ] Wi-Fi
- [ ] mobile network
- [ ] mobile data
- [ ] calls
- [ ] SMS
- [ ] Bluetooth
- [ ] camera
- [ ] microphone
- [ ] speakers
- [ ] fingerprint sensor
- [ ] GPS
- [ ] USB data
- [ ] charging
- [ ] hotspot
- [ ] auto-rotation
- [ ] deep sleep / standby

---

# 12. Updating later

For a newer build from the same ROM branch, read the maintainer's update notes first.

A typical update may look like:

```text
Recovery
→ Apply Update
→ ADB Sideload
→ newer ROM ZIP
→ reboot
```

A clean flash may be required when:

- changing Android major version
- changing maintainer
- changing ROM family
- encryption changes
- firmware base changes
- partition layout changes
- recovery requirements change

Do not assume every build is dirty-flash compatible.

---

# 13. Returning to stock MIUI

Keep a recovery path ready before experimenting.

General idea:

1. Download the correct official POCO M3/`citrus` fastboot ROM for your region.
2. Extract it on the PC.
3. Boot the phone into Fastboot.
4. Use Xiaomi's supported flashing method / Mi Flash where appropriate.
5. Restore stock software.
6. Boot and verify everything before considering any bootloader relock.

### Bootloader warning

Never relock the bootloader while an unofficial ROM is installed.

A wrong ROM + locked bootloader can make recovery much harder.

---

# 14. Troubleshooting

## ADB cannot see the phone

Try:

```bash
adb kill-server
adb start-server
adb devices
```

Then reconnect the phone and accept the USB-debugging authorization prompt.

---

## Fastboot cannot see the phone

Check:

- USB cable
- USB port
- bootloader driver
- Device Manager on Windows
- that the phone is actually in Fastboot mode

---

## Recovery does not boot

Possible causes include:

- wrong recovery
- recovery for another device/tree
- incompatible Android generation
- wrong flashing procedure
- stock system overwrote recovery

Return to Fastboot and check the ROM maintainer's exact recovery instructions.

---

## ROM refuses to install

Check:

- ROM filename/device target
- `citrus` support
- ZIP integrity
- firmware requirement
- recovery compatibility
- battery level
- release notes

If a SHA256 checksum is published, verify it.

---

## Boot loop after flashing

Try this order:

1. Return to recovery.
2. Confirm you flashed the correct build.
3. Confirm firmware requirements.
4. Make sure Format Data was actually performed for a clean flash.
5. Reinstall the ROM.
6. Boot the ROM once **before** adding Magisk or modules.

This makes it easier to separate ROM problems from root/module problems.

---

## Wi-Fi disconnects while idle

I encountered **Wi-Fi idle/disconnect behaviour** after moving this POCO M3 to LineageOS.

Things worth checking:

- test with no root modules
- reset network settings
- compare 2.4 GHz and 5 GHz
- remove aggressive battery restrictions from apps that need connectivity
- check the ROM maintainer's issue tracker
- test a newer ROM build if the issue is already fixed upstream

---

## Banking apps / Play Integrity

An unlocked bootloader and unofficial ROM may affect:

- Play Integrity
- banking apps
- DRM
- Widevine behaviour
- games/apps with integrity checks

This guide does not claim stock-level compatibility for those services.

---

## Camera quality is different

AOSP/LineageOS camera behaviour can differ from MIUI.

On my POCO M3 I also experimented with compatible **GCam ports**, but GCam is optional and may have limitations with video, auxiliary cameras, or gallery integration depending on the ROM and camera HAL.

---

# 15. Notes from my POCO M3

My actual custom-ROM path was roughly:

```text
POCO M3 (citrus)
│
├── MIUI
│
├── PixelOS Android 13
│
└── Unofficial LineageOS / Android 15-era build
```

The goal of this repository is not to mirror random ROM files.

It is to keep a **repeatable installation reference** for the device I actually use and to record real-world problems that can appear after installation.

---

# 16. Credits and references

### Main projects

- LineageOS — https://lineageos.org/
- LineageOS Wiki — https://wiki.lineageos.org/
- Android Platform Tools — https://developer.android.com/tools/releases/platform-tools
- Xiaomi / POCO — https://www.mi.com/

### POCO M3 community development

The POCO M3 has community Android development under device trees using names such as:

```text
citrus
juice
chime
```

These device trees, kernels, recoveries, firmware packages, and unofficial ROM builds belong to their respective developers/maintainers.

Whenever possible, download builds from the original maintainer rather than re-uploaded mirrors.

---

## Contributing

Corrections and device-specific findings are welcome.

When reporting a problem, include:

```text
POCO M3 model:
ROM build:
ROM date:
Recovery:
Firmware:
Clean/dirty flash:
Exact error:
What was flashed immediately before the problem:
```

That is much more useful than only saying "it doesn't boot".

---

## Quick pre-flash checklist

- [ ] Device is POCO M3 / `citrus`
- [ ] Bootloader is unlocked
- [ ] Data is backed up
- [ ] Fastboot works
- [ ] Recovery supports the build
- [ ] ROM supports the device
- [ ] Firmware requirement is confirmed
- [ ] Battery is charged
- [ ] Stock recovery method is available

---

**Maintained by [ish4ra](https://github.com/ish4ra)**

If this guide helped, consider starring the repository.
