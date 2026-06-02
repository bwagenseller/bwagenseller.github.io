# Asterisk Basics  

# Resources  

* [Asterisk](https://www.asterisk.org/)  
* [FreePBX](https://www.freepbx.org/)  
* [Asterisk Tutorial on YouTube](https://www.youtube.com/watch?v=jMQfSsO1da4&list=PLnzEbgyK52Gu9fdVDHburrsG3KBIntXFK&index=1)  
* [Asterisk-Service](https://asterisk-service.com/en_US/), which has a [resource page](https://asterisk-service.com/en_US/page/asterisk-books)  
  * <font color="red">Be warned</font> that this seems to be a Russian site and is almost assuredly not an official Asterisk site.  
* [freecomputerbooks.com](https://freecomputerbooks.com/Asterisk-The-Definitive-Guide.html)  
* [vdoc.pub](https://vdoc.pub/search/Asterisk/2)  
* [](https://community.asterisk.org/)


---  

# What Is Asterisk / FreePBX  

[Asterisk](https://www.asterisk.org/) is both a [PBX](telephony/terms?id=private-branch-exchange) and a <font color="green">SIP router</font> - it can route a SIP call from one endpoint to another. It can do much more than this, though - it can also provide voicemail services, call queues, AI Speech recognition, etc (it does these additional features through add-ons).

[FreePBX](https://www.freepbx.org/) is a web-based GUI for Asterisk; you dont _need_ to use FreePBX - and you _should_ eventually learn the direct Asterisk commands - but its easier for beginners. Once you install FreePBX, you can access the webpage by opening your browser and navigating to the IP of the host where you installed FreePBX/Asterisk (i.e. if you installed it on a host with a local IP of `192.168.1.200`, you can access your page at `http://192.168.1.250/`).  

> [Kamailio](https://www.kamailio.org/w/) is an alternative to Asterisk - and can actually handle thousands of calls at a time (whereas Asterisk cannot). That said, Asterisk has a more diverse feature set. Its mentioned [in this video](https://www.youtube.com/watch?v=jMQfSsO1da4) that many people use Kamailio as a Session Border Controller (SBC) that feeds Asterisk instances - therefore getting the best of both worlds. 

---   

# Terms  

> This section will deal with terms _as they relate to Asterisk_; other telephony terms - many of which are used in Asterisk - can be found [here](/telephony/terms).  

## Channel Event Logging (CEL)

<font color="green">Channel event logging</font> (<font color="green">CEL</font>) provides additional elements that can supplement [CDRs](telephony/terms?id=call-data-record-cdr) (which are also captured by Asterisk [if enabled](telephony/asterisk/modules?id=call-detail-recording-modules)). You can read more about it [here](telephony/asterisk/modules?id=channel-event-logging-modules).  

## Module  

A <font color="green">module</font> is a component that provides _specific_ functionality; for example, a resource that allows connection to an external technology (i.e. ChatGPT, Text-To-Voice, etc).  

Most things worth doing in Asterisk require a module.  

Some common modules (examples com from [Asterisk: The Definitive Guide, 5th Edition](https://www.oreilly.com/library/view/asterisk-the-definitive/9781492031598/)):  
* Applications - The workhorses of the dialplan, such as Dial(), Voicemail(), Playback(), Queue(), etc
* Bridging modules - Mechanisms that connect channels (calls) to each other
* Channel event logging (CEL) modules  
* Channel drivers - Various connections into and out of the system; SIP (Session Initiation Protocol) messaging uses the PJSIP channel drivers  
* Codec translators - Convert various codecs such as G729, G711, G722, Speex, and so forth  
* Format interpreters - As above, but relating to files stored in the filesystem  
* Dialplan functions - Enhance the capabilities of the dialplan  

---  

# Installation  

 ## Installing Debian  

The recommended OS for Asterisk (and a few other telephony-related apps) is [Debian](https://www.debian.org/). You dont _have_ to use Debian, but it is recommended. The [FreePBX](https://www.freepbx.org/get-started/) site gives [this guide to installing Debian](https://sangomakb.atlassian.net/wiki/spaces/FP/pages/295403538), or you can do it on your own.  

A few tips from me:  
* If installing from a USB, it _cannot be partitioned_ - it _must_ be on a USB that uses the entire space on the USB (i.e. if its something like `/dev/sdc1` it wont work, but `/dev/sdc`will work)  
* I had trouble with the installation process recognizing my wifi adapter - you may need an ethernet cable.  
* Unlike Ubuntu, it seems every account can access root  
  * Also, root is `su -` and not `sudo su -`  
* You will want to install these additional packages (as recommended by [FreePBX](https://sangomakb.atlassian.net/wiki/spaces/FP/pages/295403538)): `apt-get -y install net-tools htop screen tshark vim sngrep`  
* You will also want to stop sleeps - do so by editing `/etc/systemd/sleep.conf` and enter the following:  
```  
[Sleep]
AllowSuspend=no
AllowHibernation=no
AllowSuspendThenHibernate=no
AllowHybridSleep=no
```  
  * This was taken from [the Debian forums](https://forums.debian.net/viewtopic.php?t=156005)  
  * The initial suggested file was `/etc/systemd/sleep.conf.d/nosuspend.conf` but I edited the above - if this remains, the above worked.  

## Installing FreePBX / Asterisk  

> Please note that FreePBX _also_ installs Asterisk.  

FreePBX offers an install script you can run, using a script from its [github page](https://github.com/FreePBX) (the instructions are [here](https://sangomakb.atlassian.net/wiki/spaces/FP/pages/230326391/FreePBX+17+Installation)). This assumes you have already installed [Debian 12](telephony/asterisk/asterisk_basics?id=installing-debian).  

To install FreePBX:  

1\. Become root: `su -`  

2\. Run: `cd /tmp`  

3\. Run: `wget https://github.com/FreePBX/sng_freepbx_debian_install/raw/master/sng_freepbx_debian_install.sh  -O /tmp/sng_freepbx_debian_install.sh`  

4\. Run: `bash /tmp/sng_freepbx_debian_install.sh`  

5\. You may have to disable sleep - possibly a second time, as this was seemingly not a problem for me until  installed FreePBX. To do so, edit `/etc/systemd/sleep.conf` and enter the following:  
```  
[Sleep]
AllowSuspend=no
AllowHibernation=no
AllowSuspendThenHibernate=no
AllowHybridSleep=no
```  
  * This was taken from [the Debian forums](https://forums.debian.net/viewtopic.php?t=156005)  
  * The initial suggested file was `/etc/systemd/sleep.conf.d/nosuspend.conf` but I edited the above - if this remains, the above worked.  


> You do not _have_ to install FreePBX - you are free to install Asterisk by itself.  

## Additional Tools  to Install  

* `apt-get install setools auditd `  
  * auditd: Provides the audit daemon which logs events from the Linux kernel and user space applications. It can be used to monitor and log SELinux denials.    

## Install Check  

> This section _should_ be handled by the install process - but if not - or if you installed from scratch - you will have to do it manually.  

* Make sure the following are owned by `asterisk:asterisk`  
  * /etc/asterisk  
  * /var/lib/asterisk  
  * /var/spool/asterisk  
  * /var/log/asterisk  
  * /var/run/asterisk  
* Add a rule to the /etc/tmpfiles.d folder, which allows Asterisk to create sockets at runtime.  
  * Open `/etc/tmpfiles.d/asterisk.conf` for editing (as root), then enter this into the contents  
```
d /var/run/asterisk 0775 asterisk asterisk  
```  
  * The file `/etc/tmpfiles.d/asterisk.conf` was not present on my machine after the install, so...this may or may not be needed.  

---  

# Important Files and Locations  

!> It should be the case that all files related to Asterisk are owned by `asterisk:asterisk`, as Asterisk should run as `asterisk` and not as root.  

## Main File Location  

The typical location for most of the critical files is `/etc/asterisk`.  

## asterisk.conf  

`asterisk.conf` lists the directories of where various Asterisk file types list; you probably shouldnt change these unless there is a need. For example, there is an 'Asterisk Etc Directory' entry: `astetcdir => /etc/asterisk`. If you ever wanted to change where the files that belong in this directory should be located, you would have to change this. Most people don't.  



## Modules  

See more of this on the [modules page](telephony/asterisk/modules).  

## Resource Library  

The directory `/var/lib/asterisk` is where system prompts, AGI scripts, music on hold, and other resource files are stored.  

## The Spool  

In Asterisk, the spool is used to store transient items such as voice messages, call recordings, call files, and so forth. Its located in `/var/spool/asterisk`.  

## logger.conf  

`logger.conf` is the configuration file for general logging in Asterisk. Located in `/etc/asterisk` (and if you wre using freePBX, this wold be a link to `/var/www/html/admin/modules/logfiles/etc/logger.conf`). The _actual_ logs are stored in `/var/log/asterisk`.  

A basic log configuration file looks like this:
```
[general]
exec_after_rotate=gzip -9 ${filename}.2;
[logfiles]
;debug => debug
;security => security
console => notice,warning,error,verbose
;console => notice,warning,error,debug
messages => notice,warning,error
full => notice,warning,error,debug,verbose,dtmf,fax
;full-json => [json]debug,verbose,notice,warning,error,dtmf,fax
;syslog keyword : This special keyword logs to syslog facility
;syslog.local0 => notice,warning,error
```
* From [Asterisk: The Definitive Guide, 5th Edition](https://www.oreilly.com/library/view/asterisk-the-definitive/9781492031598/)  
* note the commented lines (which start with a `;`); these are left in, as sometimes you want to quickly change the properties of the logs.  

Another practice - one that FreePBX uses - is to manage this in four files that are included in `logger.conf`; in that case, `logger.conf` would simply look like this:  
```
[general]
#include logger_general_additional.conf
#include logger_general_custom.conf

[logfiles]
#include logger_logfiles_additional.conf
#include logger_logfiles_custom.conf
```  
* This simply includes the named 4 files - you make changes in those files instead of `logger.conf`  



---  

# Old Sections  

## Launching Asterisk Interface

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

## ARI Debug Messages

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

## SIP Debug Messages

To turn on SIP debug messages, [launch the Asterisk interface](telephony/asterisk/asterisk_basics?id=launching-asterisk-interface) and then type:  
```
sip set debug on
```

To turn of SIP debug messages, [launch the Asterisk interface](telephony/asterisk/asterisk_basics?id=launching-asterisk-interface) (if you are not in it already) and then type:  
```
sip set debug off
```

## Show Active Bridges  

To show active bridges in Asterisk, become root and then run: `asterisk -rvvvvvvvx "bridge show all"`  
