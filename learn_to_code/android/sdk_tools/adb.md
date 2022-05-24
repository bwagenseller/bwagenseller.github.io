# ADB  

# Purpose of ADB  

While the [emulator](learn_to_code/android/sdk_tools/emulator) is used for setting up the emulator, you need a way to interact with it once it is running. The <font color="green">Android Debug Bridge</font> (or <font color="green">adb</font> for short) is used to interact with a running [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) instance.  

`adb` is installed during the [installation of Android Studio](learn_to_code/android/android_studio?id=install-android-studio), which is the recommended way to install. If, for whatever reason, you do not want to install via Android Studio you can install with `apt-get install adb`, but installing directly gave me problems.  

# Basic ADB Commands  

## ADB Help

For help with ADB:  
```
adb help
```  

!> There are plenty of topics I have missed here - I just covered the most critical ones. Check out `adb help` for the full listing of commands supported by ADB!  

## List Running AVDs  

To show a list of running [AVDs](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd), use the `adb devices` command:  
```
adb devices -l
```  
* The `-l` is not required, but shows you a bit more information about the running instance.  

Example output of this command is:  
```
List of devices attached
emulator-5554          device product:sdk_gphone64_x86_64 model:sdk_gphone64_x86_64 device:emu64xa transport_id:6
```  

Note the name - `emulator-5554`. You cannot directly set this as far as I can tell; that said, the `5554` is the port opened for this ADV for [console commands](learn_to_code/android/sdk_tools/other_tools?id=console-commands).  This means the name is a bit hard to pin down, but you can [explicitly set the port](learn_to_code/android/sdk_tools/emulator?id=set-ports) when you start the emulator, which may make finding your specific AVD easier if you have multiple AVDs running.  

## Specifying AVD in ADB  

If you only have _one_ running [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) instance, you will never have to specify the instance - `adb` will automatically know to pick the sole running instance. However, if you _do_ have multiple instances, you will need to get the device name (found be [listing the devices in adb](learn_to_code/android/sdk_tools/adb?id=list-running-avds)) and then use that name like so when running commands:  
```
adb -s EMULATOR_SERIAL_NUMBER XYZ
```  
* The `EMULATOR_SERIAL_NUMBER` is the name from [listing the devices in adb](learn_to_code/android/sdk_tools/adb?id=list-running-avds).  
* The `-s EMULATOR_SERIAL_NUMBER` specifies the running AVD instance.  
* `XYZ` is any command that is used by `adb` (`adb -s EMULATOR_SERIAL_NUMBER shell`, for example).  

## Starting the ADB Server  

To start the ADB server (if it is not running):  
```
adb start-server
```  

## Stopping the ADB Server  

To stop the ADB server:  
```
adb kill-server
```  

# Opening a Shell  

To open a shell to a running [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd):  
```
adb shell
```  
* You do not have to identify the AVD so long as you only have one running; if you do have multiple AVDs running, [specify the specific AVD](learn_to_code/android/sdk_tools/adb?id=specifying-avd-in-adb).  

You will be operating the `shell` account when you log in; if you want to switch to root, its simply `su`.  

# Package Commands  

## Installing a Package (APK)  

To install an .apk file to a running [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd):  
```
adb install /path/to/apk/someAPK.apk
```  
* You do not have to identify the AVD so long as you only have one running; if you do have multiple AVDs running, [specify the specific AVD](learn_to_code/android/sdk_tools/adb?id=specifying-avd-in-adb).  
* Its worth noting that most APKs will be in `PROJECT_NAME/MODULE_NAME/build/outputs/apk/debug`  
   * `PROJECT_NAME` and `MODULE_NAME` will vary.  
   * ITs possible that the last directory - `debug` - may not exist for you; in that case just ignore that directory.  

Some installs come with _multiple_ APKs; to install these you use `adb install-multiple` and then list all APKs separated by a space (and each individually wrapped in double quotes). The order does not matter, but there should be a main APK (usually called `base.apk` but that is not guaranteed).  Example:  
```
adb install-multiple "base.apk"  "split_config.en.apk" "split_config.x86_64.apk"
```  

> There are other ways to install multiple APKs that are centered around a single package as well.  


## List All Packages

To list all packages, use the `shell` command as follows:  
```
adb shell pm list packages | sort  
```  

## Uninstall a Package  

To uninstall a package, find the package name by [listing all packages](learn_to_code/android/sdk_tools/adb?id=list-all-packages) and then using the `uninstall` command:  
```
adb uninstall com.path.to.package.targetapp
```  
* `com.path.to.package.targetapp` is the package name above.  

## Locate Package APK  

To locate the .apk file used to install a particular package, find the package name by [listing all packages](learn_to_code/android/sdk_tools/adb?id=list-all-packages) and then using the `shell pm` command:  
```
adb shell pm path com.path.to.package.targetapp
```  
* `com.path.to.package.targetapp` is the package name above.  


---  

# Emulate Commands  

You can mimic the [console commands](learn_to_code/android/sdk_tools/other_tools?id=console-commands) via adb; the basic structure is `adb emu` followed by the command that would otherwise be given on the console.  

For example, if we wanted to simulate a call from `215-555-1234` we could do that like so:  
```
adb emu gsm call 2155551234
```  
* You do not have to identify the AVD so long as you only have one running; if you do have multiple AVDs running, [specify the specific AVD](learn_to_code/android/sdk_tools/adb?id=specifying-avd-in-adb).  

# Logging  

The logging works similarly to how it works [using the emulator command](learn_to_code/android/sdk_tools/emulator?id=logging); the general usage is:  
```
adb logcat "*:v"
```  
* You do not have to identify the AVD so long as you only have one running; if you do have multiple AVDs running, [specify the specific AVD](learn_to_code/android/sdk_tools/adb?id=specifying-avd-in-adb).  
* The `"*:v"` is a listing of `tag: log level`, just as it is while using the logcat command via [emulator](learn_to_code/android/sdk_tools/emulator?id=logging).  
* This command is one of the rare commands you can get further help with `adb logcat --help`.  

You _can_ save to a file (as opposed to having the logs sent to the screen), however - its a file on the AVD and _not_ your local hard drive. To do so, use the `-f /path/on/avd/device/log.txt` argument at the end of your `adb logcat` statement above.    

# File Transfers  

## Pushing a File  

To push a local file to the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd):  
```
adb push --sync /path/to/local/file.txt /path/on/emulated/device/someFile.txt
```  
* You do not have to identify the AVD so long as you only have one running; if you do have multiple AVDs running, [specify the specific AVD](learn_to_code/android/sdk_tools/adb?id=specifying-avd-in-adb).  
* `--sync` is not required, however if the file on the host is older than the device, the file will _not_ be pushed.  

## Pulling a File  

To pull a file from the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) and store it on the host:  
```
adb pull -a /path/on/emulated/device/someFile.txt /path/to/new/local/file.txt  
```  
* You do not have to identify the AVD so long as you only have one running; if you do have multiple AVDs running, [specify the specific AVD](learn_to_code/android/sdk_tools/adb?id=specifying-avd-in-adb).  
* `-a` preserves file timestamp and mode (not required).  