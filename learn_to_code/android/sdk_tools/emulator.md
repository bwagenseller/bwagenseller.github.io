# Android Emulator  


# Emulator (Command Line)  

> There is a fantastic resource for many emulator arguments on [developer.android.com](https://developer.android.com/studio/run/emulator-commandline).  

The command <font color="green">emulator</font> launches any installed Android virtual device ([here](learn_to_code/android/android_studio?id=adding-a-virtual-device) is how to install such a device in Android Studio). If you [installed the Android SDK](learn_to_code/android/android_studio?id=install-android-studio) in the default directory, <font color="green">emulator</font> is in `~/Android/Sdk/emulator`.  

It should be noted that <font color="green">emulator</font> is _not_ meant for interacting with the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) via shell or file transfers once its running (outside of the 'Extended Controls' or dropping APKs on the screen to load) - you would use <font color="purple">adb</font> for that.  

Typically, all of your stored [AVDs](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) are located in `~/.android/avd`.  

# Creating an AVD  

Before you can run any of these commands, you must create an [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd). Right now the way I do this is via [Android Studio](learn_to_code/android/android_studio?id=adding-a-virtual-device), but there may be a way to create an AVD without Android Studio (I just havent looked into it yet).  

# Simulated Emulator Interaction  

Here are some shortcuts for interacting with the emulator:  

| Keypress | Description |  
| --- | --- |  
| Ctrl + Right Click | Two-finger screen manipulation. It seems to move the simulation of pressing two fingers on the screen for a zoom, but I cannot figure out how to replicate the zoom. |  
| Ctrl + Left Click | Two-finger screen manipulation. The center of the screen acts as the midpoint, and moving the mouse will zoom in / out. Release the left mouse to stop zooming. |  
| Ctrl + Up | Increase screen size |  
| Ctrl + Down | Decrease screen size |  
| Ctrl + = | Volume up. |  
| Ctrl + - | Volume down. |  

> You can also drag and drop APKs from your host to the AVD and the APK will automatically install.  

---  

# Using Emulator help  

To view the help section of any `emulator` subcommand:  
```
emulator -help-SUBCOMMAND  
```  
* `SUBCOMMAND` is the _specific_ subcommand in question.  
* As an example, `emulator -help-no-boot-anim`  

# Show all Android Devices 

To show all [AVDs](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd), run:  
```
emulator -list-avds
```  

---  

# Booting  

## Run Specific Android Device

To run a specific [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd), make sure you have [created one](learn_to_code/android/android_studio?id=adding-a-virtual-device) first. Once you have the name, run:  
```
emulator -avd NAME_OF_DEVICE_HERE
```  
* This will launch the Android device `NAME_OF_DEVICE_HERE` in an emulation window.  
* The `NAME_OF_DEVICE_HERE` is the name of the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) as displayed from [showing all Android devices](learn_to_code/android/sdk_tools/emulator?id=show-all-android-devices).  

Conversely, you could preface the name with an `@` symbol for the same effect, like so:  
```
emulator @NAME_OF_DEVICE_HERE
```  

## Cold Boot  

When running the emulator command, an [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) will simply resume from its last [Quick Boot snapshot](learn_to_code/android/sdk_tools/emulator?id=quick-boot-snapshot); this helps with boot times, but if there was some error with the last [state](learn_to_code/computer_science_concepts?id=state), that error will persist the next time you boot the AVD. If you want to perform a cold boot - that is to say, boot from scratch - use the `-no-snapshot-load` argument like so:  
```
emulator -no-snapshot-load @NAME_OF_DEVICE_HERE
```  
* The `NAME_OF_DEVICE_HERE` is the name of the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) as displayed from [showing all Android devices](learn_to_code/android/sdk_tools/emulator?id=show-all-android-devices).  

If you want to issue a clean boot _as well as_ refrain from saving the [state](learn_to_code/computer_science_concepts?id=state) of the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) when you are finished (so deleting apps, etc will make no difference), use the `-no-snapshot` option instead.  

## Skip Loading Animation

To shave some time off of a cold boot, skip the loading animation with:  
```
emulator -no-boot-anim @NAME_OF_DEVICE_HERE
```  
* This scales the emulator window down by 50% on startup (effectively halving it).  


## Do Not Save State  

If you want to boot into a previous [state](learn_to_code/computer_science_concepts?id=state) but do _not_ want to save your current state when you exit the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) (so, for example, you can delete apps, and when you exit, they will be there again) you can use `-no-snapshot-save`:  
```
emulator -no-snapshot-save @NAME_OF_DEVICE_HERE
```  
* The `NAME_OF_DEVICE_HERE` is the name of the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) as displayed from [showing all Android devices](learn_to_code/android/sdk_tools/emulator?id=show-all-android-devices).  

If you want to issue a clean boot _as well as_ refrain from saving the [state](learn_to_code/computer_science_concepts?id=state) of the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) when you are finished (so deleting apps, etc will make no difference), use the `-no-snapshot` option instead.  

## Wipe Data  

If you want to _completely_ restore the image to its initial [state](learn_to_code/computer_science_concepts?id=state) (removing all changes you have made on the image) use the `-wipe-data` argument:  
```
emulator -wipe-data @NAME_OF_DEVICE_HERE
```  
* The `NAME_OF_DEVICE_HERE` is the name of the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) as displayed from [showing all Android devices](learn_to_code/android/sdk_tools/emulator?id=show-all-android-devices).  
* While I am not certain, I believe this also [issues a clean boot via -no-snapshot-load](learn_to_code/android/sdk_tools/emulator?id=cold-boot).  

## Multiple AVD Instances 

Normally, if you simply wanted to run multiple [AVDs](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd), you could do so with multiple _separate_ devices; however, if you wanted to run multiple instances of the _same_ device, you could do this with the `-read-only` agrument:  
```
emulator @NAME_OF_DEVICE_HERE -read-only  
```  
* The `NAME_OF_DEVICE_HERE` is the name of the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) as displayed from [showing all Android devices](learn_to_code/android/sdk_tools/emulator?id=show-all-android-devices).  
* This does not require a [cold boot](learn_to_code/android/sdk_tools/emulator?id=cold-boot).  
* Every instance will be read-only - no state will be saved.  
* _All_ instances of this AVD _must_ use the `-read-only` argument for this to work. 

## Headless Instance  

If you did _not_ want to have an associated visual window with the AVD, you could run it headless with the `-no-window` argument like so:
```
emulator @NAME_OF_DEVICE_HERE -no-window  
```  
* The `NAME_OF_DEVICE_HERE` is the name of the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) as displayed from [showing all Android devices](learn_to_code/android/sdk_tools/emulator?id=show-all-android-devices).  


---  

# Logging  

## Log Output  

> You can also do this [with the ADB logcat](learn_to_code/android/sdk_tools/adb?id=logging).  

If you wish to capture the log output in the terminal, use `-logcat`:
```
emulator @NAME_OF_DEVICE_HERE -no-snapshot-load -logcat "*:v"
``` 
* The `NAME_OF_DEVICE_HERE` is the name of the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) as displayed from [showing all Android devices](learn_to_code/android/sdk_tools/emulator?id=show-all-android-devices).  

`-logcat` takes a list of `tag:log level`, which identifies a tag and its associated log level. The log levels are:  

| log level | Description |  
| --- | --- |  
| v | verbose |  
| d | debug |  
| i | informative |  
| w | warning |  
| e | error |  
| s | silent |  

Each level shows itself and _all_ levels after that. For example, if you selected a log level of `i`, any log level deemed `i`, `w`, or `e` would be displayed (`v` and `d` level logs would be ignored, and `s` is effectively "do _not_ print this to the log, ever"). The wildcard `*` is accepted for tags, but instead of using the wildcard for the log level you should just use `v` (the lowest level).  

For example, lets say you wanted to completely silence _all_ tags _with the exception_ of `bluetooth` tags (of which you would like to see informative or higher) and `NearbySharing` tags (of which you would like to see warning or higher); you would make this logcat line as `-logcat "*:s bluetooth:i NearbySharing:w"`. Note the focus on tags and _not_ apps - unfortunately, it does not seem you can capture all logs generated by a _specific_ app, only the tags it uses). Apparently its not fully possible to capture the log output of a target app, unless the app uses one and only one tag (which doesnt seem to be likely).   


Here is an example log output:  
```
05-11 20:23:34.298   830   844 D ActivityAttributionService: notifyActivityAttributionInfo UID=1002 packageName=com.google.android.bluetooth.services deviceAddress=no_active_device_address
05-11 20:23:34.298  1802  4286 I BackupAndSyncValidation: Scheduling the service.
05-11 20:23:34.301   830   844 I bt_btif : packages/modules/Bluetooth/system/btif/src/bluetooth.cc:457 get_profile_interface: get_profile_interface: id = activity_attribution
05-11 20:23:34.301   830  1109 I bt_stack: [INFO:le_scanning_manager.cc(130)] Scan in shim layer
05-11 20:23:34.301   830  1120 I bluetooth: packages/modules/Bluetooth/system/gd/hci/le_scanning_manager.cc:594 stop_scan: Scanning already stopped, return!
05-11 20:23:34.306   830   844 I bt_stack: [INFO:le_scanning_manager.cc(124)] Unregister in shim layer, scanner_id:2
05-11 20:23:34.307  1228  3079 I Nearby  : [MBleClient] M hardware scan: 1 clients, scanMode= LOW_LATENCY, callback type= ALL_MATCHES [CONTEXT service_id=49 ]
05-11 20:23:34.307  1228  3079 D BluetoothAdapter: isLeEnabled(): ON
05-11 20:23:34.312   830   844 I bt_stack: [INFO:le_scanning_manager.cc(117)] RegisterScanner in shim layer
05-11 20:23:34.314  1228  4716 W NearbySharing: Cannot set visibility on DisabledNearbySharingProvider. [CONTEXT service_id=194 ]
05-11 20:23:34.316  1228  1246 D BluetoothLeScanner: onScannerRegistered() - status=0 scannerId=1 mScannerId=0
05-11 20:23:34.331  1228  4716 I NearbySharing: Broadcasting NearbySharing state change. [CONTEXT service_id=194 ]
05-11 20:23:34.332  1228  4716 I NearbySharing: Screen is locked [CONTEXT service_id=194 ]
```  

The categories of the log line, in order, are:  
* Timestamp (in local time).  
* The [PID](operating_systems/ubuntu/linux_notes?id=process-handling).  
* The Parent [PID](operating_systems/ubuntu/linux_notes?id=process-handling) (PPID) of the PID.  
* The log level (`v`/`d`/`i`/`w`/`e`, there should never be a `s`).  
* The tag (followed by a colon).  
* A description of the event (this can vary wildly).  

!> You may notice I included a [clean boot via -no-snapshot-load](learn_to_code/android/sdk_tools/emulator?id=cold-boot) along with `-logcat`; this is because I noticed that if you did not clean boot the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) and simply restored the previous [Quick Boot snapshot](learn_to_code/android/sdk_tools/sdk_basics?id=quick-boot-snapshot), _any and all log level requests_ would be ignored and whatever log levels were used to initially boot the device would be used. Apparently, it seems a cold boot is required if you wish to alter the log output.  

## Log Output (To File)  

You can also log the output to a file with the `-logcat-output` argument:  
```
emulator @NAME_OF_DEVICE_HERE -no-snapshot-load -logcat "*:v" -logcat-output /path/to/some/file/LogOutput.txt
``` 
* The `NAME_OF_DEVICE_HERE` is the name of the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) as displayed from [showing all Android devices](learn_to_code/android/sdk_tools/emulator?id=show-all-android-devices).  
* Notice you must still issue the [-logcat argument](learn_to_code/android/sdk_tools/emulator?id=log-output); if you do not, nothing will print to the file.  

## TCP Dump  

To capture network packets and store them to a .cap file, use the argument `-tcpdump` like so:  
```
emulator @NAME_OF_DEVICE_HERE -tcpdump /some/path/to/a/file.TCP_DUMP.cap
```  
* The `NAME_OF_DEVICE_HERE` is the name of the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) as displayed from [showing all Android devices](learn_to_code/android/sdk_tools/emulator?id=show-all-android-devices).  
* The file name and location is up to you.  
* This captures all ethernet packts and is not limited to TCP connections.  

# Hypervisor Settings  

These settings are not visible to the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd); they are set on the host outside of the AVD.  

## Set Ports  

Two ports on the host are needed for outside entities to interact with the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) - one for [the console](learn_to_code/android/sdk_tools/other_tools?id=console-commands) and the other for [adb](/learn_to_code/android/sdk_tools/adb).  The default is `5554` and `5555`, but if for whatever reason you cannot use these _or_ you wish to specify them, you can set the ports via:  
```
emulator @NAME_OF_DEVICE_HERE -port SOME_PORT
```  
* The `NAME_OF_DEVICE_HERE` is the name of the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) as displayed from [showing all Android devices](learn_to_code/android/sdk_tools/emulator?id=show-all-android-devices).  
* `SOME_PORT` is the port you wish to use on the host.  
* The above will set the port for [the console](learn_to_code/android/sdk_tools/other_tools?id=console-commands), but (`SOME_PORT` + 1) will be used for [adb](/learn_to_code/android/sdk_tools/adb) - know that whatever port `SOME_PORT` is you will _also_ be using the port after that one too.  
* The range is 5554 to 5682.  
* You should use an even port value.  
   * If its not, and if the [adb](/learn_to_code/android/sdk_tools/adb) server starts after the emulator, `adb devices` will _not_ see the port in use.  
   * Its best to use an even number for this.  
   
You can set _both_ ports with `-ports`:  
```
emulator @NAME_OF_DEVICE_HERE -port CONSOLE_PORT,ADB_PORT  
```  

However, **if at all possible** use the `-port` option instead of the `-ports` option.  

## Set DNS Server  

To specify DNS server(s) use the `-dns-server` argument:  
```
emulator @NAME_OF_DEVICE_HERE -memory 127.0.0.1,192.168.1.1
```  
* The `NAME_OF_DEVICE_HERE` is the name of the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) as displayed from [showing all Android devices](learn_to_code/android/sdk_tools/emulator?id=show-all-android-devices).  
* This accepts a comma separated list.  

## Set HTTP Proxy  

If your AVD requires a HTTP(s) proxy, you can specify that with:  
```
emulator @NAME_OF_DEVICE_HERE -http-proxy SERVER:PORT
```  
* The `NAME_OF_DEVICE_HERE` is the name of the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) as displayed from [showing all Android devices](learn_to_code/android/sdk_tools/emulator?id=show-all-android-devices).  
* You need to change `SERVER:PORT` to actual values.  
   * This also accepts `USERNAME:PASSWORD@SERVER:PORT`  

---  

# AVD Hardware Simulation  

This section covers how you can simulate hardware on the mobile device.  

## Set AVD Memory  

To set the memory, run:  
```
emulator @NAME_OF_DEVICE_HERE -memory 2048  
```  
* The `NAME_OF_DEVICE_HERE` is the name of the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) as displayed from [showing all Android devices](learn_to_code/android/sdk_tools/emulator?id=show-all-android-devices).  
* This sets the memory, in MB.  
* This can be checked with running `adb shell "cat /proc/meminfo"` in a separate terminal window.  
   * This is displayed in KB.  
* The [documentation](https://developer.android.com/studio/run/emulator-commandline) says this is a value from 128 to 4096 MBs, but I found the specific skin (or other factor) has a floor of 2048 and cannot go lower.  

## Disable Audio (From Device)  

by default, audio that would play on the device will play over your speakers; you can turn this off with the `-no-audio` argument:  
```
emulator @NAME_OF_DEVICE_HERE -no-audio  
```  
* The `NAME_OF_DEVICE_HERE` is the name of the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) as displayed from [showing all Android devices](learn_to_code/android/sdk_tools/emulator?id=show-all-android-devices).  

> There are some instances where sound makes the emulator crash; if you experience this, you should consider turning the sound off with the `-no-audio` argument.  

## Enable Microphone  

If you wish to send audio from your microphone to the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) (i.e. simulating talking into the device), you must enable this with the `-allow-host-audio` argument:  
```
emulator @NAME_OF_DEVICE_HERE -allow-host-audio  
```  
* The `NAME_OF_DEVICE_HERE` is the name of the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) as displayed from [showing all Android devices](learn_to_code/android/sdk_tools/emulator?id=show-all-android-devices).  

> If you have any issues with this, there is a good thread over at [stackoverflow](https://stackoverflow.com/questions/57189364/android-emulator-not-detecting-host-audio-input).  


## Enable Camera  

You can set your front _and_ back cameras via the `-camera-back` and `-camera-front` arguments. You also need to specify a 'mode' which is either `emulated`, `none`, or `webcam`<font color="red">n</font> where <font color="red">n</font> is the camera number (`webcam0` is usually your laptop camera, and `webcam1` is the first external camera you plug in).  For example, if you wanted the camera in the device to mirror your laptop camera, that would be:  
```
emulator @NAME_OF_DEVICE_HERE -camera-back webcam0
```  
* The `NAME_OF_DEVICE_HERE` is the name of the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) as displayed from [showing all Android devices](learn_to_code/android/sdk_tools/emulator?id=show-all-android-devices).  

## Install SD Card  

To simulate the installation of a SD card, use the `-sdcard` argument:  
```
emulator @NAME_OF_DEVICE_HERE -sdcard /path/to/your/SD-CARD-IMAGE-FILE.img
```  
* The `NAME_OF_DEVICE_HERE` is the name of the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) as displayed from [showing all Android devices](learn_to_code/android/sdk_tools/emulator?id=show-all-android-devices).  
* The `/path/to/your/SD-CARD-IMAGE-FILE.img` is the path to a [SD Image you created](learn_to_code/android/sdk_tools/other_tools?id=sd-card-creation); make **sure** you have created an image!    

There are some oddities about SD card images:  
* _Some_ skins - but apparently not all - come pre-bundled with a SC card already.  
   * These will be called `~/.android/avd/NAME_OF_AVD.avd/sdcard.img`
   * You can point to another SD card if you prefer.  
* While multiple images seem to be able to share the same image file, they _cannot_ share it if bot hare actively running - it seems corruption of the file is very likely. 
   * This corrupted my file every time I tried to save to the SD card image while another [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) was connected to it as well.
   * The corruption is not noticable until after you terminate both [AVDs](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) linking to the file.  
* If you include the command to [wipe data](learn_to_code/android/sdk_tools/emulator?id=wipe-data) in additon to the command above connecting a SD card image, it _will_ wipe / reset the image file too.  
   * The official docs say this will not happen, but I was able to do it predictably and consistently.  
   * This only seems to happen _if_ both the wipe data _and_ sd card arguments are present.  
* If you go from using a mounted SC card, exiting the emulator, then re-launching the emulator _without_ specifying the `-sdcard` argument, the [quick bootsnapshot](learn_to_code/android/sdk_tools/sdk_basics?id=quick-boot-snapshot) will be ignored and a [cold boot](learn_to_code/android/sdk_tools/emulator?id=cold-boot) will automatically  be issued.  

## Scale The Window  

To scale the emulator window on startup, use the `-scale X.X` argument like so:  
```
emulator -scale 0.5 @NAME_OF_DEVICE_HERE
```  
* This scales the emulator window down by 50% on startup (effectively halving it).  
* The `NAME_OF_DEVICE_HERE` is the name of the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) as displayed from [showing all Android devices](learn_to_code/android/sdk_tools/emulator?id=show-all-android-devices).  

> You can also change the window size on the fly by holding in `Ctrl` and pressing up / down (left / right flips the screen, as you would a phone).  

---  

# Custom Mobile Settings  

Mobile apps come with unique challenges - this section details some of the ways you can simulate those special situations or challenges in order to test how your app will act under these conditions.  

## Set Network Delay  

Mobile phones can have network latency / delay. You can mimic this delay with:
```
emulator @NAME_OF_DEVICE_HERE -netdelay DELAY_VALUE
```  
* The `NAME_OF_DEVICE_HERE` is the name of the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) as displayed from [showing all Android devices](learn_to_code/android/sdk_tools/emulator?id=show-all-android-devices).  
* You need to change `DELAY_VALUE` to an actual value - either a hardcoded `min:max` _or_ a single number _or_ a preset (as defined below).  

| Delay Value | Description | min (milliseconds) | max (milliseconds) |  
| --- | --- | --- | --- |  
| gsm | GSM / CSD | 150 | 550 |  
| hscsd | HSCSD | 80 | 400 |  
| gprs | GPRS | 35 | 200 |  
| edge | EDGE / EGPRS | 80 | 400 |  
| umts | UMTS / 3G | 35 | 200 |  
| hsdpa | HSDPA | 0 | 0 |  
| lte | LTE | 0 | 0 |  
| evdo | EVDO | 0 | 0 |  
| none |  | 0 | 0 |  
* The default is `none`.  

## Set Network Speed  

Mobile phones can have different network speeds - you can set this with:
```
emulator @NAME_OF_DEVICE_HERE -netspeed SPEED_VALUE
```  
* The `NAME_OF_DEVICE_HERE` is the name of the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) as displayed from [showing all Android devices](learn_to_code/android/sdk_tools/emulator?id=show-all-android-devices).  
* You need to change `SPEED_VALUE` to an actual value - either with hardcoded `up:down` numbers _or_ a single number _or_ a preset (as defined below).  

| Speed Value | Description | up (kbps) | down (kbps) |  
| --- | --- | --- | --- |  
| gsm | GSM / CSD | 14.4 | 14.4 |  
| hscsd | HSCSD | 14.4 | 57.6 |  
| gprs | GPRS | 28.8 | 57.6 |  
| edge | EDGE / EGPRS | 473.6 | 473.6 |  
| umts | UMTS / 3G | 384 | 384 |  
| hsdpa | HSDPA | 5760 | 13,980 |  
| lte | LTE | 58,000 | 173,000 |  
| evdo | EVDO | 75,000 | 280,000 |  
| full | No limit. |  |  |  
* The default is `full`.  

---  

# Other Emulator Commands  

I only covered a few of the most helpful commands in this section; there are a few dozen more `emulator` commands at your disposal. You can check them out [here](https://developer.android.com/studio/run/emulator-commandline).  




