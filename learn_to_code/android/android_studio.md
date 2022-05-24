# Android Studio  

# Installation  

## Install Android Studio  

> This assumes you are in Ubuntu, or another Debian offshoot.  

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root).

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages).

3\. Install `qemu-kvm` via: `apt-get install qemu-kvm`  

4\. Add your current user to the `kvm` group: `usermod -aG kvm USERNAME`  
   * `USERNAME` is your user name.  
   
5\. If it exists, uninstall `adb` with: `apt remove adb`  
   * You _probably_ do not actively use `adb` if you have never used the Android SDK, but if its installed - figure out why.  
     * If its needed elsewhere you can leave it, but be warned - the Android SDK also installs a local version of this, and if the versions do not match between the two you _will_ have issues.  
	 * Its best to have only _one_ instance of `adb` installed (and I would stick with the one the Android SDK installs).  

6\. Exit root: `exit`  

7\. Download the Android Studio tarball [here](https://developer.android.com/studio).  

8\. Unzip the tarball to your favorite directory - I usually pick `/android-studio`, but the choice is ours.  

9\. <font color="red">Optional</font> Follow [these instructions](operating_systems/ubuntu/linux_notes?id=creating-executable-icons) on how to make an executable icon for Android Studio.  
   * If you do not take this step, **you will have to run** `/android-studio/bin/studio.sh` every time you want to run Android Studio.  
     * This assumes you unzipped the tarball in step #8 above to `/android-studio`; if you did not, you will have to run `studio.sh` from that directory.  
   * I named my desktop file <font color="purple">Android Studio.desktop</font> but you are free to name it whatever you wish - the contents are:
```
[Desktop Entry]
Type=Application
Name=Android Studio
Comment=Android Studio
Icon=/android-studio/bin/studio.png
Exec=/android-studio/bin/studio.sh
Terminal=false
Categories=Android;Development;IDE;
StartupWMClass=jetbrains-studio
```  
     * This assumes you unzipped the tarball in step #8 above to `/android-studio`; if you did not, you will have to alter the Icon and Exec lines above.  
   * You should be able to skip checking the `WM_CLASS` as that should be `jetbrains-studio` - but if you have problems, check it.  
   * You _will_ have to add the desktop file to `/usr/share/applications`  
     * You will have to do this step [as root](/operating_systems/ubuntu/linux_notes?id=becoming-root) (and only this step - once the file is added exit root).  
   * You _will probably_ have to alter the 'Custom Values' field after you launch `dconf-editor` for the icon to stay (and work).  
   
10\. Run Android Sudio (either via the executable `studio.sh` _or_ your icon).  
   * The first time you run this, you will be prompted to set up / install the **<font color="purple">Android SDK</font>** - do so.  
   * Not only does this install the **<font color="purple">Android SDK</font>**, it also installs critical tools to interact with the Android emulator (i.e. `adb` and `emulator`).  
   * By default, **<font color="purple">Android SDK</font>** installs to your directory `~/Android/Sdk` (its specific to you, it does not seem to be a global install).  

11\. Add this line to your [.profile](operating_systems/ubuntu/linux_notes?id=the-profile), as the Android SDK that was installed in step #10 above installs a bunch of critical tools as well (`adb` and `emulator` to name a few):  
```
PATH="$HOME/Android/Sdk/cmdline-tools/latest/bin:$HOME/Android/Sdk/emulator:$HOME/Android/Sdk/platform-tools:$PATH"
```  
   * This assumes you installed the Android SDK to the default of `~/Android/Sdk`  

12\. [Source in](operating_systems/ubuntu/linux_notes?id=sourcing-your-profile) your .profile.  
   * This is only necessary if you have a terminal up and you are ready to run Android SDK commands immediately in that terminal.  
   
13\. Add your current user to the `plugdev` group. [As root](/operating_systems/ubuntu/linux_notes?id=becoming-root) (and only this step - once the file is added exit root): `usermod -aG plugdev USERNAME`  
   * `USERNAME` is your user name.  
   
14\. Install the Common SDK Platform Tools so you can load your apk install file directly to a physical Android device. do this [as root](/operating_systems/ubuntu/linux_notes?id=becoming-root) (and only this step - once the file is added exit root): `apt-get install android-sdk-platform-tools-common`  

15\. [Add a virtual device](learn_to_code/android/android_studio?id=adding-a-virtual-device).  
   * While not technically a part of setup, you will need this if you ever wish to test your code on a virtual device.  

## Additional Skins  

Additional Skins:
1\. [Samsung Galaxy Skins](https://developer.samsung.com/galaxy-emulator-skin)

## Adding a Virtual Device  

If you wish to test your code, you _will_ need to install a virtual Android device - here is how that is done within Android Studio.  

1\. Click the dropdown for `Tools / Device Manager` which will open a toolbox on the right like so:  
![android_studio_device_manager_toolbox.jpg](images/android_studio_device_manager_toolbox.jpg)
* Note the `/dev/kvm device: permission denied` warning - [here is how you fix that](learn_to_code/android/android_sdk?id=changing-ownership-of-devkvm).  
* I have a virtual device set up already (`Pixel 2 API Tiramisu`) - if you are just starting out, this box will be empty.  

2\. Click <font color="purple">Create device</font>.  

3\. A pop-up will appear, asking you to select some virtual hardware. This is the physical Android device you will be emulating (Samsung Galaxy, Pixel, etc). You can also customize the device, if you wish):  
![android_studio_select_virtual_hardware.jpg](images/android_studio_select_virtual_hardware.jpg)
* Once you are happy with your selection, click <font color="purple">Next</font>.  

4\. Now you must set the system image (i.e., which Android OS would you like to install on this device?):
![android_studio_set_system_image.jpg](images/android_studio_set_system_image.jpg)
* If you select a system image you have never downloaded, you _must_ click the `Download` link immediately next to the system image name to download the system image.  
* Note the `/dev/kvm device: permission denied` warning - [here is how you fix that](learn_to_code/android/android_sdk?id=changing-ownership-of-devkvm).  
* Once you are happy with your selection, click <font color="purple">Next</font>.  

5\. Finally, you will have to name the device; you also have some final things to set, but only if you wish to do so:  
![android_studio_name_device.jpg](images/android_studio_name_device.jpg)
* Note the `/dev/kvm device: permission denied` warning - [here is how you fix that](learn_to_code/android/android_sdk?id=changing-ownership-of-devkvm).  
* Once you are happy with your selection, click <font color="purple">Finish</font>.  
* You will now have a device in the `Device Manager` toolbox.  
   * See the image in step #1 for an example - here, we have a 'Pixel 2' device running on the 'Tiramisu' version of the Android OS.  
 
## Attaching a Physical Android Device  

> I give a very brief overview here; for a more detailed walkthrough, see [developer.android.com](https://developer.android.com/studio/command-line/adb#connect-to-a-device-over-wi-fi-android-11+).  

Its fully possible to attach a physical Android device to Android Studio; if you do so, you will be able to test your code on an actual device.  

The first thing you have to do is enable <font color="purple">Developer Options</font> on your physical device. Typically the way to do this is to find the <font color="purple">Build Number</font> field and tap it 7 times in a row (usually, the <font color="purple">Build Number</font> is in Settings -> About Phone -> Software Information, but that is no guarantee). Once you enable <font color="purple">Developer Options</font>, it will typically be the last option in the main <font color="purple">Settings</font> tree.  Once you have unidden this option, _do not forget to turn the option on_.  

Once you have enabled <font color="purple">Developer Options</font> on your device you have two options on connecting it in Android Studio - pairing using WiFi and pairing over USB. The advantage of the Wifi route is you can connect _multiple_ devices at once; on the downside, the WiFi option requires Android 11 or higher.  

