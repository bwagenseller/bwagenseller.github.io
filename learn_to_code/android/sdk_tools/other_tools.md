# Android - Other Tools  


# Console Commands  

Console commands can alter various states of the [AVD](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd). I am not going to go deep into console commands, as you can effectively execute almost all of them from the AVD toolbox (that appears to the right of the AVD); you can find these console commands [here](https://developer.android.com/studio/run/emulator-console).  

That said, to _initially_ get into the console you need the port information you will need its telnet port - to get that, make sure the device is running and then run `adb devices`; for me this gives:  
```
List of devices attached
emulator-5554	device
```  

Acording to the above, my device is running on the hypervisor port `5554` (if you wish to explicity set the ports, [see here](learn_to_code/android/sdk_tools/emulator?id=set-ports)).  

You will _also_ need the auth token, which is in the file `~/.emulator_console_auth_token`.  

To use telnet to console into your AVD:  
1\. type `telnet localhost YOUR_PORT_HERE`  
2\. Once in, you must type `auth YOUR_AUTH_TOKEN_HERE`.  
3\. From here you can issue console commands.  


# SD Card Creation  

You can make a SD card with the `mksdcard` command. The command is pretty straightforward:
```
mksdcard -l someSdCard 1024M someSdCard.img
```  
* The first argument `-l someSdCard` creates a label for the SD Card image - this name can be anything you wish.  
* The second argument `1024M` is the SD card size
   * You can specify this in bytes, or you can put a `K` behind the number for kilobytes, `M` for megabytes (as in the example above), or `G` for gigabytes.  
   * The minimum is `9M` and the max is `1023G`.  
* The last argument is the path / filename of the image. 
   * It should end in `.img`.  
   
It appears _multiple_ [AVDs](learn_to_code/android/sdk_tools/sdk_basics?id=android-virtual-device-avd) can use the same SD card image, but _not_ running concurrently (so multiple AVDs can link to the same SD card, but they cant run at the same time unless you dont care about the SD card corrupting).  
