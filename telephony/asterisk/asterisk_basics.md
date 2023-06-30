# Asterisk Operations

# Launching Asterisk Interface

> I initially found some of this on [asteriskdocs.org](http://asteriskdocs.org/en/2nd_Edition/asterisk-book-html-chunk/asterisk-CHP-4-SECT-10.html).

To launch the Asterisk interface (so you can issue commands to a running instance of Asterisk), type the following on any command prompt:
```
asterisk -r
```

This starts a _remote_ console; this can be a bit confusing as the above will only connect you to the running Asterisk on your local server, but in this case, _remote_ means 'connecting to a running instance of Asterisk'. 

You can start Asterisk and then run the console in one command if you use `-c` instead of `-r`, but this means when you quit the console, Asterisk itself will quit, which is usually undesirable.

You can also start Asterisk with a certain level of verbosity in the debug messages, you can add 1 to 10 'v's in the flags (as there are 10 levels of verbosity in Asterisk); to start on level 5 verbosity, for example, you would type this to enter the console:
```
asterisk -rvvvvv
```
* verbosity=9 seems to be a good debug level.

# ARI Debug Messages

!> Note that changing the ARI debug messages can _completely_ hose the Asterisk server - if calls stop processing, turn the debug off (whichever you turned on) and see if calls work again. Its always a good idea to turn off ARI debug messages you turned on once your work is complete.

To turn on ARI debug messages, [launch the Asterisk interface](telephony/asterisk/asterisk_basics?id=launching-asterisk-interface) and then type:  
```
ari set debug XXX on
```

where `XXX` is one of the following:  
* `all`  
 * This can break the system, causing Asterisk to not process any calls - be careful with this.  
* `dialer`  
 * Seems to be popular when dealing with calls.  
 * Turning this on may half-break the system; sometimes it will ring on the client side but will not ring the foreign party. Be careful with this.  

> If you ever forget the list of which can be turned on, simply type `ari set debug` and then press `Tab` twice.

To turn ARI debug messages off, [launch the Asterisk interface](telephony/asterisk/asterisk_basics?id=launching-asterisk-interface) (if you are not in it already) and then type:  
```
ari set debug XXX off
```

where `XXX` is one of the above options.  


> Due to the system tanking when ARI debug messages are turned on, you may have to place the call and then quickly turn on your debug messages, reviewing the event in the debug messages, then turning the debug messages off quickly.

# SIP Debug Messages

To turn on SIP debug messages, [launch the Asterisk interface](telephony/asterisk/asterisk_basics?id=launching-asterisk-interface) and then type:  
```
sip set debug on
```

To turn of SIP debug messages, [launch the Asterisk interface](telephony/asterisk/asterisk_basics?id=launching-asterisk-interface) (if you are not in it already) and then type:  
```
sip set debug off
```

# Show Active Bridges  

To show active bridges in Asterisk, become root and then run: `asterisk -rvvvvvvvx "bridge show all"`  
