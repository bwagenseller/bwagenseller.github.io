# Android SDK  

<!-- www.apkmirror.com -->

This document discusses the Android SDK on Debian Linux (Ubuntu). 

# Terms  

## Android Virtual Device (AVD)  

An <font color="green">Android Virtual Device</font> (or <font color="green">AVD</font> for short) is a _specific_ virtualized / emulated Android device. You should make one <font color="green">AVD</font> for each device type you wish to support. An <font color="green">AVD</font> is a virtualized device that is the result of pairing an Android OS with a [skin](learn_to_code/android/sdk_tools/sdk_basics?id=skin) and running the pair through emulation software.  

## Skin  

A <font color="green">skin</font> is a list of instructions that will mimic a specific Android device. Examples are a Samsung Note device, a Samsung Galaxy Nexus device, a Google Pixel device, an Android TV, etc.  

## Snapshot  

A <font color="green">snapshot</font> references a previously saved [state](learn_to_code/computer_science_concepts?id=state) of the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd). Having snapshots are useful if you want to return to an _exact_ state for testing purposes.  

## Quick Boot Snapshot  

A <font color="green">Quick Boot snapshot</font> is a snapshot that is taken just before an [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) is shut down. By default, the next time you run the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd), the last <font color="green">Quick Boot snapshot</font> will be restored unless you specify that it shouldn't.  

Exactly one <font color="green">Quick Boot snapshot</font> is saved for each [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd).  


# Installation 

## Install via Android Studio  

I install the Android SDK via [Android Studio](learn_to_code/android/android_studio); there may be other ways to do this, but I find Android Studio is a solid IDE.  

# Changing Ownership of /dev/kvm  

!> Either of the steps below is critical to run the Android emulator; it is highly unlikely you are running the emulator as root, and if you do not do one of the two, you will have to use `sudo` on multiple other commands each time you run the emulator.  

You _must_ somehow have ownership of `/dev/kvm`; there are two ways to do this.  

The first is the recommended route; this is done by installing `qmeu-kvm` and then adding yourself to the `kvm` group. The steps are:  

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root).

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages).

3\. Install `qemu-kvm` via: `apt-get install qemu-kvm`  

4\. Add your current user to the `kvm` group: `usermod -aG kvm USERNAME`  
   * `USERNAME` is your user name.  
   
5\. Restart your desktop / laptop.  

You should only have to do this once. Moving forward, you should have access to `/dev/kvm` at all times.  

If, for some reason, the above does not work _or_ you do not want to install `qmeu-kvm` you can simply change the ownership of `/dev/kvm` as root, but be warned - you will have to do this _every_ time you re-boot your machine. To change the ownership of this resource, [become root](/operating_systems/ubuntu/linux_notes?id=becoming-root) (or use `sudo` before the command) and run:
```
chown YOUR_USERNAME_HERE:YOUR_USERNAME_HERE /dev/kvm
```  
* Change `YOUR_USERNAME_HERE` to your own user name on the system.  

---  

# Notable Directories 

These are the notable directories inside a generic emulator.  

| Directory | Description |  
| --- | --- |  
| `/storage/1A00-1709` | This is the SD card directory. Note that the final directory may change - it may be a different oddly named alphanumeric name, but it will _not_ be `emulated` or `self`.  |  
| `/storage/emulated/0` _or_ <br> `/storage/self/primary` (symlink) _or_ <br> `/sdcard` (symlink)  | This is the root memory directory as it appears on the phone. |  
| `/storage/emulated/0/Android/data` | This is the base location for some data for various installed packages.  |  

