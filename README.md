# Universally Enable `Sensors Off` Tile on Android phones

## Motivation

Android phone sensors could be a privacy leak (search yourself, too lazy to put it here). The `Sensors Off` developer tile in recent Android versions blocks conventional app accesses to basically all sensors, improving privacy. Some vendors have removed this tile, in some cases there are even outcries of users (again, search yourself). This guide aims to recover the `Sensors off` tile.

## Prerequisites

* A *recent* version of ApkTool
* The `Settings.apk` of your device's ROM
* The `Settings.apk` from a near-stock OS, such as LineageOS
* Access to fairly recent AOSP source code (just online to see 1--2 files, no need to download it)
* A rooted phone

NOTE: This guide is more of a write-up for a quasi-developer to execute based on the actual situation. If you don't understand the steps below, **DO NOT proceed further**.

## Steps

1. Core patch the Android framework so it can accept our modified Settings app in the next step.
2. Modify Settings app.
   1. Decompile the vendor `Settings.apk` using apktool.
   2. Recover service description. Search the service tag in the stock `Settings.apk` and add it to the vendor `Settings.apk`.
   3. Recover the actual class implementing the `Sensors Off` tile. Copy the relevant smali file of the stock `Setting.apk` over to the vendor one (just one class for the `Sensors Off` tile), and apply necessary changes to the smali code.
      * The places of the files need to correspond. Notice the class names.
      * The source code of AOSP Settings is your friend. Reference it for the basic arrangement of smali files, and look within for a basic idea of the relevant class, method, and field names. You need basic Java -> smali knowledge.
      * Some vendors may obfuscate class names in the Settings app, which are perhaps internal to the app and not referenced in outside packages. Please notice and replace accordingly.
      * Some vendors may modify the base class of the development tiles. Notice method signatures, and change accordingly. Some methods relevant to tile initialization may simply be merged into the constructor, so take care.
3. Install the modified `Settings.apk`.
   * As the new Settings app is now a user one, you need to either fix the SELinux contexts of its app and data directories, or disable SELinux altogether (**INSECURE**). Check the system log accordingly, and see the exact error type. This step might fail on vendor ROMs with non-generic SELinux configurations.
4. Test by adding the `Sensors Off` tile and switching it.
   * If there are crashes or tile not loading properly, check the system log. Errors occur most frequently on name porting, especially class and method names.
   * When switching. Heavily modified vendor ROMs might also fail for various reasons.

