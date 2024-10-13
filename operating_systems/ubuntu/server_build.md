# Server Build
<!-- clear for public use -->

# Overview

This page is to help you set up an Ubuntu server. It lays out several installs that I find helpful.

!> This setup assumes you have installed Ubuntu Linux on a server and now you have to install various packages.

# Before We Start (Notes On This Section)

This is a page dedicated to getting Ubuntu up and running (as well as installing various packages I use frequently in a Linux/Debian/Ubuntu environment). This assumes you currently have an Ubuntu / Debian OS; to get started with that, [see here](operating_systems/ubuntu/server_build?id=getting-and-installing-the-iso).

## Section Indicators

There are codes to indicate which sections you will need for what your server must do. Sections marked with:
* <span style='width: 20px; display:inline-block'>:card_index:</span> are required for servers that will house a database.
* <span style='width: 20px; display:inline-block'>:open_file_folder:</span> are required for servers that will interact with a database (not hosting one, but simply interacting with one).
* <span style='width: 20px; display:inline-block'>:snake:</span> are required for servers that will run Perl or Python code.
* <span style='width: 20px; display:inline-block'>:satellite:</span> are required for servers that will run the Apache web server.
* <span style='width: 20px; display:inline-block'>:bangbang:</span> are optional (or situational).
* <span style='width: 20px; display:inline-block'>:frog:</span> are for desktop versions of Ubuntu only.


## UNIX / Linux Help

I have written a short document on some critical Linux commands / ideas - if you wish to read it, [its located here](/operating_systems/ubuntu/linux_notes).

## Installing Alongside Windows

Usually, you will get an option to install alongside Windows (instead of over-writing it); that said, sometimes this option does not appear. If this happens, you can either follow [this](https://www.tecmint.com/install-ubuntu-alongside-with-windows/) guide or the following instructions (I tried these exact instructions and Windows failed to load afterwards; I then followed the below instructions which seemed to work). The basic premise is to carve out a section of the disk at the end of the partition in Windows (using `diskmgmt.msc`) and then installing Ubuntu to this partition.

The instructions I follow:

1\. Save a Windows system image to a back-up drive (either DVD, Blue Ray, or USB hard drive). You should also create a Windows rescue disk.

2\. _In Windows_, search for 'defrag' in the search box and select 'Defragment and Optimize Drives'.

3\. A box will open, showing every drive Windows can optimize. Individually select each one and click the 'Optimize' button. Finish and close this box.

4\. _In Windows_, search for 'partition' and select 'Create and format hard disk partitions'.

5\. A box will open, showing all partitions visible to Windows. Select the partition that says 'Windows (C:)', right click it, and select 'Shrink Volume...'.

6\. You will now have to select the size of the partition you want to create for Ubuntu. In the 'Enter the amount of space to shrink in MB', put the size of the partition you wish to create for Ubuntu in this box (so for example, if you wanted 100 GB for Ubuntu, place 102400 in this box). Note that you cannot give more than the free space available on the C: drive, and you will probably want to leave several free GB available to Windows (so leave some breathing room for the Windows partition).

7\. Click 'Shrink', and any other affirmative boxes until all boxes related to 'Create and format hard disk partitions' are closed.

8\. Put the Ubuntu install disk into the DVD drive.

9\. Shut down Windows.

10\. Boot the laptop to the DVD drive, which will begin the Ubuntu installation.

11\. When you get to the screen 'Installation type', you will have the option to 'Install Ubuntu alongside Windows...', 'Erase Disk and Install Ubuntu', or 'Something else'; here, pick 'Install Ubuntu alongside Windows...' and continue.

12\. Complete the Ubuntu installation.

13\. Test both the boot to Windows and the boot to Ubtuntu; if one of them does not work, revert to your saved Windows system image.




## Running Terminal Commands
If anything must be run on the command line, it will be included in a code block as such:
```
ls -ltr
```

Anything in code blocks such as this must be run on the Linux command line (unless otherwise specified); if there are multiple lines, run the commands in that order.  Occasionally, you may see an inline code as well like this: `ls -ltr`.

## Text Entry into Files
If you have to enter anything into a file, these lines will appear like this:
> Enter this line into the file. <br>
> Also enter this one

Finally, it is assumed that for the setup you will [be root](/operating_systems/ubuntu/linux_notes?id=becoming-root) unless otherwise stated.

## Getting (and Installing) the ISO 

!> You will have to decide if you want a desktop or server version of Ubuntu; the only real difference is the server version does not have a desktop GUI and has only bare-bones sources in [sources.list](operating_systems/ubuntu/linux_notes?id=changing-update-sources-sourceslist). I suggest getting the desktop version, even if you want a server: the desktop version can also function as a server. The only downside is it takes up more space, so if that is a concern stick with the server implementation.

If you havent installed Unbuntu Linux on your desktop / server, you will have to download the ISO image first [here](https://www.ubuntu.com/#download) (you will have to pick either server or desktop). The ISO image is the 'install' package - most operating systems use an ISO image for an install. Once you have the ISO, the traditional thing to do is burn its contents to a DVD (double clicking the ISO will usually prompt your DVD burner software to do this). There are other things you can do with the ISO (like [install an OS (like Ubuntu) to a VM](operating_systems/ubuntu/package_operations/kvm_notes?id=creating-a-vm-in-kvm-gui)).

Once the DVD is made, simply put it in the DVD drive and boot from CD; from there just follow the instructions. Once this is complete, you will have an Ubuntu system operational!

!> You will have to make sure your DVD boots first before your hard drive. Unfortunately, this is different for each server / desktop, but you usually have to mash `escape` or one of the function keys (`f9`, `f10`, `f11`, and `f12` are popular candidates - see [here](https://www.lifewire.com/how-to-enter-bios-2624481) for some more tips) as soon as you turn the machine on to enter the setup. Once there, look for something that is similar to 'boot options' in the [bios](https://en.wikipedia.org/wiki/BIOS) and either boot directly from the CD/DVD or make sure the CD/DVD drive is the primary boot device.

# Setting up the Static IP

> If the IP you are using is a static IP, follow these steps.

!> if you are using DCHP (dynamic IP, which is probably what you have if you did not request a static IP) and set up your system as a static, it will work until the IP changes.  If your system suddenly loses connectivity to the internet and you set it up as a static IP try un-doing these steps in case you actually have a dynamic IP.

!> If this is done remote you may lose connection; if this happens, you will NOT be able to interact with this server further! Make SURE you are physically near this server before doing this or else you will not be able to interact with the server until you (or someone you know) can physically get to this server!

You will need your current IP configuration, as well as the static IP (if you don't know it, these instructions assume the router already assigned it to you via dhcp - but make sure this is the case).

You will need a bunch of different IPs (whereas the static IP is simply one of them); to get the IPs you will need, use the command: `ifconfig –a`

Here is an example:
```
enp2s0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.160  netmask 255.255.255.0  broadcast 192.168.1.255
        ether d6:ce:a8:2c:53:45  txqueuelen 1000  (Ethernet)
        RX packets 1781846  bytes 2394915320 (2.3 GB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1058312  bytes 186362562 (186.3 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
* Take note of the following IP values:
 * inet addr (the above is 192.168.1.160, which is your IP address; this will eventually be the static IP address) 
 * Bcast addr (the above is 192.168.1.255)
 * Mask address (the above is 255.255.255.0)
* You will also need to get the network interface; in the example above its `enp2s0` but it will probably be `eth0` or `eno1` for you (there are no guarantees). The rule of thumb is its the IP after 'inet' that is not 127.0.0.1.

## Static IP (Ubuntu 16.04 and Below)

!> These steps changed in Ubuntu 17.10 when the move to NetworkManager / netplan was enforced; If you have a version of Ubuntu at or above 17.10, use [these instructions](operating_systems/ubuntu/server_build?id=static-ip-ubuntu-1710-and-above) instead!

** Steps:

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. Take note of the IPs you will need (as detailed [above](operating_systems/ubuntu/server_build?id=setting-up-the-static-ip).)

3\. Edit the /etc/network/interfaces file with the vi editor:
```
vi /etc/network/interfaces
```

4\. Find - and then comment out - the following line by placing a # at the beginning of it:
> iface NETWORK_INTERFACE_HERE inet dhcp

* You will have to put in your NETWORK_INTERFACE_HERE above

5\. Add these lines after the commented line above:
> iface NETWORK_INTERFACE_HERE inet static <br>
> address YOUR_INET_ADDRESS_HERE <br>
> netmask YOUR_MASK_ADDRESS_HERE <br>
> gateway YOUR_INET_ADDRESS_WITH_THE_LAST_NUMBER_REPLACED_BY_ONE_HERE <br>
> network YOUR_INET_ADDRESS_WITH_THE_LAST_NUMBER_REPLACED_BY_ZERO_HERE <br>
> broadcast YOUR_BCAST_ADDRESS_HERE <br>
> dns-nameservers 172.27.1.20 172.27.17.16 172.27.17.17 <br>
> dns-search YOUR_COMPANYS_DNS_SEARCH_IP_OR_FQDN

6\. Save and exit vi

7\. Type this command

!> If this is done remote you WILL lose connection!

```
ifdown NETWORK_INTERFACE_HERE
```

8\. Type this command to bring the connection back up:

!> This will have to be done locally, i.e. not remotely.

```
ifup NETWORK_INTERFACE_HERE
```

## Static IP (Ubuntu 17.10 and Above)

!> These steps changed in Ubuntu 17.10 when the move to NetworkManager / netplan was enforced; If you have a version of Ubuntu below 17.10, use [these instructions](operating_systems/ubuntu/server_build?id=static-ip-ubuntu-1604-and-below) instead!

** Steps:

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. Take note of the IPs you will need (as detailed [above](operating_systems/ubuntu/server_build?id=setting-up-the-static-ip).)

3\. Go into the directory `/etc/netplan`, find the file that ends in .yaml (for me it was `01-network-manager-all.yaml`, but it could be different for you), and back it up:
```
cd /etc/netplan
ls -la *.yaml
cp 01-network-manager-all.yaml 01-network-manager-all.yaml.bak
```

4\. Make sure the file looks like this:
```
# Let networkd manage all devices on this system
network:
  version: 2
  renderer: networkd
  ethernets:
  #declare the interface used y the bridge device
    NETWORK_INTERFACE_HERE:
     dhcp4: no
     dhcp6: no
     addresses: [YOUR_INET_ADDRESS_HERE/24]
     gateway4: YOUR_INET_ADDRESS_WITH_THE_LAST_NUMBER_REPLACED_BY_ONE_HERE
     nameservers:
       addresses: [8.8.8.8,8.8.4.4]
```
* Note **indents (the leading spaces and / or tabs) are are critical in .yaml files - so make sure they are exact.**
* Replace the word NETWORK\_INTERFACE\_HERE with the interface you found [here](operating_systems/ubuntu/server_build?id=setting-up-the-static-ip).
* 'dhcp' basically means 'ask for a random IP', but since we are getting a static IP we set this to no for 4 and 6.
* Replace the word YOUR\_INET\_ADDRESS\_HERE with the inet address you found [here](operating_systems/ubuntu/server_build?id=setting-up-the-static-ip).
* The addresses for the nameservers are a bit tricky; 8.8.8.8 and 8.8.4.4 are public Google DNS servers and will work for most applications
* If you need more examples for configuring this .yaml, [see this page](https://cloudinit.readthedocs.io/en/latest/topics/network-config-format-v2.html#examples).
* If you want another example of how to set up a static IP, [see this page](https://arador.com/how-to-configure-a-static-ip-address-in-ubuntu-18-04/).

5\. Save and exit

6\. Re-boot the server
```
systemctl reboot
```

7\. Test with a `ping` command; I use `ping android.com`. If the ping can get through, the static IP worked!

```
PING android.com (172.217.11.36) 56(84) bytes of data.
64 bytes from lga25s61-in-f4.1e100.net (172.217.11.36): icmp_seq=1 ttl=53 time=18.0 ms
64 bytes from lga25s61-in-f4.1e100.net (172.217.11.36): icmp_seq=2 ttl=53 time=15.6 ms
64 bytes from lga25s61-in-f4.1e100.net (172.217.11.36): icmp_seq=3 ttl=53 time=15.8 ms
64 bytes from lga25s61-in-f4.1e100.net (172.217.11.36): icmp_seq=4 ttl=53 time=13.9 ms
64 bytes from lga25s61-in-f4.1e100.net (172.217.11.36): icmp_seq=5 ttl=53 time=16.5 ms
^C
--- android.com ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4006ms
rtt min/avg/max/mdev = 13.992/16.011/18.039/1.315 ms
```

---

# Bonding
<span style='width: 20px; display:inline-block'>:bangbang:</span> <span style='width: 20px; display:inline-block'>:crystal_ball:</span>

`Bonding` is basically the idea that we 'bond' together network interfaces in the host so they appear as one interface; you can read more on it at [Steve's Solaris/Linux experiences](http://stivesso.blogspot.com/2014/07/linux-kvm-network-bridge-over-bonded.html).

> Bonding is only necessary if there are multiple, active internet connections; if this is not the case, bonding is usually unnecessary.


## Bonding (Ubuntu 16.04 and Before)

!> If you are on Ubuntu 17.10 or later, [use these instructions](operating_systems/ubuntu/server_build?id=bonding-ubuntu-1710-and-after) instead!

Steps:

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root).

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages).

3\. Install necessary packages. We will need a few tools to set up the bonding, so install the following:
```
apt-get install ifenslave-2.6
apt-get install ethtool
```
* ifenslave-2.6 is need to bond one interface
* ethtool is needed to check out the interface

4\. Set the host server to have a static IP. For home networks this is usually done within your router, but behind enterprise routers you may have to [follow these instructions](operating_systems/ubuntu/server_build?id=setting-up-the-static-ip). 

> Take note of the static IP for the host.

5\. Run `ifconfig -a` to view all interfaces. For my home system this was:
```
eno1: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        ether b3:ca:4d:95:3d:8e  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        device interrupt 20  memory 0xf7c00000-f7c20000  

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 917  bytes 71828 (71.8 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 917  bytes 71828 (71.8 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

virbr0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 192.168.122.1  netmask 255.255.255.0  broadcast 192.168.122.255
        ether 74:5e:00:2c:10:ae  txqueuelen 1000  (Ethernet)
        RX packets 37285  bytes 2098388 (2.0 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 74509  bytes 112138527 (112.1 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

virbr0-nic: flags=4098<BROADCAST,MULTICAST>  mtu 1500
        ether 74:5e:00:2c:10:ae  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

wlx00e02dc210e0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.133  netmask 255.255.255.0  broadcast 192.168.1.255
        inet6 fe80::eafc:121b:3cca:269f  prefixlen 64  scopeid 0x20<link>
        ether 03:e8:a5:c6:10:e4  txqueuelen 1000  (Ethernet)
        RX packets 833040  bytes 1089624115 (1.0 GB)
        RX errors 0  dropped 2  overruns 0  frame 0
        TX packets 455562  bytes 66128963 (66.1 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

Usually your `interface` is the one with the `inet` IP that is not 127.0.0.1; in this case there are two (interfaces virbr0 and wlx00e02dc210e0). Since `virbr0` is a virtual interface for KVM, wlx00e02dc210e0 must be the interface for my host that connects to the router (and it is - this is my WiFi interface).

> Your interface will probably be `enp2s0`, `eth0`, or `eno1` but it can vary - use the above method to find your interface.

6\. Check the link. Running the command `ethtool <interface>` should give you a message like `Link detected: yes`; an example is:
```
ethtool eno1
```

7\. Open the file `/etc/modules` for editing and add the following to the bottom of the file (then save/exit):

> bonding

8\. Stop networking, then load the bonding kernal module:
```
service networking stop
modprobe bonding
```

9\. Back-up the file `/etc/network/interfaces` and then open it for editing (with vi):
```
cp /etc/network/interfaces /etc/network/interfaces.orig
vi /etc/network/interfaces
```

You will see something like this in the file (this should already exist in the file):
```
# The loopback network interface <br>
auto lo
iface lo inet loopback
```

You will have to add the following to the end of the file `/etc/network/interfaces`:
```
# The primary network interface
auto INTERFACE_GOES_HERE
iface INTERFACE_GOES_HERE inet manual
bond-master bond0
bond-primary INTERFACE_GOES_HERE

auto bond0
iface bond0 inet static
        address STATIC_IP_GOES_HERE
        netmask YOUR_MASK_ADDRESS_HERE
        network YOUR_INET_ADDRESS_WITH_THE_LAST_NUMBER_REPLACED_BY_ZERO_HERE
        gateway YOUR_INET_ADDRESS_WITH_THE_LAST_NUMBER_REPLACED_BY_ONE_HERE
        dns-nameservers 8.8.8.8
        bond-mode active-backup
        bond-miimon 100
        bond-slaves none
```

* The INTERFACE\_GOES\_HERE is the interface you found in step 5 (its probably `enp2s0`, `eth0`, or `eno1` but it could be something else).
* The STATIC\_IP\_GOES\_HERE is the static IP of the host server you found /set-up in step 5.
* The netmask is probably 255.255.255.0, but change it if its not.
* The gateway is the IP to get into your router; its usually 10.0.0.1 or 192.168.1.1 for home routers; check for your company's routers.
* The dns-nameservers is a listing of servers that can be used for DNS work; this can be any server built for this, including external public servers (8.8.8.8 is a DNS public server for Google).

10\. Reboot server and then bring the networking back up as well as bond0.
```
systemctl reboot
```

Then

```
service networking start
ifup bond0
```


11\. Test If it worked; run `cat /proc/net/bonding/bond0` and see if you get something similar to the following:

```
Ethernet Channel Bonding Driver: v3.7.1 (April 27, 2011)

Bonding Mode: fault-tolerance (active-backup)
Primary Slave: eno1 (primary_reselect always)
Currently Active Slave: eno1
MII Status: up
MII Polling Interval (ms): 100
Up Delay (ms): 0
Down Delay (ms): 0

Slave Interface: eno1
MII Status: up
Speed: 1000 Mbps
Duplex: full
Link Failure Count: 0
Permanent HW addr: 88:51:fb:40:26:99
Slave queue ID: 0
```

## Bonding (Ubuntu 17.10 and After)

!> If you are on Ubuntu 16.04 or earlier, [use these instructions](operating_systems/ubuntu/server_build?id=bonding-ubuntu-1604-and-before) instead!

!> Unfortunately, I could not get bonding to work in 18.04, so this is a stub for now.

---

# Bridging
<span style='width: 20px; display:inline-block'>:bangbang:</span>

In some cases, packages (usually virtualization software like [KVM](operating_systems/ubuntu/package_install/kvm_install) or VirtualBox) need to get their own IP from the router; `bridging` allows packages to get their own IP from the router, and it allows them to interact with the router almost as if they are their own entity / server.

> Under most circumstances, WiFi connections **will not work with bridging** as their drivers do not support it (see the note under 'Bridged Networking' [here](https://help.ubuntu.com/community/KVM/Networking)). Keep this in mind if you are trying to bridge a WiFi connection!

## Bridging (Ubuntu 16.04 and Before)

!> If you are on Ubuntu 17.10 or later, [use these instructions](operating_systems/ubuntu/server_build?id=bridging-ubuntu-1710-and-after) instead!


Steps:

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root).

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages).

3\. Install supporing packages
```
apt-get install qemu
apt-get install libcap2-bin
```

4\. Back-up original interfaces file: `cp /etc/network/interfaces /etc/network/interfaces.bak`

5\. Edit the interface file.
```
vi /etc/network/interfaces
```

The file should look like the following:
```
# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto INTERFACE_GOES_HERE
iface INTERFACE_GOES_HERE inet manual
bond-master bond0
bond-primary INTERFACE_GOES_HERE

auto bond0
        iface bond0 inet manual
        bond-mode active-backup
        bond-miimon 100
        bond-slaves none

auto br0
iface br0 inet static
        address STATIC_IP_GOES_HERE
        netmask 255.255.255.0
        network YOUR_INET_ADDRESS_WITH_THE_LAST_NUMBER_REPLACED_BY_ZERO_HERE
        gateway YOUR_INET_ADDRESS_WITH_THE_LAST_NUMBER_REPLACED_BY_ONE_HERE
        dns-nameservers 8.8.8.8
        bridge_ports bond0
        bridge_stp off
        bridge_fd 0
        bridge_maxwait 0
```
* The INTERFACE\_GOES\_HERE is the interface you found [using this method](operating_systems/ubuntu/server_build?id=setting-up-the-static-ip).
* The STATIC\_IP\_GOES\_HERE is the static IP you found [using this method](operating_systems/ubuntu/server_build?id=setting-up-the-static-ip)
* The netmask is probably 255.255.255.0, but change it if its not; [this is how you find it](operating_systems/ubuntu/server_build?id=setting-up-the-static-ip)
* The gateway is the IP to get into your router; its usually 10.0.0.1 or 192.168.1.1 for home routers.
* The dns-nameservers is a listing of servers that can be used for DNS work; this can be any server built for this, including external public servers (8.8.8.8 is a DNS public server for Google).

6\. Restart Server; for Ubuntu 14.04 and below the command to do this is `shutdown -g0 -r`; for Ubuntu 16.04 use `shutdown -r 0`

## Bridging (Ubuntu 17.10 and After)

> [These instructions from webby.land](https://webby.land/2018/04/27/bridging-under-ubuntu-18-04/) can be used as a base for making a bridge in Ubuntu 18.04 and beyond.

!> These steps changed in Ubuntu 17.10 when the move to NetworkManager / netplan was enforced and the file `/etc/network/interfaces` was depricated; If you have a version of Ubuntu below 17.10, use [these instructions](operating_systems/ubuntu/server_build?id=bridging-ubuntu-1604-and-before) instead!

!> These instructions do NOT use bonding, as I cannt get bonding to work yet in 18.04.


Steps:

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. Take note of the IPs you will need (as detailed [here](operating_systems/ubuntu/server_build?id=setting-up-the-static-ip).)

3\. Go into the directory `/etc/netplan`, find the file that ends in .yaml (for me it was `01-network-manager-all.yaml`, but it could be different for you), and back it up:
```
cd /etc/netplan
ls -la *.yaml
cp 01-network-manager-all.yaml 01-network-manager-all.yaml.bak
```

4\. Make sure the file looks like this:
```
# Let networkd manage all devices on this system
network:
  version: 2
  renderer: networkd
  ethernets:
  #declare the interface used by the bridge device
    NETWORK_INTERFACE_HERE:
     dhcp4: no
     dhcp6: no
  bridges:
    br0:
     interfaces: [NETWORK_INTERFACE_HERE]
     dhcp4: no
     addresses: [YOUR_INET_ADDRESS_HERE/24]
     gateway4: YOUR_INET_ADDRESS_WITH_THE_LAST_NUMBER_REPLACED_BY_ONE_HERE
     nameservers:
       addresses: [8.8.8.8,8.8.4.4]
```
* Note **indents (the leading spaces and / or tabs) are are critical in .yaml files - so make sure they are exact.**
* If you have previously set up a static IP, this will replace that file.
* Replace the word NETWORK\_INTERFACE\_HERE with the interface you found [here](operating_systems/ubuntu/server_build?id=setting-up-the-static-ip).
* 'dhcp' basically means 'ask for a random IP', but since we are getting a static IP we set this to 'no' for 4 and 6.
* Replace the word YOUR\_INET\_ADDRESS\_HERE with the inet address you found [here](operating_systems/ubuntu/server_build?id=setting-up-the-static-ip), which should be your static IP.
 * and, yes, leave that '/24' exactly as you see it.
* The gateway is the IP to get into your router; its usually 10.0.0.1 or 192.168.1.1 for home routers.
* The addresses for the nameservers are a bit tricky; 8.8.8.8 and 8.8.4.4 are public Google DNS servers and will work for most applications.
* If you need more examples for configuring this .yaml, [see this page](https://cloudinit.readthedocs.io/en/latest/topics/network-config-format-v2.html#examples).

5\. Save and exit

6\. Re-boot the server
```
systemctl reboot
```

7\. Test with a `ping` command; I use `ping android.com`. If the ping can get through, the static IP worked!

```
PING android.com (172.217.11.36) 56(84) bytes of data.
64 bytes from lga25s61-in-f4.1e100.net (172.217.11.36): icmp_seq=1 ttl=53 time=18.0 ms
64 bytes from lga25s61-in-f4.1e100.net (172.217.11.36): icmp_seq=2 ttl=53 time=15.6 ms
64 bytes from lga25s61-in-f4.1e100.net (172.217.11.36): icmp_seq=3 ttl=53 time=15.8 ms
64 bytes from lga25s61-in-f4.1e100.net (172.217.11.36): icmp_seq=4 ttl=53 time=13.9 ms
64 bytes from lga25s61-in-f4.1e100.net (172.217.11.36): icmp_seq=5 ttl=53 time=16.5 ms
^C
--- android.com ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4006ms
rtt min/avg/max/mdev = 13.992/16.011/18.039/1.315 ms
```

---

# Installing SSH
<span style='width: 20px; display:inline-block'>:heart:</span>

> If you would like further reading on SSH, [please go here](/operating_systems/ubuntu/linux_notes?id=ssh).

SSH is critical for interacting with the host remotely (using [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/) or [WinSCP](https://winscp.net/eng/index.php), for example). Make sure the SSH package is installed:

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)

3\. Install SSH

```
apt-get install ssh
```

# Installing WiFi Capabilities
<span style='width: 20px; display:inline-block'>:bangbang:</span>

The command to interact with the WiFi in Ubuntu is `nmcli`, but you actually need to install network manager first:

```
apt-get install network-manager
```

After it is installed, you may have to run network manager once before you can start using nmcli:

```
NetworkManager
```

For more info on how to set up and interact with nmcli, [please go here](/operating_systems/ubuntu/linux_notes?id=interacting-with-wifi).

# Setting up the Proxy in wgetrc

The http proxy must also be entered in a file called 'wgetrc' in /etc. If we do not do this, the wget command that is used by many programs (apt-get, CPAN, etc) will not work if we are behind a firewall.

## wgetrc Setup

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. Open the wgetrc file in vi: 

```
vi /etc/wgetrc
```

3\. Find the line that is commented out that defines the http proxy and ftp proxy - it should be commented out (a hash # as the first character in the line); delete both entire lines, and then paste your proxies in using this format:

> http_proxy = http://YOUR_PROXY_HERE:PORT <br>
> ftp_proxy = http://YOUR_PROXY_HERE:PORT

4\. Press the escape button

5\. Type the following (which quits and saves): `:wq!`


> If you wish, you can edit the .profile file in your directory (the file `/home/YOUR_USERNAME_HERE/.profile`) and place the http_proxy there as well.

!> Occasionally you may have to empty this proxy.  To do this, simply set it to null by running: `export http_proxy=`

---

# Setting up the Proxy in apt.conf

The http proxy must also be entered in a file called apt.conf; if you do not do this, the 'apt-get install' command may not work properly.

## apt.conf Setup
1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. Change directory

```
cd /etc/apt
```

3\. See if the file exists: `ls -la apt.conf` ; if - and only if - it does not exist run this command to create it: `touch /etc/apt/apt.conf`

4\. Open the file in vi: 

```
vi /etc/apt/apt.conf
```

5\. Add these lines on the last lines of the file:

```
Acquire::http::Proxy "http://YOUR_PROXY_HERE:PORT";
```

6\. Press the escape button

7\. Type the following (which quits and saves): `:wq!`

!> If you ever have to run updates and you have this set, you may need to unset this to run the updates.

---

# Install OpenConnect

OpenConnect is similar to Cisco's AnyConnect - it's a VPN. Here are the steps to set it up:

Steps:

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root).

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages).

3\. Activate the TUN module: `/sbin/modprobe tun`

4\. Install OpenConnect: `apt-get install openconnect`  

5\. Install a GUI for OpenConnect: `apt-get install network-manager-openconnect-gnome`  
* While not required, this is necessary if you do not want to run OpenConnect over the command-line interface.  
* To run on a command line (which will pop-up the GUI): `nm-connection-editor`  

## Setting Up OpenConnect GUI  

!> Unfortunately, the VPN setup on Ubuntu 18.04 is currently pretty broken (as of February 2022, and has been since 2019). In theory, after installing the [necessary packages](operating_systems/ubuntu/server_build?id=install-openconnect), you should be able to simply open Settings / Network / and then add a VPN, but multiple things are broken with this; for now, you will just have to run `nm-connection-editor` on the command-line interface to get a GUI to do this. 

1\. Install the [necessary packages](operating_systems/ubuntu/server_build?id=install-openconnect).  
2\. In a terminal, run: `nm-connection-editor`  
* This will open a <font color="purple">Network Connections</font> GUI.  

3\. Click on the <font color="purple">VPN</font> header, then click the <font color="green">+</font> button to add a VPN. A new box <font color="purple">Choose a Connection Type</font> pops up.  
4\. Select the appropriate selection under the VPN header and click <font color="green">Create</font>.   
* I will use <font color="purple">Cisco AnyConnect Compatible VPN (openconnect)</font>.  

![choose-vpn-connection-type.jpg](images/choose-vpn-connection-type.jpg)  

5\. At the very least, fill in <font color="purple">Connection Name</font> and <font color="purple">Gateway</font> on the <font color="orange">VPN</font> tab:  
![vpn-vpn-settings.jpg](images/vpn-vpn-settings.jpg)  
* You will have to get the gateway off of your network team.  
* Set the other settings based off your needs.  

6\. Now go to the <font color="orange">IPv4 Settings</font> tab, and fill out pertinent information there:  
![vpn-ipv4-settings.jpg](images/vpn-ipv4-settings.jpg) 
* The <font color="purple">Additional search domains</font> field will probably have to be filled out, as this entry gives an address to use when resolving host names.  

7\. Click <font color="green">Save</font>.  

8\. Restart your laptop / desktop. 
* Usually, the VPN will not work if you do not do this. You are welcome to try to use it without a reset, if you wish.  

9\. _If this VPN should only handle traffic for its specific network_:  
* **Note** if you do not restart above, your entry may not show up.  
* Open the Ubuntu Settings GUI and go to the <font color="orange">Network</font> tab.  
* Find the connection and click the gear icon next to it.  
* Go to the <font color="orange">IPv4</font> tab.  
* Check the <font color="purple">Use this connection only for resources on its network</font> box.  

10\. _If other users on this desktop should be able to use this connection_:  
* **Note** if you do not restart above, your entry may not show up.  
* Open the Ubuntu Settings GUI and go to the <font color="orange">Network</font> tab.  
* Find the connection and click the gear icon next to it.  
* Go to the <font color="orange">Details</font> tab.  
* Check the <font color="purple">Make available to other users</font> box.  

It should be noted that doing the above will save the settings to a file in `/etc/NetworkManager/system-connections` - whatever you named the <font color="purple">Connection Name</font> in step #5 above will be the name of the file (no extension); for me, that was <font color="purple">SomeConnection</font>. That file will look something like this:  
```
[connection]
id=SomeConnection
uuid=ABCDEFG-XYZ
type=vpn
autoconnect=false
permissions=user:ubuntu_user_name_here:;

[vpn]
authtype=password
autoconnect-flags=0
certsigs-flags=0
cookie-flags=2
enable_csd_trojan=no
gateway=secure.gateway.yourcompany.com
gateway-flags=2
gwcert-flags=2
lasthost-flags=0
pem_passphrase_fsid=no
protocol=anyconnect
stoken_source=disabled
xmlconfig-flags=0
service-type=org.freedesktop.NetworkManager.openconnect

[vpn-secrets]

[ipv4]
dns-search=yourcompany.com;
method=auto

[ipv6]
addr-gen-mode=stable-privacy
dns-search=
ip6-privacy=0
method=auto
```  
## Using the VPN GUI  

There are two ways to activate the VPN via a GUI.  
* From the Ubuntu Settings GUI  
  * Open the Ubuntu Settings GUI and go to the <font color="orange">Network</font> tab.  
  * Click the button next to the VPN name to turn it on.  
* From the top dropdown  
  * Click the top dropdown.  
  * Click 'VPN'.  
  * Turn on the VPN of your choice.  



# Updating and Upgrading Packages
<span style='width: 20px; display:inline-block'>:heart:</span>

Typically, its important to update all packages before installing any new package (note: this does not actually change them, it simply updates info on them).  This is done by [becoming root](/operating_systems/ubuntu/linux_notes?id=becoming-root) and then running the following:
```
apt-get update
```

Occasionally, you should update all packages which actually modifies all packages. To do this, [become root](/operating_systems/ubuntu/linux_notes?id=becoming-root) and then running the following:
```
apt-get upgrade
```

If you wish to read more on this, [please do so here](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages).

---

# Install Random Packages
<span style='width: 20px; display:inline-block'>:heart:</span>

These are some packages that we will need on the server for various things:
* ssh is for ssh
* libaio1 is for the Oracle instantclient
* unzip is for unzipping various packages
* mysql-client is for the mysql-client, which will be used on most servers (even if the MySQL server itself is not used; the client is for connecting to both the local and remote databases)
* libmysqlclient-dev further supports mysql-client
* net-tools is for important networking tools (like ifconfig).
* unrar is a tool that helps unzip .rar files. The system can do this natively, but the default package can sometimes fail giving you a 'Parsing filters not supported' message; installing unrar fixes this.
* iptraf-ng installs iptraf, which is a great tool to quickly monitor IP packets coming into / going out of your host. 
* sg3-utils has a bunch of tools to help with secure file cleanup (smem, sswap, sfill, etc)   
* htop is a powerful resource management tool  

Run:
```
apt-get install ssh
apt-get install libaio1
apt-get install unzip
apt-get install mysql-client
apt-get install libmysqlclient-dev
apt-get install net-tools
apt-get install unrar
apt-get install iptraf-ng
apt-get install sg3-utils
apt-get install htop
```

---


# User Accounts and Settings

> If you need to add users, [here is how you add a user](/operating_systems/ubuntu/linux_notes?id=adding-a-user), [set a password](/operating_systems/ubuntu/linux_notes?id=setting-a-password), [changing a user's shell](/operating_systems/ubuntu/linux_notes?id=changing-a-user39s-default-shell), or [setting a user as admin](/operating_systems/ubuntu/linux_notes?id=setting-a-user-as-admin).

**<font size="4">A Generic Account** <br>

The generic account uses the account login 'uperson'. To get the uperson account set up:

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. Add the uperson user:

```
useradd -d /home/uperson -m -c "User Person's Name Here" uperson
usermod -s /bin/bash uperson
passwd uperson
```

3\. Consider [setting up a SSH key](operating_systems/ubuntu/linux_notes?id=ssh) for uperson.

4\. Consider [setting up uperson's profile](operating_systems/ubuntu/linux_notes?id=the-profile).


---

# Oracle Instant Client
<span style='width: 20px; display:inline-block'>:snake:</span> <span style='width: 20px; display:inline-block'>:open_file_folder:</span> <span style='width: 20px; display:inline-block'>:satellite:</span>

The Oracle instantclient is needed to connect to the back-end of Oracle databases.

## Oracle Instant Client Setup

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. Navigate to /usr/local/lib and then get the instantclient packages using sftp.

3\. Confirm all packages exist by performing `ls -ltr`  (should see 5 packages)

4\. Unzip each package - they will automatically unpack into an instantclient_XX_X directory (currently this will be a directory called instantclient_12_1)

```
unzip instantclient-basic.zip
unzip instantclient-jdbc.zip
unzip instantclient-odbc.zip
unzip instantclient-sdk.zip
unzip instantclient-sqlplus.zip
```

5\. Confirm instantclient_12_1 directory exists by performing `ls -ltr`

6\. Remove instantclient packages

```
rm instantclient*zip
```

> this will only remove the instantclient zip files!

7\.  Now we must install the current tnsnames.ora file; navigate to /usr/local/lib/instantclient_12_1 and load your tnsnames.ora file here.

> Your organization will probably have a pre-built TNSNAMES.ORA file.

8\. Check if libaio1 is installed: `dpkg -s libaio1 | grep Status`
 * If installed you will see:
> Status: install ok installed
 * IF NOT installed you will see:
> Package `libaio1' is not installed and no info is available.
 * If libaio1 not installed run the following command (else skip):

```
apt-get install libaio1
```

9\. Create symlink for libclntsh.so:

```
cd /usr/local/lib/instantclient_12_1
ln -s /usr/local/lib/instantclient_12_1/libclntsh.so.12.1 libclntsh.so
```

10\. Now link the instantclient_12_1 package to /usr/local
```
cd /usr/local
ln -s /usr/local/lib/instantclient_12_1 instantclient
```

---

# Install Perl Packages
<span style='width: 20px; display:inline-block'>:snake:</span>

These are the various Perl packages I like.

Run:
```
apt-get install build-essential
apt-get install libdbd-mysql-perl
apt-get install libdbi-perl
apt-get install libproc-background-perl
apt-get install libdate-calc-perl
apt-get install libxml-parser-perl
```

---

# Build-essential Package
<span style='width: 20px; display:inline-block'>:snake:</span>

The build-essential package is necessary to compile perl packages

To check, run:
```
dpkg -s build-essential | grep Status
```

If it is not installed, run
```
apt-get update
apt-get install build-essential
```

---

# Perl DBD::Oracle Setup
<span style='width: 20px; display:inline-block'>:snake:</span> <span style='width: 20px; display:inline-block'>:open_file_folder:</span>

DBD::Oracle is needed for Perl.

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. Make a temp directory and switch to that directory

```
mkdir -p /var/tmp/modules
cd /var/tmp/modules
```

3\. Set https proxy (if you need to / if you are behind a firewall):

```
export https_proxy=http://YOUR_PROXY_HERE:PORT
```

4\. Get the DBD-Oracle package using wget:

```
wget https://cpan.metacpan.org/authors/id/P/PY/PYTHIAN/DBD-Oracle-1.74.tar.gz
```

!> Always check manually to see if this is newest package

5\. Uncompress package and then change directory into the newly created directory
```

tar xvfz DBD-Oracle-1.74.tar.gz
cd DBD-Oracle-1.74
```

6\. Export ORACLE_HOME, PATH, LD_LIBRARY_PATH, and TNS_ADMIN variables

```
export ORACLE_HOME=/usr/local/instantclient
export PATH=$PATH:$ORACLE_HOME
export LD_LIBRARY_PATH=$ORACLE_HOME
export TNS_ADMIN=$ORACLE_HOME
```

7\. Compile the code and watch for any strange errors:

```
perl Makefile.PL
make
make test
make install
```

8\. Validate a Perl script can connect to Oracle DB via a Perl script.

---

# Python: Anaconda Install
<span style='width: 20px; display:inline-block'>:snake:</span>

!> WARNING - This will be an installation that will work alongside the already installed version of Python - so there will be concurrent versions installed on the server, one in `/usr/bin/python` and the one we are installing now, which is `/usr/bin/anaconda/python2.7/bin/python`. If you have downloaded any other additional Python packages - or installed any Python packages - to the native version of Python you will have to re-install them again after you follow these instructions.

Anaconda is a version of Python that enables Python to use powerful data analysis tools (SciPy, Pandas, Numpy, Matplotlib to name a few). We are going to install the version that supports Python 2.7.

While its possible to add all of these tools to Python on a one-by-one basis, using Anaconda makes it far easier to install these add-ons.

## Downloading Anaconda

To download Anaconda, [go here](https://www.anaconda.com/download/) and select the linux 64 bit (x86) installer (it should end in .sh). If you do not have access to our code repository, this is how you can get the download file.

## Installing Anaconda
<span style='width: 20px; display:inline-block'>:snake:</span>

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)

3\. Save the downloaded Anaconda file to your home directory.

4\. Run the file:

```
bash Anaconda.sh
```

5\. You will be prompted to select where to install (**note: the directory CANNOT exist!**).  Input location / install to: `/usr/bin/anaconda/python3`  
 * `/usr/bin/anaconda/python3` is just my preference - if its not yours, don't use it.  
 * Make _sure_ to be careful when scrolling through the terms and conditions - if you press enter once it asks for `yes/no`, even once, you must do it all again.  

6\. Initialize conda by running `/usr/bin/anaconda/python3/bin/conda init` (note, this is dependent on the path you selected for Anaconda previously).
 * This should set your path to the version of Python installed with Anaconda - you can check this out by sourcing your `.bashrc` file by running `source ~/.bashrc` and then running `witch python` to see if the path to Python is the Anaconda version.  
 * If `conda init` did not work, you can add this to the end of your `~/.bashrc` file:  
 ```  
 # >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/usr/bin/anaconda/python3/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/usr/bin/anaconda/python3/etc/profile.d/conda.sh" ]; then
        . "/usr/bin/anaconda/python3/etc/profile.d/conda.sh"
    else
        export PATH="/usr/bin/anaconda/python3/bin:$PATH"
    fi
fi
unset __conda_setup
# <<< conda initialize <<<
```  
 * This assumes you used my path for Anaconda - if you did not, substitute what you used.  

 * _If_ prompted to append the Anaconda3 install location to PATH, do it  
 * _If_ prompted to install Microsoft VSCode, say NO  
 * _If_ prompted to  initialize Anaconda3 by running conda init, say YES  

6\. Source in your path (usually in the .bashrc file): `source ~/.bashrc`

7\. Install pyside6 (for matplotlib):

```
pip install pyside6
```

8\. Remove the install file:

```
rm /home/YOUR_LOGIN_HERE/Anaconda.sh
```

## Changing the PATH for Python
<span style='width: 20px; display:inline-block'>:snake:</span>

In order for this new version of Python to be the default on the server, we must edit every user's PATH variable to explicitly point to the path we just installed. Everyone on the server that will use Anaconda will have to do this.

Setup:

1\. Edit the .bashrc file in your home directory: 

```
vi ~/.bashrc
```

2\. Add these lines at the bottom:

```  
 # >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/usr/bin/anaconda/python3/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/usr/bin/anaconda/python3/etc/profile.d/conda.sh" ]; then
        . "/usr/bin/anaconda/python3/etc/profile.d/conda.sh"
    else
        export PATH="/usr/bin/anaconda/python3/bin:$PATH"
    fi
fi
unset __conda_setup
# <<< conda initialize <<<
```  

Or, if you prefer to keep it simple:  
```  
PATH=/usr/bin/anaconda/python3/bin:$PATH <br>
export PATH
```  

3\. Quit and save: `wq!`

4\. Source in the .bashrc file: 

```
source ~/.bashrc
```

## Adding to Python PATH
<span style='width: 20px; display:inline-block'>:snake:</span>

There is a second PATH variable that we can alter - PYTHONPATH. This path is different from the previous one: whereas [the one above](operating_systems/ubuntu/server_build?id=changing-the-path-for-python) pointed the server to the new version of Python, this PYTHONPATH allows us to build Python functions in the designated folder, then we can import them into other Python scripts with a simple `import` at the top of the file.

As an example, We are going to add /home/YOUR_LOGIN_HERE/GeneralPythonFunctions to the PYTHONPATH, so any Python function we write in this folder can be quickly imported into other Python scripts with the `import` function in Python.

Setup:

1\. Edit the .bashrc file in your home directory: 

```
vi ~/.bashrc
```

2\. Add these two lines at the bottom:
> PYTHONPATH=/home/YOUR_LOGIN_HERE/GeneralPythonFunctions:$PYTHONPATH <br>
> export PYTHONPATH

3\. Press escape, then type this to save and quit: `wq!`

4\. Source in the .bashrc file: 

```
source ~/.bashrc
```

!> You will have to do this for all users that will use this functionality in Python.

## Uninstalling Anaconda
<span style='width: 20px; display:inline-block'>:snake:</span> <span style='width: 20px; display:inline-block'>:bangbang:</span>

!> If you remove Anaconda after installing Python packages, you will have to re-install those packages.

If you must uninstall Anaconda, simply remove the folder 
```
rm -r /usr/bin/anaconda
```

Find the PATH lines we entered into our .bashrc file for each user that entered it:

1\. Edit the .bashrc file in your home directory: 

```
vi ~/.bashrc
```

2\. Seek out and delete these 2 lines:
> PATH=/usr/bin/anaconda/python2.7/bin:$PATH <br>
> export PATH

3\. Seek out and delete these 2 lines (if they exist):
> PYTHONPATH=/home/YOUR_LOGIN_HERE/GeneralPythonFunctions:$PYTHONPATH <br>
> export PYTHONPATH

4\. Press escape, then type this to save and quit: `wq!`
5\. Source in the .bashrc file: 

```
source ~/.bashrc
```

6\. Change the .bashrc file for EVERY user that made these changes.

---

# Python Supporting Packages
<span style='width: 20px; display:inline-block'>:snake:</span>

These packages are meant to support Python.

## Install Python PIP

Python PIP is the official method to install Python packages; when possible, use PIP to install anything related to Python.

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)

3\. Install python-pip:

```
apt-get install python3-pip
```

## Install MySQL for Python
<span style='width: 20px; display:inline-block'>:snake:</span> <span style='width: 20px; display:inline-block'>:open_file_folder:</span>

> The base of these install instructions were originally [found here](https://dev.mysql.com/doc/connector-python/en/connector-python-introduction.html).

If we wish to use the MySQL connector in Python (specifically the package `mysql.connector`), we must install the MySQL connector module.

We can download it [from here](http://dev.mysql.com/downloads/connector/python/) - when prompted to select the operating system select **Platform Independent**, and make sure to get the ZIP file; I initially downloaded the .deb file but it didn't play well - either with Anaconda or in general. The tar file had some problems too, so just use the platform independent / zip file.

Setup:

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. Create a temp directory where we can unzip this file (we use `/home/YOUR_LOGIN_HERE/tmp/mysql-connector`) and then change to it:

```
mkdir -p /home/YOUR_LOGIN_HERE/tmp/mysql-connector
chown YOUR_LOGIN_HERE:YOUR_LOGIN_HERE /home/YOUR_LOGIN_HERE/tmp/mysql-connector
cd /home/YOUR_LOGIN_HERE/tmp/mysql-connector
```

3\. Download the MySQL connector install file and save it to `/home/YOUR_LOGIN_HERE/tmp/mysql-connector`.

4\. Unzip the install package to our temp directory (you will simply delete this directory later; the name `mysql-connector-python.zip` is assumed but it can be different):

```
unzip mysql-connector-python.zip
```

5\. Change to the directory we just created and run the install script:

```
cd mysql-connector-python-2.1.5
python setup.py install
```

!> The directory above may be different - you may have to check it and see the actual name of the directory.

6\. Remove the setup folder:

```
cd /home/YOUR_LOGIN_HERE/tmp
rm -r /home/YOUR_LOGIN_HERE/tmp/mysql-connector
```

## Install HP Vertica for Python
<span style='width: 20px; display:inline-block'>:snake:</span> <span style='width: 20px; display:inline-block'>:open_file_folder:</span>

> The base of these install instructions were originally [found here](https://my.vertica.com/docs/7.2.x/HTML/Content/python_client/install_uninstall.html) and [more supporting documentation is here](https://my.vertica.com/docs/7.2.x/HTML/Content/python_client/index.html).

If we wish to use the HP Vertica connector in Python, we must install the HP Vertica connector module.

We can download it [from here](https://my.vertica.com/search/downloads/python/). You will be dumped to another page; for me, I picked 'Client Drivers 8.0.x' (as that is what works for our PRIDE HP Vertica database, but that may change), and then I found the header 'Vertica 8.0.x', then 'Linux'; I picked the tar file (the driver 'ODBC, JDBC, Python, and vsql'); my filename was vertica-client-8.0.1-0.x86_64.tar.gz. This may change in the future - it may be a different version of Vertica - but you should stick with 'Linux' and tar.gz files.


Setup:

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root).

2\. [Install Python PIP](/operating_systems/ubuntu/server_build?id=install-python-pip), if you have not done so already.

3\. Create a temp directory where we can untar this file (we use `/home/YOUR_LOGIN_HERE/tmp/vertica`) and then change to it:

```
mkdir -p /home/YOUR_LOGIN_HERE/tmp/vertica
chown YOUR_LOGIN_HERE:YOUR_LOGIN_HERE /home/YOUR_LOGIN_HERE/tmp/vertica
cd /home/YOUR_LOGIN_HERE/tmp/vertica
```

4\. Get the remote the HP Vertica client install file and save to `/home/YOUR_LOGIN_HERE/tmp/vertica`:

5\. Untar the file (assuming the file name is `vertica-client.tar.gz`):

```
tar xvzf vertica-client.tar.gz
```

> There will be a bunch of 'wheel' files in the directory at this point.

6\. Run pip to install:

```
pip install --no-index -f /home/YOUR_LOGIN_HERE/tmp/vertica/opt/vertica/Python hp-vertica-client 
```

7\. Remove the setup folder:

```
cd /home/YOUR_LOGIN_HERE/tmp
rm -r /home/YOUR_LOGIN_HERE/tmp/vertica
```

## Install Oracle for Python
<span style='width: 20px; display:inline-block'>:snake:</span> <span style='width: 20px; display:inline-block'>:open_file_folder:</span>

> The base of these install instructions were originally found in 3 places: [here](http://stackoverflow.com/questions/7300321/how-to-use-pythons-pip-to-download-and-keep-the-zipped-files-for-a-package) and [here](https://oracle.github.io/python-cx_Oracle/ ) and [here](http://stackoverflow.com/questions/15031694/installing-python-packages-from-local-file-system-folder-with-pip).

If we wish to use the Oracle client in Python, we must install the Oracle client module.

Setup:

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root).

2\. [Install Python PIP](/operating_systems/ubuntu/server_build?id=install-python-pip), if you have not done so already.

3\. [Install the Oracle instantclient](/operating_systems/ubuntu/server_build?id=oracle-instant-client)

4\. Set the variable ORACLE_HOME (this does not seem to matter once cx_Oracle is installed, but it MUST be set during the install).  ORACLE_HOME MUST point to the folder that contains the Oracle instant client; to do this (assuming you installed the Oracle instant client to the '/usr/local/instantclient' directory):

```
export ORACLE_HOME=/usr/local/instantclient
```

5\. Find a version of 'cx_Oracle'; This CAN be done fully on the server using pip:

!> Your company's firewall may block portions of the install; I will show what to do if this happens.

```
python -m pip install cx_Oracle
```

 * Note the above will install the current 'live' version; if you want the latest (and potentially buggy) package, run:

 ```
python -m pip install cx_Oracle --pre
 ```

6\. If step 5 times out, you can download the cx_Oracle tar.gz file and then manually load it.  Note that I could only find this on sourceforge on the internet and I did not trust it; you can do this, but do it at your own risk.

 * You can do this to avoid sourceforge: I ran the pip command on an Ubuntu server that was not behind a firewall, and included a flag to download the tar.gz file and not delete it (I chose the folder '/home/YOUR_LOGIN_HERE/tmp/cx_Oracle').  Note that I forget which exact line I used so I will list both, but if it downloads you are good (try one, and if it fails try the other):

```
python -m pip install --download="/home/YOUR_LOGIN_HERE/tmp/cx_Oracle" cx_Oracle
#-OR-
pip install --download="/home/YOUR_LOGIN_HERE/tmp/cx_Oracle" cx_Oracle
```

 * From here, I simply copied the file to the other servers and then ran the pip on the local file downloaded above (note: I saved the file to /home/YOUR_LOGIN_HERE/tmp/cx_Oracle and the file downloaded was called cx_Oracle-5.3.tar.gz):

```
pip install /home/YOUR_LOGIN_HERE/tmp/cx_Oracle/cx_Oracle.tar.gz
```

7\. As long as you set ORACLE_HOME properly, you should be done - but test it with a script.

8\. Remove the setup folder

```
cd /home/YOUR_LOGIN_HERE/tmp
rm -r /home/YOUR_LOGIN_HERE/tmp/cx_Oracle
```

### Set-up TNS_ADMIN for Python
<span style='width: 20px; display:inline-block'>:snake:</span> <span style='width: 20px; display:inline-block'>:open_file_folder:</span>

For every user that will use the Oracle client in Python, some other variables must be set in the environment file (.env file) - namely LD_LIBRARY_PATH and TNS_ADMIN. Make an environment file in your home directory that starts with a dot (so, for example, .myenvironment.env) and copy the following 4 lines into this file:
> \#\# SETUP TNS_ADMIN so tnsnames.ora is sourced in <br>
> export TNS_ADMIN=/usr/local/instantclient <br>
> export LD_LIBRARY_PATH=/usr/local/instantclient <br>
> PATH=$PATH:/usr/local/instantclient

Save the file and close. Now go into the .profile file in your home directory and add this at the end, save, and close:
```
. "$HOME/.myenvironment.env"
```

## OpenCV for Python
<span style='width: 20px; display:inline-block'>:rotating_light:</span> <span style='width: 20px; display:inline-block'>:snake:</span> <span style='width: 20px; display:inline-block'>:bangbang:</span>

OpenCV is image processing software for Python - it can do things like facial recognition.

**<font size="4">Manual OpenCV Install Attempt</font>** <br>
The base of these install instructions were originally found in 2 places: [here](https://docs.opencv.org/2.4/doc/tutorials/introduction/linux_install/linux_install.html) and [here](http://milq.github.io/install-opencv-ubuntu-debian/). The first link is the official way; I tried this and it simply did not install (although it said it did). The second way suggests running apt-get install python-opencv, but the problem is it installs to the default Python location (not where Anaconda is); in addition this seems to be an older release. These attempts ended in failure.

**<font size="4">Working OpenCV Install</font>** <br>

This method was found [on YouTube](https://www.youtube.com/watch?time_continue=66&v=zWPtHhGc3GI):

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root).

2\. [Install Python PIP](/operating_systems/ubuntu/server_build?id=install-python-pip), if you have not done so already.

3\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)

4\. Install libopencv-dev:

```
apt-get install libopencv-dev
```

5\. Simply use pip to install openCV:

```
python -m pip install opencv-python
```

6\. To test, open your python and type import cv2. If this works, it worked!

> Note that if you installed using pip, there are XML files called 'cascades' that are important that may not be installed with pip. They ARE in the manual download method though, so you may want to download that anyway if only to extract these XML files for later use.

Further Tutorials:
* https://realpython.com/face-recognition-with-python/
* http://gregblogs.com/computer-vision-cropping-faces-from-images-using-opencv2/

---

# Remote Desktop Packages
<span style='width: 20px; display:inline-block'>:bangbang:</span>

> Before adding these, make sure there is actually a need for remote desktop management. There are really only few times it makes sense (to manage KVM, for example).

Its possible to connect to a GUI Desktop version of this server; to do so, we are going to install a few things that will allow us to remotely launch a desktop that will run on this machine. 

First, we must [be root](/operating_systems/ubuntu/linux_notes?id=becoming-root) and [update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages); then install these packages on the server:
```
apt-get install xfce4 xfce4-goodies
apt-get install xrdp xorgxrdp
```
* `xrdp` allows remote desktop connection (this is needed to connect to xfce4 remotely); `xorgxrdp` is needed for this as well.
* `xfce4` is a light Desktop application users can use to interact with the KVM GUI remotely (which also uses `xfce4-goodies`).

> If you this is an Ubuntu Desktop and you want to connect to remote desktops, you will need to install `xrdp` and `xorgxrdp` at a minimum to do so (and possibly `xrdp-pulseaudio-installer`, but I am not certain about this).

Make sure xrdp is running on all servers that will connect to a remote host or be a remote host itself; to do so, run:
```
service xrdp status
```

If it is installed but inactive / not running, start it with:
```
service xrdp start
```


---

# Apache
<span style='width: 20px; display:inline-block'>:satellite:</span>

A common web server is Apache. Apache is a free web server available on Windows and Linux.

## Install Apache
<span style='width: 20px; display:inline-block'>:satellite:</span>

First, check if Apache is running with: `ps -ef | grep apache`

If Apache is not running its probably not installed - install it:
```
apt install apache2-bin
apt install apache2
```

Apache should start automatically, but if it does not, run this to start it:
```
apache2 -k start
```

## The Apache Document Root

Apache has a 'root' directory on Ubuntu (which acts as the 'base' web site root). For versions of Ubuntu under 14.04, this was /var/www, and for Ubuntu 14.04+ the default base is /var/www/html. This web document root directory can be changed, but that is beyond the scope of this documentation.

For example, say you had the file hello.html in /var/www/html; if your website was www.mywebsite.com and you navigated to www.mywebsite.com/hello.html, that HTML file would load in your browser. If you created a folder 'myfolder' in /var/www/html and then made a file called 'helloworld.html' in that directory, you could navigate to www.mywebsite.com/myfolder/helloworld.html and helloworld.html would render in your browser.

Anything under this root directory can potentially be seen externally, so be careful with what you place here.

---

# PHP Oracle Setup
<span style='width: 20px; display:inline-block'>:satellite:</span> <span style='width: 20px; display:inline-block'>:open_file_folder:</span>

This will enable us to connect from PHP to an Oracle DB (using OCI8 on Ubuntu).

1\. If you have not already, [install the Oracle instantclient](/operating_systems/ubuntu/server_build?id=oracle-instant-client)

2\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

3\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)

4\. Validate the version of PHP to make sure PHP 7 is installed:

```
php --version
```

5\. Install supporting PHP packages:

```
apt-get install php7.0-dev
apt-get install libaio-dev
apt-get install php-pear
```

6\. Set the pear proxy (if you need to set a proxy):

```
pear config-set http_proxy http://YOUR_PROXY_HERE:PORT/
```

7\. Install the oci8 extension: `pecl install oci8`

 * This will ask for Oracle Home Directory - give the path of the instant client: 

```
instantclient,/usr/local/instantclient
```

> This is telling pecl that its an instantclient and then giving the path to the instantclient directory. <br>
> If you get an error saying:  No releases available for package "pecl.php.net/oci8", make **SURE** the proxy is set correctly! <br>
> If the installation is successful, you should see messages like "Build Process completed successfully"

8\. Add the oracle oci8.so to php.ini (can also be done using vi to add "extension=oci8.so" under the OCI section in each file):

```
echo "extension=oci8.so" >> /etc/php/7.0/cli/php.ini 
echo "extension=oci8.so" >> /etc/php/7.0/apache2/php.ini 
```

9\. Restart apache

```
systemctl restart apache2 
```

10\. Validate it worked. Create the file 'phpinfo.php' in /var/www/html and add following line using vi:
> <?php phpinfo(); ?>

 * Save the file and then open a web browser and navigate to the phpinfo file ( in our case it is localhost/phpinfo.php ) <br>
 * Search for oci8 and you should find oci8 block with variables ( this means you have successfully compiled PHP with the oci8 extension )

11\. Test connecting to an oracle instance using oci_connect() in PHP. To do so, create the file phpconnect.php file in /var/www/html and add the following code:

	```
	<?php
	error_reporting(E_ALL); 
	ini_set('display_errors', 1);

		$db = "(DESCRIPTION =
		    (ADDRESS = (PROTOCOL = TCP) (HOST = YOUR_ORACLE_HOST) (PORT = YOUR_PORT))
		    (CONNECT_DATA = (SERVER = DEDICATED) (SERVICE_NAME = YOUR_SERVICE_NAME))
		  )";

		$conn = oci_connect("USER", "PASSWORD", $db);
		if (!$conn) {
		   $m = oci_error();
		   echo $m['message'], "\n";
		   exit;
		}
		else {
		   print "Connected to Oracle!";
		}
		// Close the Oracle connection
		oci_close($conn);

	?>
	```
!> You will need to change the USER, PASSWORD, and database settings.
 * Save the file and exit
 * Open a web browser and navigate to the phpconnect.php file ( in our case it is localhost/phpconnect.php )
 * If it worked you will see a screen with the message:  `Connect to Oracle!`
 * If there was an issue you might see an error like `Fatal error: Uncaught Error: Call to undefined function oci_connect() in /var/www/html/phpconnect.php:10`

12\. Once testing is complete the phpinfo.php and phpconnect.php files need to be removed.

---

# MySQL Setup
<span style='width: 20px; display:inline-block'>:card_index:</span>

MySQL setup [is discussed at length here](databases/mysql/mysql_install).

If you are running a MySQL database on this server you will at least need to do the following:

1\. [Install MySQL](databases/mysql/mysql_install?id=mysql-install).

2\. [Install Other Packages](databases/mysql/mysql_install?id=other-packages-to-install).

3\. [Create users in MySQL](databases/mysql/mysql_maintenance?id=creating-mysql-users).

4\. [Set up mysqldcnf](databases/mysql/mysql_install?id=mysql-master-settings-mysqldcnf).

5\. [Add MySQL Timezone Tables](databases/mysql/mysql_install?id=add-timezone-tables).

6\. Consider [Set up disabling MySQL strict mode](databases/mysql/mysql_install?id=disable-strict-sql-mode), but only if you have legacy tables and queries you will be importing.

If you ever need to re-set the root password, [this is how you do that](databases/mysql/mysql_maintenance?id=re-setting-the-root-password-for-mysql).


## Testing MySQL Accounts
<span style='width: 20px; display:inline-block'>:card_index:</span>

Its not a bad idea to test the accounts you just created. To test the local login, run this on the box (replacing uperson with the user account in question):
```
mysql -u uperson -p
```

You will be prompted for a password. <br>
<br>
Now, go to a different server and log in remotely (you will need the IP for this, which we just use 111.111.111.111 below
```
mysql -h 111.111.111.111 -u uperson -p
```

---

# Install Java JRE
<span style='width: 20px; display:inline-block'>:bangbang:</span>

If you need the Java JRE, this is how it is installed.

Steps:

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)

3\. Check to see if java is already installed: `java -version`
 * For more detailed info you can run: `java -XshowSettings:properties -version`

4\. If java is not installed, install the default-jre

```
apt-get install default-jre
```

5\. After the install, confirm jre exists: `java -version` . You should see something like this (maybe with different version numbers):
> openjdk version "1.8.0_151" <br>
> OpenJDK Runtime Environment (build 1.8.0_151-8u151-b12-0ubuntu0.16.04.2-b12) <br>
> OpenJDK 64-Bit Server VM (build 25.151-b12, mixed mode)

---

# Installing Java (From Oracle)  

## Installing Java via Deb (From Oracle)  

Modern Java now gives out a .deb file which you can install from [the Java website](https://www.oracle.com/java/technologies/downloads/).  Generally, this will install Java in `/usr/lib/jvm/`; for example, Java 20 is installed in `/usr/lib/jvm/jdk-20`.  

You will still have to add items to either your profile _or_ `/etc/profile`; in addition, you _may_ have to run `update-alternatives` as described in [the manual install](operating_systems/ubuntu/server_build?id=installing-java-manually-from-oracle), but there is a chance the `.deb` install has already done this for you.  


## Installing Java Manually (From Oracle)  
<span style='width: 20px; display:inline-block'>:bangbang:</span>

Sometimes it is necessary to install Java directly from Oracle (instead of just installing the JRE or OpenJDK). These instructions come from [wikihow](http://www.wikihow.com/Install-Oracle-Java-JDK-on-Ubuntu-Linux).

!> This will require you to remove Java if it exists already. Removing Java can uninstall other programs (Eclipse is notorious for this) - so be careful (or install Java first). <br> Its also possible to skip the Java deletion and just switch java versions when using different programs. To do so, open a terminal and run `update-alternatives --config java` . You will now be presented with a list of java versions installed (will be listed as option 0, 1, 2, 3...).  Pick the option number you want and press enter.  <br> To make sure the version of java switched, run: `java -version`

You will need to Get the latest version of Java from Oracle [here](http://www.oracle.com/technetwork/java/javase/downloads/index.html)  (get the one that is a tar.gz file, and make sure its the jdk and not a jre). You will need to get it on your server somehow.

Steps:

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. Delete OpenJDK: 

```
apt-get purge openjdk-\*
```

3\. If the directory /usr/local/java does not exist, create it and then change directory to it:

```
mkdir -p /usr/local/java
cd /usr/local/java
```

4\. You downloaded the latest version of java (the tar file).  Note the name, copy the file to /usr/local/java, and then unzip the file in that directory (here we assume the Java version is 8u45 and the tar file is called jdk-8u45-linux-x64.tar.gz):

```
tar xvzf jdk-8u45-linux-x64.tar.gz
```

> Note that after you unzip the file in /usr/local/java, a directory will be created in that folder that contains the phrase 'jdk'; locate that folder and note it (for this install the folder name was 'jdk1.8.0_45'), as you will have to alter some of the instructions here to use that folder (wherever the java version exists - in folder names, specifically - you will have to change them).

5\. Add information to the bottom of /etc/profile

 * You will have to do this once, either in the global profile (`/etc/profile`) or your own profile will the the .profile file in your home directory.  These instructions are how to set up in `/etc/profile`.
 * Open /etc/profile (using vi): 

 ```
 vi /etc/profile
 ```

 * Add this text to the bottom of that file:
 ```
JAVA_HOME=/usr/lib/jvm/jdk-21-oracle-x64
PATH=$PATH:$HOME/bin:$JAVA_HOME/bin
export JAVA_HOME
export PATH
 ```  
 * Your `JAVA_HOME` will almost assuredly be different - use the directory that contains the `bin` directory  
   * I find this with the command `ls -la /etc/alternatives/java` - stop just before `/bin` is mentioned  


 * Save the file and exit.

6\. Update the links in /etc/alternatives by running the following commands

```
update-alternatives --install "/usr/bin/java" "java" "/usr/lib/jvm/jdk-21-oracle-x64/bin/java" 1 
update-alternatives --install "/usr/bin/javac" "javac" "/usr/lib/jvm/jdk-21-oracle-x64/bin/javac" 1 
update-alternatives --install "/usr/bin/javaws" "javaws" "/usr/lib/jvm/jdk-21-oracle-x64/bin/javaws" 1 
update-alternatives --set java /usr/lib/jvm/jdk-21-oracle-x64/bin/java 
update-alternatives --set javac /usr/lib/jvm/jdk-21-oracle-x64/bin/javac 
update-alternatives --set javaws /usr/lib/jvm/jdk-21-oracle-x64/bin/javaws 
```  

!> Note that if you are installing Java 9+, 'javaws' is depricated and in Java 11+ it is fully removed - so this may fail.

7\. Source in the changes we just made (aka make the commands we just changed executable): `source /etc/profile`

---

# Installing Apache Ant
<span style='width: 20px; display:inline-block'>:bangbang:</span>

> To read more on Ant, please go [here](ant.apache.org); the binary download is [here](ant.apache.org/bindownload.cgi).

Ant is a Java library that helps build Java applications (although it can be used for other languages if the correct version is installed).

!> Install Java FIRST, either [from Oracle](/operating_systems/ubuntu/server_build?id=installing-java-from-oracle) or [from apt-get install](/operating_systems/ubuntu/server_build?id=install-java-jre)

**<font size="4"> Easy Method: Apt-Get</font>**

Steps:

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)

3\. Install using apt-get:
```
apt-get install ant
```

Ant should now be installed!

**<font size="4"> Long Method</font>**

You can also install ant from binary files, if you wish. Steps:

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. [Download the binary file(s)]](ant.apache.org/bindownload.cgi).

3\. Unzip /tar them in your preferred directory. I created and then used: 
```
mkdir -p /usr/local/ant
```

4\. Exit root:
```
exit
```

5\. Set up [your environment file](/operating_systems/ubuntu/linux_notes?id=the-profile). Put these lines at the end and save:
> export ANT_HOME=/usr/local/ant <br>
> export PATH=${PATH}:${ANT_HOME}/bin

6\. Source your profile: 
```
source ~/.profile
```
---

# Installing Apache Maven
<span style='width: 20px; display:inline-block'>:bangbang:</span>

Maven, much like Ant, is a Java library that helps build Java applications; its newer than Ant, and most people prefer Maven.  

To install Maven, do the following:  

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)

3\. Install using apt-get:
```
apt-get install maven
```  

---  

# Installing Gradle
<span style='width: 20px; display:inline-block'>:bangbang:</span>

> I took the instructions on how to install Gradle from [here](https://gradle.org/install/).  

Gradle, much like Ant and Maven, is a Java library that helps build Java applications. I usually use Maven, but Gradle is becoming more popular.  

To install Gradle, do the following:  

1\. Download the [official Gradle zip file](https://gradle.org/install/).  

2\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

3\. Make the new Gradle directory:  
```
mkdir /opt/gradle
```  

4\. Unzip the file to the `/opt/gradle` directory with:  
```
unzip -d /opt/gradle gradle-VERSION.zip
```  
* This assumes 
   * The zip file is named `gradle-VERSION.zip` (it won't be).  
   * you run this command from the same directory as the zip file is in.  

5\. Place the following at the end of your `~\.profile`:  
```
# set PATH to Gradle  
if [ -d "/opt/gradle/gradle-VERSION/bin" ] ; then
    PATH="/opt/gradle/gradle-VERSION/bin:$PATH"
fi
```  
* The `gradle-VERSION` part will be different.  

---

# Installing Protobuf

Protobuf (protocol buffers) act like 'XML' for data streams; the power behind them is you get to determine the structure.

The easy way to install it is:

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)

3\. Install using apt-get:
```
apt-get install protobuf-compiler
```

However, this may give you an old version; to get the new version you will have to get it manually / load it manually. The steps for this are:

> These instructions were found on [stackExchange](https://askubuntu.com/questions/1072683/how-can-i-install-protoc-on-ubuntu-16-04).

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)

3\. Download the .tar.gz file from [this page](https://github.com/protocolbuffers/protobuf/releases) (it will be called 'protobof-all-X.X.X.tar.gz', where the X.X.X will be a version number.

4\. Extract the contents to a folder.

5\. Enter the folder and run: `./configure`

6\. Run: `make`

7\. Run: `make check`

8\. Run: `make install`

9\. Refresh the shared library cache: `ldconfig`

To check it out, run `protoc --version`; know that you may have to exit and re-enter the unix session.


---

# Installing Git
<span style='width: 20px; display:inline-block'>:rotating_light:</span> <span style='width: 20px; display:inline-block'>:bangbang:</span>

>For more info on Git, please go to [Git-Scm](http://git-scm.com/), [Github](https://github.com/), or [my own page](/learn_to_code/git/).

Git is a program that is used to share project files with others. Often times teams use git for version control.

To install:

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)

3\. Install:
```
apt-get install git
```

4\. <font color="purple">Optional</font>: install the GUI for Git:
```
apt-get install git-gui
```
* Note that to actually launch the GUI, you will have to type `gitk`

---
  
# Installing Tilix
<span style='width: 20px; display:inline-block'>:rotating_light:</span> <span style='width: 20px; display:inline-block'>:bangbang:</span>

[Tilix](https://gnunn1.github.io/tilix-web/) is a very nice upgraded terminal.  

To install:

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)

3\. Install:
```
apt-get install tilix
```

> There is a minor issue on Ubuntu (as described [here](https://gnunn1.github.io/tilix-web/manual/vteconfig/)); one way to fix this is to go into Preferences/DefaultCommand and check `Run command as login shell`, but be warned - if you type `exit` to close terminals (as opposed to clicking the `x`) it can mess with mounted drives.  
![tilix_login_shell.png](images/tilix_login_shell.png)


---  

# Media Apps  

## Installing Plex    
<span style='width: 20px; display:inline-block'>:bangbang:</span>  

> For the official Plex install instructions, [go here](https://support.plex.tv/articles/200288586-installation/).  

[Plex](/tools/plex/plex_basics) is a tool you can install on a server that acts as a multimedia server - you can put movies, tv shows, and home videos on your server. Once installed, you can then download the Plex app on your phone / Roku / Smart TV / etc and watch your curated videos on these apps!  

How to install:  

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)  

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)  

3\. Download the Plex .deb file [here](https://www.plex.tv/media-server-downloads/?cat=computer&plat=linux)  

4\. Install the .deb file: `dpkg -i plex.deb`  
* This assumes the .deb file is named `plex.deb` - you may have to change this.  


## Installing VLC  
<span style='width: 20px; display:inline-block'>:rotating_light:</span> <span style='width: 20px; display:inline-block'>:bangbang:</span>

VLC is a powerful video package - [here is the VLC official page](https://www.videolan.org/vlc/) and [here is the download section](https://www.videolan.org/vlc/#download).  

To install:

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)

3\. Install:
```
apt-get install vlc libdvd-pkg libaacs-dev libdvdcss2 
```
* `libdvdcss2` is needed for DVDs.  
* `libdvd-pkg` and `libaacs-dev` are needed to support Blu Ray.  

4\. You should also install ffmpeg for the multimedia toolset: `apt-get install ffmpeg`  
* You may not use this right away, but its helpful later.  

In addition, [this site](https://echoshare.co/no-valid-processing-key-found-aacs-config-vlc/) claims that if you want Blu Ray files to play, you will have to put the KEYDB.cfg file (located [here](https://vlc-bluray.whoknowsmy.name/files/KEYDB.cfg) - you may want to save this is a good spot) in the directory `~/.config/aacs` (I can confirm this works as of Ubuntu 20.04, August 2022). 

> When opening Blu Ray movies, make _sure_ to select the 'Blu-ray' radio button; in addition, there is a 'No disc menus' checkbox - check that box.  


---  
# Music Apps  

## Tagger  
<span style='width: 20px; display:inline-block'>:frog:</span>  

Tagger is an easy to use music tag (metadata) editor; it helps with modifying tags in mp3 files.  

To install:

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)  

3\. Run: `snap install tagger`  


## SoundConverter  
<span style='width: 20px; display:inline-block'>:frog:</span>  
  
SoundConverter converts sound files to other formats (found as a suggestion [on askubuntu](https://askubuntu.com/questions/174287/how-do-i-convert-an-mp4-to-an-mp3)). Its nice for converting mp4s to mp3s.  

To install:

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)  

3\. Run: `apt-get install soundconverter`  

4\. You should also install ffmpeg for the multimedia toolset: `apt-get install ffmpeg`  
* You may not use this right away, but its helpful later.  


---  
# Visual Studio Code  

## Installing VS Code  

<span style='width: 20px; display:inline-block'>:rotating_light:</span> <span style='width: 20px; display:inline-block'>:bangbang:</span>

[Visual Studio Code](https://code.visualstudio.com/) is a good basic text editor that doubles as an IDE in a pinch. Up to date install instructions can be found [here](https://code.visualstudio.com/docs/setup/linux). Finally, some great feature metntions / plugins are [here](https://www.makeuseof.com/tag/10-essential-productivity-tips-visual-studio-code/).   

To install:

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)

3\. Install necessary packages:
```
apt-get install wget gpg
```

4\. Get the Microsoft gpg file, install to your keyrings, then delete the downloaded file:
```
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg
sh -c 'echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list'
rm -f packages.microsoft.gpg
```  

5\. Install `apt-transport-https`:  
```
apt-get install apt-transport-https
```  

6\. [Update all packages AGAIN](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)

7\. Install Visual Studio Code:  
```
apt-get install code # or code-insiders
```  

## Changing VS Code Properties  

The file `/home/USER_NAME/.config/Code/User/settings.json` (where `USER_NAME` is your login) governs general settings for VS Code, and you can make changes here. I like adding the following, so you can see the scroll bars much better:
```
     "workbench.colorCustomizations": {
        "scrollbarSlider.background": "#1D7CB5",
        "scrollbarSlider.hoverBackground": "#77C6F5",
        "scrollbarSlider.activeBackground": "#D1690E"
    }
```  
* This must be put inside the `{}`.  
* Make sure to use a comma after the previous last value.  
* `background` is the resting background, `hoverBackground` is when you are hovering over it, and `activeBackground` is when you are moving it.  
   * You can pick different colors [here](https://htmlcolorcodes.com/) if you wish.  



---  
  
# CIFS Share
<span style='width: 20px; display:inline-block'>:bangbang:</span>

!> This section is not required unless you wish to mount a Windows shared drive.

This section assumes the following: 
* You are currently logged into the Linux box (for our example we will assume your login is 'uperson').
* You have a valid Windows login / password (our example these will be 'myWindowsUserName' and 'myWindowsPassword' respectively).
* You have a FQDN for the remote Windows shared folder (our example will use 'someWindowsBox.it.yourcompany.com').
* You have your target folder name on the Windows shared drive (our example will use '/FolderOnWindowsMachine/Dropbox').
* You have selected an empty folder on Ubuntu to mount the drive (we will use /mnt/cifs_share)

!> Whenever you mount a folder, make SURE the mounting folder is empty! In addition, when you unmount a folder, make SURE you are not in that directory!

**CIFS** is an Ubuntu package that allows you to effortlessly mount an Ubuntu directory to a remote shared Windows drive.

## CIFS Setup

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)

3\. Install cifs-utils:

```
apt-get install cifs-utils
```

4\. Create a .cifs file in your home directory and change the file permissions:

```
touch /home/uperson/.cifs
chmod 600 /home/uperson/.cifs
```

5\. Open .cifs in vi, add the Windows login / password to the file, and save/quit from vi:

```
vi /home/uperson/.cifs
```
> username=myWindowsUserName <br>
> password=myWindowsPassword

6\. Create a mount point. Usually this is in a folder under /mnt. We will create an empty folder called cifs_share and this will be our mount point (we will also make sure we own the mount point):
```
mkdir -p /mnt/cifs_share
chown uperson:uperson /mnt/cifs_share
```

## Mounting the CIFS Drive
<span style='width: 20px; display:inline-block'>:bangbang:</span>

!> Make SURE you mount to an empty folder in Ubuntu!

We must first get the ID of the user that is mounting the drive (the user that altered the .cifs file). To get the ID of this user, type
```
id uperson
```

This will return something like:
```
uid=1004(uperson) gid=1004(uperson) groups=1004(uperson)
```

Note the 'uid' as we will need it to mount (we will assume uid will bet 1004 but it will almost CERTAINLY be different for you).

To mount, run the following command:
```
mount -t cifs //{FQDN of the Windows Server}/Windows/Base/Directory /Ubuntu/mount/Directory -o user,uid=UserIDYouFoundAbove,rw,credentials=/home/uperson/.cifs
```
* Do not actually include the { or }

A real-world example would be:
```
mount -t cifs //someWindowsBox.it.yourcompany.com/FolderOnWindowsMachine/Dropbox /mnt/cifs_share -o user,uid=1004,rw,credentials=/home/uperson/.cifs
```

## Unmounting the CIFS Drive
<span style='width: 20px; display:inline-block'>:bangbang:</span>

!> Make SURE you are not in the directory you are about to unmount!

To unmount, simply run:
```
umount -f /mnt/cifs_share
```

---

# Docsify
<span style='width: 20px; display:inline-block'>:bangbang:</span>


[Docsify](https://docsify.now.sh/) generates your documentation website on the fly. It smartly loads and parses your Markdown files and displays them as a website. To start using it, all you need to do is create an index.html and deploy it on GitHub Pages.

## Basics of Docsify and Markdown

If you want to know the basics of Docsify, [see my guide](learn_to_code/docsify/gettingstarted), and if you wish to learn a bit on the Markdown language [see my guide on that](learn_to_code/docsify/markdowntutorial).


## Docsify Install  
<span style='width: 20px; display:inline-block'>:bangbang:</span>

There are two ways I know of installing this: a more modern way (installing an actual Docsify app via node.js, no Apache server required) or a more obscure, older way (having a tarball and using an [Apache server](operating_systems/ubuntu/server_build?id=apache)).  

The first listed here is the more modern way (which I recommend); the second is the older way (using Apache).  


### Docsify Install (node.js)  

> I recommend this install - its much easier, provided you dont mind installing node.js. You can read more on this [here](https://github.com/docsifyjs/docsify-cli).   

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)

3\. Install the latest LTS version of node.js (if its not installed already).  
* This may seem odd, but React Native relies on the `npm` command, and that is difficult to install on its own without the rest of node.js - so install node.js.  
* To install, run these commands:
```  
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
apt-get install -y nodejs
```  

> According to [DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-20-04), you can simply run `apt install nodejs` and then `apt install npm` if you do not care about getting an older version of node.js _or_ npm - that said, if you want a more modern version of either, use the curl command given.  
   
4\. Upgrade `npm`
* The version of `npm` installed by node.js is probably very old, so upgrade it.  
* Run this as root: `npm install -g npm@6.14.4`  
  * Currently the latest version is `6.14.4` but this will probably be different for you.  

5\. Install Docsify: `npm i docsify-cli -g`  


> At this point, Docsify is installed; you can [initialize a Docsify project](operating_systems/ubuntu/server_build?id=initialize-docsify-nodejs) (you only have to do this once) and then you can [run the Docsify server](operating_systems/ubuntu/server_build?id=run-docsify-nodejs).  


### Docsify Install (Apache, Deprecated)  

!> [Apache](operating_systems/ubuntu/server_build?id=apache) MUST be running for Docsify to work!  

!> You must have access to the tar file; it is certainly possible to install Docsify without it if you visit [Docsify](https://docsify.now.sh/) to get the very few files you will need (and for an idea of what is needed, see [Basics of Docsify Files and Locations](/operating_systems/ubuntu/server_build?id=basics-of-docisfy-files-and-locations) above), but its easier just to get the tar.

!> You will have to install Docsify to a directory; we will use /opt/app/docsify, but you can put it wherever you wish. Know that if you do place it somewhere else, you must use that location instead of /opt/app/docsify wherever you see that location in these directions.

!> It is assumed you have a folder set up already with a main docsify folder (the initial folder that contains the docsify files README.md, _sidebar.md, and _navbar.md) and this folder is /mnt/docsify_library. In reality, it doesn't have to be /mnt/docsify_library; what is important is that the link in /var/www/html points to another directory that holds the 'assets' folder, the initial 'index.html', and a symbolic link named 'library' which MUST be pointing to whatever folder contains your main docsify folder with the files README.md, _sidebar.md, and _navbar.md.

> If you prefer to just use GitHub Pages to host a Docsify website, [here is how that is done](learn_to_code/docsify/gettingstarted?id=github-pages).

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)

3\. Make the install directory and change its permissions:

```
mkdir -p /opt/app/docsify
chown uperson:www-data /opt/app/docsify
```
* Change `uperson` to your login above, but leave the group `www-data` as is - if its not `www-data` it will probably break.

4\. Get the tar file (instructions on where this is tbd).


5\. Unpack file. This will make a folder like myDocsifyv1.0.0, but the numbers may be different. Take note of the folder name, and anywhere it says myDocsifyv1.0.0 in the instructions use your specific folder instead.

```
tar xvfp myDocsify.tar
```

6\. Make a symbolic link to the unpacked directory, and call the link myDocsify. We do it this way as we want to source control and links are an excellent way to do that:

```
ln -s myDocsifyv1.0.0 myDocsify
```

> Note that in the (newly named) myDocsify folder, there may be a symbolic link named 'library' - you WILL have to delete this and make it again, using the location of where you put the initial README.md file (for us its `/mnt/docsify_library`). 

7\. Now make another link so Apache can read it in /var/www/html

```
cd /var/www/html
ln -s /opt/app/docsify/myDocsify myDocsify
```

8\. At this point you should be done! Navigate to http://YourHostName.com/myDocsify/#/ (In step 7 we named the link 'myDocsify' in /var/www/html, so this is where 'myDocsify' comes from; however the /#/ at the end will remain for docsify).


## Docsify Files and Locations  
<span style='width: 20px; display:inline-block'>:bangbang:</span>



### Docsify Files (node.js method)  

When you [initialize](operating_systems/ubuntu/server_build?id=initialize-docsify-nodejs) the Docsify host, a directory is created which contains some files for the Docsify host. _This created directory is the base directory of your Docsify host._  

A few things are present by default in the initialized directory (these must be present):  
* A file called `index.html`  
  * This file instructs the server on how to build every page in this Docsify host.  
  * When adding add-ons for Docsify, often you will be instructed to add a line similar to `<script src="vendor/docsify.js"></script>` and possibly modify `window.$docsify` - you do both in this file.  
* A directory called `vendor/`  
  * A file called `docsify.js`  
    * This is the driving force / script file behind Docsify  
  * A `themes/` directory  
    * Various style sheets  
      * `vue.css` is stored here, as an example  
* `README.md`  
  * This file is written in the Markdown language and acts as a 'hub' for the folder.  
  * Typically, the contents of README.md is a simple listing of the markdown files available in the current folder, but you could put other things in here as well if you wanted.  
  * EVERY subdirectory in this directory MUST have a README.md.  
* `.nojekyll`  


The above are the base files - but you can (and SHOULD) have more:  
* `_sidebar.md`  
  * This file populates the sidebar navigation menu on the left of the page.  
  * This file is not present initially, but its a good file to have.  
  * EVERY subdirectory can have a `_sidebar.md`, but its not a requirement.  
  * `window.$docsify` in the file `index.html` controls the depth of this.   
    * I like to set `maxLevel: 4` and `subMaxLevel: 4`.    
* `_navbar.md`  
  * This file populates the navigation menu across the top of the page.  
  * `_navbar.md` is _not_ required for every level - if a sublevel is missing this file, it will inherit it from a parent directory.  
  * The position is _not_ static - it will scroll away if you go down the page  
  * The initial level goes across the top  
    * Subsequent levels go in a dropdown  
    * I dont like going more than one sublevel, as it gives it a box with scrollbars   
  * This file is not present initially.  
* Other markdown (i.e. `.md`) files  
  * These markdown files will be independent documents that are centered around a theme  
    * For example, if you wanted a document that detailed the contents of your garage, you could have a `garage.md` file here.  
    * When referencing these files in the web browser, do not use the `.md`  
      * For example, `garage.md` could be referenced as `http://YOUR.IP.HERE/#/garage`  
* Other directories  
  * Feel free to organize your markdown files in subdirectories  
  * Each subdirectory _must_ have a `README.md`, but its also a good idea to have a `_sidebar.md` file too.  
  * For example, if you made a subdirectory `rooms` you could reference it in your web browser as so: `http://YOUR.IP.HERE/#/rooms/`  
    * This relies on the `README.md` being present.  


### Docsify Files (Apache method)  

There are a few rules that must be followed in order to use Docsify. Firstly, you must create a folder in the [Apache web root directory](/operating_systems/ubuntu/server_build?id=the-apache-document-root) that will house Docsify (although please note, this folder can be a symbolic link, which I instead of an actual folder). 

While you can create an actual folder in the Apache web root directory, we create a link to another link that points to our real docsify folder. The first link is 'myDocsify' which resides in /var/www/html and points to /opt/app/docsify/myDocsify; /opt/app/docsify/myDocsify is a link itself which points to /opt/app/docsify/myDocsifyv1.0.0 (it may seem a bit confusing as to why we have a link called 'myDocsify' pointing to the actual folder 'myDocsifyv1.0.0' which resides right next to the link, but we do this for version control purposes). In this case, the Apache web server thinks our Docsify base is in /var/www/html/myDocsify but its actually in /opt/app/docsify/myDocsifyv1.0.0 (currently, until we change our version of Docsify). For the purposes of this discussion, /var/www/html/myDocsify will be considered our base Docsify folder.

In the base Docsify folder, 3 things must be present:
* An 'assets' folder, which houses the JavaScript (and other) files that actually powers Docsify. The four folders under this folder are
 * css - A folder that houses the css that is used by Docsify
 * fonts - A folder that houses the fonts that are used by Docsify
 * img - A folder that houses the images that are used by Docsify
 * js - A folder that houses the JavaScript that is used by Docsify
   * Its under this folder where the heart of Docsify resides
   * You can put all add-ons and extensions to Docsify here
   * The folder 'docsify' resides here; under this folder is simply a version number and under this folder resides the JavaScript that powers Docsify:
     * docsify.js
     * docsify.min.js
* A file called 'index.html'
 * This file instructs Apache on how to render the base skeleton of Docsify.

!> !!!NOTE!!! You may have to change one thing in this file: the basePath. Note the directory name of the docsify folder right under /var/www/html (for us its myDocsify), find the basePath variable in index.html, and change it to '/myDocsify/library/'

* A 'library' folder
  * This 'library' folder is the 'base' location of what is displayed by Docsify from the web page perspective. 
    * This 'library' folder can be a symbolic link; as far as we are concerned, we do not have a 'library' folder but a 'library' link.
  * This folder must contain
    * README.md - This file is written in the Markdown language and acts as a 'hub' for the folder.  Typically, the contents of README.md is a simple listing of the markdown files available in the current folder, but you could put other things in here as well if you wanted. EVERY subfolder in the library folder MUST have a README.md. 
    * `_sidebar.md`  
      * This file populates the sidebar navigation menu on the left of the page.  
      * This file is not present initially, but its a good file to have.  
      * EVERY subdirectory can have a `_sidebar.md`, but its not a requirement.  
      * `window.$docsify` in the file `index.html` controls the depth of this.   
        * I like to set `maxLevel: 4` and `subMaxLevel: 4`.    
    * `_navbar.md`  
      * This file populates the navigation menu across the top of the page.  
      * `_navbar.md` is _not_ required for every level - if a sublevel is missing this file, it will inherit it from a parent directory.  
      * The position is _not_ static - it will scroll away if you go down the page  
      * The initial level goes across the top  
      * Subsequent levels go in a dropdown  
      * I dont like going more than one sublevel, as it gives it a box with scrollbars   
      * This file is not present initially.  
    * (OPTIONAL if the main folder is simply used to navigate to subfolders) ???.md - This file will be named something logical that describes your documentation (so if this was documentation about birds, it may be called birds.md, for example). This file will be the main document that houses all of the critical information contained in your documentation, describing whatever process you are covering in detail.
  * The 'library' folder can contain other folders as well
    * Each folder can act like its own semi-independent Docsify entity, but these folders must contain at least the following files:
      * README.md - This file is written in the Markdown language and acts as a 'hub' for the folder.  Typically, the contents of README.md is a simple listing of the markdown files available in the current folder, but you could put other things in here as well if you wanted. EVERY subfolder in the library folder MUST have a README.md. 
      * _sidebar.md - This file populates the sidebar navigation menu on the left of the page.
      * ???.md - This file will be named something logical that describes your documentation (so if this was documentation about birds, it may be called birds.md, for example). This file will be the main document that houses all of the critical information contained in your documentation, describing whatever process you are covering in detail.


## Initialize Docsify (node.js)  

> If you are looking for how to initialize the [Apache](operating_systems/ubuntu/server_build?id=apache) method - there is no initialization for this, just run the Apache server.  

You will have to run an initial setup for Docsify for each instance of Docsify you wish to run (using the node.js method) - this has to be done only once. Pick a spot where you wish to store _all_ of your instances of Docsify (I like `/docsify` but you can pick what you wish), navigate there, then run:  
```
docsify init NAME_OF_REPO --local --theme vue  
```  
* `NAME_OF_REPO` is the name of the Docsify repo you are initializing  
  * All files associated with `NAME_OF_REPO` will be stored inside the `NAME_OF_REPO` directory  
* This elects to store some of the local files here (most notably the base .css and .js files) via `--local` (or `-l`)   
  * The default is _not local_, but if you wanted to expressly state that use `--no-local`
* The `--theme` (or `-t`) sets the 'theme'  
  * Values: `vue, buble, dark, pure`  
    * Default is `vue`  
* You can read more on this [here](https://github.com/docsifyjs/docsify-cli).  

Now, simply [run the Docsify server](operating_systems/ubuntu/server_build?id=run-docsify-nodejs).  

## Run Docsify (node.js)  

> If you are looking for how to run the [Apache](operating_systems/ubuntu/server_build?id=apache) method - there is no run for this directly, just run the Apache server.  

To run the node.js instance of Docsify:  
```
docsify serve NAME_OF_REPO --port 80 &disown
```  
* The `NAME_OF_REPO` is the same name used [when you initialized the Docsify project](operating_systems/ubuntu/server_build?id=initialize-docsify-nodejs).  
  * Remember, before you can run the Docsify host you must [initialize](operating_systems/ubuntu/server_build?id=initialize-docsify-nodejs) it.  
    * This only has to be done once.  
* You _must_ run this in the parent directory of `NAME_OF_REPO`.  
* The `--port` can be what you wish (I used `80` above but feel free to change that as needed / necessary).  
* The `&disown` is necessary to have the server run in the background, regardless of your account being logged in or not.  
* You can read more on this [here](https://github.com/docsifyjs/docsify-cli).  

## Docsify: index.html  

The file `index.html` holds a bunch of things that can be configured for Docsify.  The `window.$docsify` object houses most of it - here is a basic one I use:  
```
    <script>
    window.$docsify = {
	   name: (
         '<img'
         + '  src="./someImage.jpg"'
         + '  width="88" data-no-zoom=""'
         + '/><br />'
       ),
      repo: '', 

      loadSidebar: true,
      auto2top: true,
      maxLevel: 4,
      loadNavbar: true,
      subMaxLevel: 4,
      search: 'auto', // default

      // configuration parameters to add 
      search: {
        maxAge: 86400000, // Expiration time, the default one day
        paths: 'auto',
        placeholder: 'Type to search',
        noData: 'No Results!',
        // Headline depth, 1 - 6
        depth: 6,
        hideOtherSidebarContent: false, // whether or not to hide other sidebar content
     }
    }
  </script>
```
* The `<img` section adds a constant image at the top of the navigation bar.  
  * Change `/someImage.jpg` to whatever you wish
* This assumes you are using the `Search` plugin.  

After the `<script>` section that houses the `window.$docsify` object, you can list all of your add-ons; if you do not care if these are not accessible offline you can reference them directly like so:
```
  <script src="//unpkg.com/docsify/lib/docsify.min.js"></script>
```  
* Referenced is the main Docsify script  
  * If you do _not_ chose to [initialize locally](operating_systems/ubuntu/server_build?id=initialize-docsify-nodejs), the Docsify script will be referenced remotely like this.  

However, if you want them available in situations where you lose the internet, you can reference them like so: 
```
<script src="vendor/docsify.js"></script>  
```  
* Referenced is the main Docsify script  
  * If you chose to [initialize locally](operating_systems/ubuntu/server_build?id=initialize-docsify-nodejs), it _will_ make this local - otherwise, the online one will be referenced.  
* In theory, if you could download the `.js` script, you could call it locally and _not_ need the online version.  

## Docsify Plugins  

> You will notice there are two Javascript files for most things - `.js` and `.min.js`. the `.min.js` is a [minified](https://stackoverflow.com/questions/3475024/whats-the-difference-between-jquery-js-and-jquery-min-js) version - its the same functionality, but all unnecessary characters are removed. These are smaller and _may_ be faster to load. Its good to download the `.js` file to understand what is going on in the Javascript, but functionally - use the `.min.js` file as your actual script.  

These list a bunch of helpful plugins / add-ons I like to use.  

You will notice that for all of them, you will need to add a `<script src` - most of these go to [https://unpkg.com](https://unpkg.com).  

If, instead, you wish to download the `.js` file locally and simply reference it - you do have the option to do that. For example, PlantUML is referenced via `<script src="//unpkg.com/docsify-plantuml/dist/docsify-plantuml.min.js"></script>`; you _could_ navigate to [https://unpkg.com/browse/docsify-plantuml@1.3.2/dist/](https://unpkg.com/browse/docsify-plantuml@1.3.2/dist/) (you can go back and pick a specific version if you wish) and then download the `.js` files there (you may have to click on them then directly, click `View Raw`, then copy them to a `.js` file). You could download (i.e. save) the `docsify-plantuml.min.js` file locally, put it under the `vendor/plantuml/` directory, then simply reference `<script src="vendor/plantuml/docsify-plantuml.min.js"></script>`  

**<font size="4">Docsify</font>**    
* Docsify itself needs a Javascript file  
  * This is included when you [initialize](operating_systems/ubuntu/server_build?id=initialize-docsify-nodejs)  
    * No action necessary, but... 
    * If you want to change it for any reason (upgrade, make it local, etc) its nice to know where it is.  
  * [UNPKG Website](https://unpkg.com/browse/docsify@4.13.1/lib/)  
    * You can navigate up and change the version, if you wish  
    * You will have to find either `docsify.js` or `docsify.min.js` in this list      
* Install:  
  * Place this script towards the end of the `</body>` in the Docsify `index.html` file:  
    * `<script src="//unpkg.com/docsify/lib/docsify.min.js"></script>`  
    * Note you _cannot_ have multiples of these, and you _cannot have both a local instance and reference the remote instance simultaneously_.  
 

**<font size="4">Search</font>**  
* Search is a base plugin for Docsify that allows you to put a search bar in the navigator sidebar.  
  * [UNPKG Website](https://unpkg.com/browse/docsify@4.13.1/lib/plugins/)  
    * You can navigate up and change the version, if you wish  
    * You will have to find either `search.js` or `search.min.js` in this list  
* The files included in the search _must_ be visible in the `_sidebar.md` file - if its not there (or not listed under a referenced directory there), the file will not be searched.    
* Install:  
  * Place this script towards the end of the `</body>` in the Docsify `index.html` file:  
    * `<script src="//unpkg.com/docsify/lib/plugins/search.min.js"></script>`  
  * Place this in `window.$docsify` in the Docsify `index.html` file:  
```
      search: 'auto', // default

      // complete configuration parameters
      search: {
        maxAge: 86400000, // Expiration time, the default one day
        paths: 'auto',
        placeholder: 'Type to search',
        noData: 'No Results!',
        // Headline depth, 1 - 6
        depth: 6,
        hideOtherSidebarContent: false, // whether or not to hide other sidebar content
     }, 
```  


**<font size="4">Zoom-Image</font>**    
* Zoom-Image allows you to zoom in on an image  
  * [UNPKG Website](https://unpkg.com/browse/docsify@4.13.1/lib/plugins/)  
    * You can navigate up and change the version, if you wish  
    * You will have to find either `zoom-image.js` or `zoom-image.min.js` in this list  
* Install:  
  * Place this script towards the end of the `</body>` in the Docsify `index.html` file:  
    * `<script src="//unpkg.com/docsify/lib/plugins/zoom-image.min.js"></script>`  


**<font size="4">Emojis</font>**  
* Emojis allows you to print emojis 
  * [UNPKG Website](https://unpkg.com/browse/docsify@4.13.1/lib/plugins/)  
    * You can navigate up and change the version, if you wish  
    * You will have to find either `emoji.js` or `emoji.min.js` in this list  
  * NOTE: Emojis seem to have been deprecated as of Docsify version 4.13  
* Install:  
  * Place this script towards the end of the `</body>` in the Docsify `index.html` file:  
    * `<script src="//unpkg.com/docsify/lib/plugins/emoji.min.js"></script>`  
* Referencing emojis
  * Emojis are referenced like this: `:emoji:`  
    * Example: `:frog:`  
      * I like wrapping it in a span like so: `<span style='width: 20px; display:inline-block'>:frog:</span>`  
  * The full list of emojis are [here](https://unpkg.com/browse/docsify@4.13.1/lib/plugins/emoji.js)  


**<font size="4">PlantUML</font>**    
* PlantUML allows you to quickly insert UML diagrams into your Docsify page using PlantUML.  
  * [Github Website](https://github.com/imyelo/docsify-plantuml)  
  * [UNPKG Website](https://unpkg.com/browse/docsify-plantuml@1.3.2/dist/)  
    * You can navigate up and change the version, if you wish  
* Install:  
  * Place this script towards the end of the `</body>` in the Docsify `index.html` file:  
    * `<script src="//unpkg.com/docsify-plantuml/dist/docsify-plantuml.min.js"></script>`  
    * Remember, you can also just download the `.js` files, HOWEVER thsoe `.js` files reference [plantuml.com](https://plantuml.com) so it may be moot in this instance.  
  * Place this in `window.$docsify` in the Docsify `index.html` file:  
```
       plantuml: {
          skin: 'classic',
          renderSvgAsObject: true,
       },
```  
    * `renderSvgAsObject: true` allows it to be interactive (i.e. links will work)  
      * That said, adding this will remove the zoom functionality - which is also important  
    * `serverPath` is not used here
      * By default, the official PlantUML server is used. 
      If you have your own, configure it using the serverPath option: `serverPath: 'https://custom-server.local/plantuml/png/',`  


**<font size="4">Prism</font>**  
* Prism is a lightweight, extensible syntax highlighter  
  * [Website](http://prismjs.com/)  
    * [Examples](https://prismjs.com/examples.html)  
  * [UNPKG Website](https://unpkg.com/browse/prismjs@1.13.0/components/)  
    * You can navigate up and change the version, if you wish  
* Install:  
  * Place this script towards the end of the `</body>` in the Docsify `index.html` file:  
    * `<script src="//unpkg.com/browse/prismjs@1.29.0/components/prism-java.min.js"></script>`  
      * There are several of these - this is simply one example  
* Scripts I typically use  
  * prism-bash.min.js  
  * prism-c.min.js  
  * prism-cpp.min.js  
  * prism-latex.min.js  
  * prism-python.min.js  
  * prism-sql.min.js  
  * prism-perl.min.js  
  * prism-java.min.js  
  * prism-javascript.min.js  


---  

# Handling Secure Boot
<span style='width: 20px; display:inline-block'>:rotating_light:</span> 

>For further reading, please see [here](http://askubuntu.com/questions/760671/could-not-load-vboxdrv-after-upgrade-to-ubuntu-16-04-and-i-want-to-keep-secur), [here](https://forums.virtualbox.org/viewtopic.php?f=7&t=77363&start=15), and [here](http://askubuntu.com/questions/726052/ubuntu-booting-in-insecure-mode-with-secureboot-enabled).

**<font size="4"></font>**
>You will need a separate password for interacting with Secure Boot.

Starting in Ubuntu 15.10, Ubuntu uses something called ‘Secure Boot’ which signs packages that need to somehow alter the bootloader. Its suggested that if its at all possible to leave Secure Boot active that you do so.

Unfortunately, VirtualBox is affected by this, so we need to properly sign specific VirtualBox executables if you wish to use Secure Boot and Virtualbox.

## Enabling Secure Boot
<span style='width: 20px; display:inline-block'>:rotating_light:</span> 

To enable Secure Boot, run this command.  Note that it does not take effect until you restart – and when you do restart you will automatically be dumped to a screen prompting you to interact with Secure Boot.  If you do nothing at this screen, your desired changes will not take effect. To enable:
```
mokutil --enable-validation
```

## Disabling Secure Boot
<span style='width: 20px; display:inline-block'>:rotating_light:</span> 

To disable Secure Boot, run this command.  Note that it does not take effect until you restart – and when you do restart you will automatically be dumped to a screen prompting you to interact with Secure Boot.  If you do nothing at this screen, your desired changes will not take effect. To disable:
```
mokutil --disable-validation
```

## Creating the Key (Less Secure)
<span style='width: 20px; display:inline-block'>:rotating_light:</span> 

There needs to be a private RSA key created by you that can be used to sign files that will be permitted by Secure Boot. Its probably a good idea to log in as root for this; also, change directory to a folder that can house sensitive files.

To create this key, run:
```
openssl req -new -x509 -newkey rsa:2048 -keyout USERNAME.priv -outform DER -out MOK.der -nodes -days 36500 -subj "/CN=USERNAME/"
```
* USERNAME.priv is the name of the key file generated – choose whatever you want it to be
* CN is the name of the signing entity – so CN=USERNAME above means USERNAME is the entity that is signing the file (you may want to change USERNAME here too).
* MOK.der is a file that is generated and is needed by the signing process

## Alternate Key Creation (More Secure)
<span style='width: 20px; display:inline-block'>:rotating_light:</span> 

In theory, if someone gets a hold of the .priv file, they can use it to sign their own malicious files. A more secure way to create the key is to save your Secure Boot password to an environment variable and to then run the create key without the -nodes flag:
```
export KBUILD_SIGN_PIN='YourSelectedSecureBootPasswordHere'
openssl req -new -x509 -newkey rsa:2048 -keyout USERNAME.priv -outform DER -out MOK.der -days 36500 -subj "/CN=USERNAME/"
```
* Note you will need to change the password and 'USERNAME' in two places above.

## Adding RSA Signature to Secure Boot
<span style='width: 20px; display:inline-block'>:rotating_light:</span> 

You need to now add the RSA signature to specific executables.

## Example: Adding Virtualbox RSA Signatures
<span style='width: 20px; display:inline-block'>:rotating_light:</span> 

For VirtualBox, you need run these commands (where the final one loads the MOK.der file) for the main drv executable plus three other files (for added functionality such as networking):
```
/usr/src/linux-headers-$(uname -r)/scripts/sign-file sha256 ./USERNAME.priv ./MOK.der $(modinfo -n vboxdrv)
/usr/src/linux-headers-$(uname -r)/scripts/sign-file sha256 ./USERNAME.priv ./MOK.der $(modinfo -n vboxnetadp)
/usr/src/linux-headers-$(uname -r)/scripts/sign-file sha256 ./USERNAME.priv ./MOK.der $(modinfo -n vboxnetflt)
/usr/src/linux-headers-$(uname -r)/scripts/sign-file sha256 ./USERNAME.priv ./MOK.der $(modinfo -n vboxpci)
mokutil --import MOK.der
```
* You must be in the same directory where the MOK.der file is located
* You must change USERNAME.priv to whatever you used

# Installing Brave
<span style='width: 20px; display:inline-block'>:frog:</span>  
![brave](https://brave-browser.readthedocs.io/en/latest/_static/brave-release.svg)  

[Brave](https://brave.com/) is a web browser built for privacy - this is my choice of browsers, as it natively supports adblocks, javascript blocking, and several protections against tracking.

The instructions on installing Brave [can be found here for linux](https://brave-browser.readthedocs.io/en/latest/installing-brave.html#linux) (and [here](https://laptop-updates.brave.com/latest/winx64) for Windows), but here is a recap of how to install on Linux:

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. Run:
```
curl -s https://brave-browser-apt-release.s3.brave.com/brave-core.asc | sudo apt-key --keyring /etc/apt/trusted.gpg.d/brave-browser-release.gpg add -
```

3\. Source in /etc/os-release:
```
source /etc/os-release
```

4\. Run:
```
echo "deb [arch=amd64] https://brave-browser-apt-release.s3.brave.com/ $UBUNTU_CODENAME main" | sudo tee /etc/apt/sources.list.d/brave-browser-release-${UBUNTU_CODENAME}.list
```

5\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)

6\. Install Brave:
```
apt install brave-keyring brave-browser
```

!> While not mandatory, you should disable 'Use graphics acceleration when available' in `settings/System` - otherwise, Brave (and Chrome, for that matter) has a tendency to over-use resources, sometimes leading your laptop to lock!  

# Adding Security To Firefox
<span style='width: 20px; display:inline-block'>:rotating_light:</span> <span style='width: 20px; display:inline-block'>:frog:</span>

!> I highly suggest that you use [Brave](operating_systems/ubuntu/server_build?id=installing-brave) instead of FireFox, as Brave is built for privacy.  

> These suggestions are taken from [here](https://wiki.ubuntu.com/BasicSecurity) and are very popular.

There are many security threats on the web; fortunately Firefox has some native add-ons that can help greatly reduce threats. 

## Applying Proxy to Firefox
<span style='width: 20px; display:inline-block'>:rotating_light:</span> <span style='width: 20px; display:inline-block'>:frog:</span>

> This is different from the [proxy in wgetrc](/operating_systems/ubuntu/server_build?id=setting-up-the-proxy-in-wgetrc) or the [proxy in apt.cnf](/operating_systems/ubuntu/server_build?id=setting-up-the-proxy-in-aptconf)

If this is a desktop machine _and_ you are behind the firewall, you will need to set up an automatic proxy for Firefox (or Chrome, but this assumes you use Firefox).

Steps:

1\. Open Firefox and click ‘Open Menu’ (the 3 horizontal lines) then ‘Preferences’.

2\. Click on the ‘General’ tab.

3\. Where it says ‘Network Settings’, click the ‘Settings…’ button.

4\. Select the ‘Automatic proxy configuration URL:’ and enter your company's proxy; below is an example: 
```
http://your.companys.proxy.com/companyproxy.cgi
```

5\. Click 'OK'.

## Adding an Add-Ons in Firefox
<span style='width: 20px; display:inline-block'>:rotating_light:</span> <span style='width: 20px; display:inline-block'>:frog:</span>

1\. Open Firefox and click ‘Open Menu’ (the 3 horizontal lines) then ‘Add-ons’.
* Make sure “Get Add-Ons” are highlighted.

2\. At the bottom there will be a 'Find more add-ons' button; click it.

3\. In the “Find add-ons” text box, type the name of the add-on you are looking for and press enter.

4\. Click on the add-on you wish to install.

5\. Click the “+ Add to Firefox” button.

6\. To inspect your add-ons at any time, open the add-ons in Firefox and make sure the “Extensions” selection is highlighted (you can also uninstall them from here).

## Firefox Security Add-Ons
<span style='width: 20px; display:inline-block'>:rotating_light:</span> <span style='width: 20px; display:inline-block'>:frog:</span>

### Adblock Plus
<span style='width: 20px; display:inline-block'>:rotating_light:</span> <span style='width: 20px; display:inline-block'>:frog:</span>

[Adblock Plus](https://adblockplus.org/en/firefox) blocks adds from thousands of known add URLs, which can contain malicious code.  This also blocks popup adds.  Its useful because it automatically filters out many malicious sites that you never intended on visiting, but unknowingly touched through an add on the side of a site you did navigate to.

### NoScript
<span style='width: 20px; display:inline-block'>:rotating_light:</span> <span style='width: 20px; display:inline-block'>:frog:</span>

[NoScript](https://addons.mozilla.org/en-US/firefox/addon/noscript/) blocks all scripts on a site.  Scripts can do some malicious things and should only be run if you trust the site.

Often times, NoScript will disable scripts for a page you do want to interact with.  If this is the case (or you notice clicking buttons are not doing what you expect them to do), you can turn off NoScript for the particular page you are on (either on a temporary basis or permanently). In the top right you will see an image with an 'S' crossed out; this is noscript. Click it and it will list all potential websites the current website interacts with (note below the only one available is 'adblockplus.org' but there are usually multiple websites listed):

![noscript](images/noscript.png)

You have the option of 'Temp Trusted' / 'Trusted' / 'Untrusted' (with a few other options). To allow temp access, select 'Temp Trusted'; if you never want to do this again for this particular website, select 'Trusted'. If you do not trust the listed site

> Each time you trust / temp trust a website it will allow that website to load. You may have to do this a few times for any given website you visit (if you trust it), as it may continuously try to load new websites that it could not access previously.

!> If your interaction is not hampered by scripts being disabled, leave the scripts disabled for that site.  Better safe than sorry!

### Legacy Add-Ons
<span style='width: 20px; display:inline-block'>:rotating_light:</span> <span style='width: 20px; display:inline-block'>:frog:</span>

I used to advocate for [BetterPrivacy](https://addons.mozilla.org/en-US/firefox/addon/betterprivacy/) and [Click&Clean](https://addons.mozilla.org/en-US/firefox/addon/clickclean/), but BetterPrivacy was removed and Click&Clean seems to have lost support in more recent versions of Firefox.

# Installing GIMP
<span style='width: 20px; display:inline-block'>:rotating_light:</span> <span style='width: 20px; display:inline-block'>:frog:</span> <span style='width: 20px; display:inline-block'>:bangbang:</span>

> There are Gimp tutorials [here](http://www.gimp.org/tutorials/) and [here][http://meetthegimp.org/); also a quick note on [making the background of an image transparent](http://graphicdesign.stackexchange.com/questions/5446/making-the-background-of-an-image-transparent-in-gimp)

Occasionally you will need to use an image editor – fortunately there is an “official” one for Ubuntu available (although its not installed out of the box): Gimp.

Screenshots work on the Ubuntu desktop as they do on windows – press the print screen/printscreen/prntscn button and your screen will automatically be saved as a .png file to your ~/Pictures folder (older versions gave you an option to save the image to a file or to copy it to the clipboard).  You can then interact with this via Gimp.

Steps to Install:

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root).

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)

3\. Run: 
```
apt-get install gimp
```

4\. You should be able to right click a running instance of gimp to save it to your favorites, but if not, you have the option of [creating an executable icon](/operating_systems/ubuntu/linux_notes?id=creating-executable-icons).


# Android Studio
<span style='width: 20px; display:inline-block'>:rotating_light:</span> <span style='width: 20px; display:inline-block'>:frog:</span> <span style='width: 20px; display:inline-block'>:bangbang:</span>

!> The old way of writing Android programs was downloading and installing the Android Developer Tools (ADT), but Android Studio is now the official Android IDE as of January 2015.

!> Android studio requires at least Java 1.7, so install Java FIRST, either [from Oracle](/operating_systems/ubuntu/server_build?id=installing-java-from-oracle) or [from apt-get install](/operating_systems/ubuntu/server_build?id=install-java-jre)

!> The download and install is VERY large - one gig currently!

If you wish to write Android programs on your Ubuntu desktop, you will need to install Android Studio.

## Install Android Studio
<span style='width: 20px; display:inline-block'>:rotating_light:</span> <span style='width: 20px; display:inline-block'>:frog:</span> <span style='width: 20px; display:inline-block'>:bangbang:</span>

Steps to Install (note this is not done under root, but your own login):

1\. Download the latest version of [Android Studio here](http://developer.android.com/sdk/index.html); you will download a zip file for Linux. Make sure it’s the ‘Studio Package’ and NOT the ‘SDK Tools Only’ package.

2\. Pick a directory (I used either `/android-studio` or `~/apps/android-studio` (this one preferable)) and open the zip file in that directory.

3\. Switch to the ‘bin’ directory in the directory you created: `cd ~/apps/android-studio/bin`

4\. Run the studio launcher: `./studio.sh`

!> You may have to be on a network that is not behind a firewall to complete the file downloads this process launches.

## Errors in Android Studio Install
**<font size="4">Android Error Handling: Unable to run mksdcard SDK tool.</font>**

> These notes came from [here](http://stackoverflow.com/questions/29241640/unable-to-run-mksdcard-sdk-tool-ubuntu).

This can happen when running this tool on a 64 bit platform – apparently some of the code is tailored for the 32 bit platform.

To combat this, install the required 32 bit platform files (as root): 
```
apt-get install lib32z1 lib32ncurses5 lib32bz2-1.0 lib32stdc++6
```

## Locking Android Studio To Favorites
<span style='width: 20px; display:inline-block'>:rotating_light:</span> <span style='width: 20px; display:inline-block'>:frog:</span> <span style='width: 20px; display:inline-block'>:bangbang:</span>

Android Studio may not lock to the launcher as most programs do. if not, you can [create an executable icon](/operating_systems/ubuntu/linux_notes?id=creating-executable-icons) with the following settings:
* Name=Android Studio
* Comment=Android Studio
* Icon=/android-studio/bin/studio.png

!> This assumes you installed to /android-studio

* Exec=/android-studio/bin/studio.sh

!> This assumes you installed to /android-studio

* Categories=Android;Development;IDE;
* StartupWMClass=Android

# Eclipse
<span style='width: 20px; display:inline-block'>:rotating_light:</span> <span style='width: 20px; display:inline-block'>:frog:</span> <span style='width: 20px; display:inline-block'>:bangbang:</span> <span style='width: 20px; display:inline-block'>:snake:</span>

!> Eclipse requires Java, so install Java FIRST, either [from Oracle](/operating_systems/ubuntu/server_build?id=installing-java-from-oracle) or [from apt-get install](/operating_systems/ubuntu/server_build?id=install-java-jre).

Eclipse is a powerful IDE that allow you to write programs; it was initially written for Java, but it can do Python, C, and many other languages in a nice editor.

## Installing Eclipse
<span style='width: 20px; display:inline-block'>:rotating_light:</span> <span style='width: 20px; display:inline-block'>:frog:</span> <span style='width: 20px; display:inline-block'>:bangbang:</span>

You can install Eclipse with a traditional `apt-get install eclipse-platform` or `snap install eclipse`, but I prefer to [download Eclipse from their site](https://www.eclipse.org/downloads/), and then install it.

If you decide to download yourself, do **not** install as root, and open the downloaded compressed file in a temp directory - it will ask you to install elsewhere, so you will have to pick a location (I recommend a place in your home directory). After the install is complete you can delete everything in the temp directory.


## Installing the Eclipse C/C++ Editor, CDT
<span style='width: 20px; display:inline-block'>:rotating_light:</span> <span style='width: 20px; display:inline-block'>:frog:</span> <span style='width: 20px; display:inline-block'>:bangbang:</span>

!> This assumes you want the ‘kepler’ version of Eclipse.  If you did not – or if this does not work – you must find the correct p2 repository [here](https://eclipse.org/cdt/downloads.php)

> For more information, [visit this site](https://eclipse.org/cdt/downloads.php)

Steps:

1\. Open Eclipse.

2\. Go to 'Help / Install New Software...'

3\. In the 'Work With' text box, enter: `http://download.eclipse.org/tools/cdt/releases/kepler` and press 'Enter' 
* This is called the ‘p2 software repository link

4\. Press Enter.

5\. Select all options that appear.

6\. Follow all on-screen instructions from there.


## Install the Eclipse git program, Egit
<span style='width: 20px; display:inline-block'>:rotating_light:</span> <span style='width: 20px; display:inline-block'>:frog:</span> <span style='width: 20px; display:inline-block'>:bangbang:</span>

Steps:

1\. Open Eclipse.

2\. Go to 'Help / Install New Software...'

3\. In the 'Work With' text box, enter: `http://download.eclipse.org/egit/updates` and press 'Enter' 

4\. Press Enter.

5\. Two options will appear: 'Eclipse Git Team Provider' (do NOT install JGit). Check both and click next/next/accept/finish.

6\. Follow all on-screen instructions from there.

## Install the Perl add-on for Eclipse, EPIC (Eclipse Perl Integration)
<span style='width: 20px; display:inline-block'>:rotating_light:</span> <span style='width: 20px; display:inline-block'>:frog:</span> <span style='width: 20px; display:inline-block'>:bangbang:</span> <span style='width: 20px; display:inline-block'>:snake:</span>

> For more information, [visit this site](http://www.epic-ide.org/)

Steps:

1\. Open Eclipse.

2\. Go to 'Help / Install New Software...'

3\. In the 'Work With' text box, enter: `http://e-p-i-c.sf.net/updates` and press 'Enter' 
* This is called the ‘p2 software repository link

4\. Press Enter.

5\. Select all options that appear.

6\. Follow all on-screen instructions from there.


## Install the Eclipse Python editor
<span style='width: 20px; display:inline-block'>:rotating_light:</span> <span style='width: 20px; display:inline-block'>:frog:</span> <span style='width: 20px; display:inline-block'>:bangbang:</span> <span style='width: 20px; display:inline-block'>:snake:</span>

> For more information, [visit this site](http://pydev.org/download.html )

Steps:

1\. Open Eclipse.

2\. Go to 'Help / Install New Software...'

3\. In the 'Work With' text box, enter: `http://pydev.org/updates` and press 'Enter' 

4\. Press Enter.

5\. Two options will appear: PyDev and PyDev Mylyn Integration.  Check both and click next/next/accept/finish.

6\. Follow all on-screen instructions from there.

### Eclipse Crashing Due to Default Browser Changing

If this happens, PyDev is trying to open the default browser, but something happened to the default browser pointer.

When I upgraded from Ubuntu 12.04 to 14.04, PyDev / Python for Eclipse would cause Eclipse to error out and crash.  After some searching, I found the problem also happened with Eclipse's hovering tooltips – during the upgrade, the default web browser for Eclipse was disrupted, so when Eclipse tried to utilize some HTML / redirection (in the case of PyDev it was their initial splash screen when opening a .py file) it would crash.

To Fix:
1\. Open the eclipse.ini file for editing (the location can vary, but it should be right alongside your eclipse executable).

2\. Go to the last line, and under that line enter this: `-Dorg.eclipse.swt.browser.DefaultType=mozilla`

3\. Save and exit.

4\. The error should now be fixed.

## Installing the Eclipse Maven Add-On

<span style='width: 20px; display:inline-block'>:rotating_light:</span> <span style='width: 20px; display:inline-block'>:frog:</span> <span style='width: 20px; display:inline-block'>:bangbang:</span> <span style='width: 20px; display:inline-block'>:snake:</span>

> For more information, [visit this site](https://java2blog.com/how-to-install-maven-pluginm2eclipse-in/)

Steps:

1\. Open Eclipse.

2\. Go to 'Help / Install New Software...'

3\. In the 'Work With' text box, enter: `http://download.eclipse.org/technology/m2e/releases` and press 'Enter' 

4\. Press Enter.

5\. The option 'Maven integration for Eclipse' option will appear.  Check it and click next/next/accept/finish.

6\. Follow all on-screen instructions from there.



---

# Installing Xming / Setting Up PUTTY for Xming
<span style='width: 20px; display:inline-block'>:frog:</span> <span style='width: 20px; display:inline-block'>:bangbang:</span>

> For more info [visit this site](https://wiki.utdallas.edu/wiki/plugins/servlet/mobile#content/view/6881592)

!> Note that this is for your additional Windows machine, NOT to be installed on Ubuntu. In order for it to work, PUTTY must be installed and configured correctly.

Xming is a program that is installed on Windows and allows visual applications to be launched FROM your Ubuntu machine on your Windows machine.  

Xming can be [downloaded from SourceForge](http://sourceforge.net/projects/xming/), although it would be preferable to download it from elsewhere.

Xming MUST be running if you want PUTTY to support opening visual programs running on your Ubuntu machine via your Windows machine.

## Setting up Xming in PuTTY

Steps:

1\. Click on the connection you want, then click “Load”.

2\. On the left there is a list of categories.  Select “Connection/SSH/X11”

3\. Check “Enable X11 forwarding”

4\. In the “X display location” type: localhost:0

5\. Make sure “Remote X11 authentication protocol” is set to “MIT-Magic-Cookie-1”

6\. Back in the Category listing, select “Session” and then click the “Save” button.

7\. You can now type a program launch command in PUTTY and it will open the visual window in Xming!

---

# Installing R
<span style='width: 20px; display:inline-block'>:bangbang:</span>

!> Only install R if your plan on doing any work in R on the machine.

> For more instructions on how to install R, [please go here](https://cran.r-project.org)

Steps:

1\. Visit the [CRAN r-project mirrors site](https://cran.r-project.org/mirrors.html) and pick a mirror (I picked http://lib.stat.cmu.edu/R/CRAN/)

!> The version can change with the [version of Ubuntu changing](https://en.wikipedia.org/wiki/Ubuntu_version_history); you will have to go to the target mirror and see the versions available. For example, when I first installed R I navigated to http://lib.stat.cmu.edu/R/CRAN/bin/linux/ubuntu and saw that xenial – the latest for Ubuntu 16.04 at the time – was available; now the latest version is bionic-cran35, but that is for R 3.5, so if we wanted R 3.4 we would have to pick either artful or xenial.

2\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root).

3\. Prepare to [Edit the sources.list file or sources.list.d](/operating_systems/ubuntu/linux_notes?id=changing-update-sources-sourceslist).

4\. Add this to sources.list / sources.list.d:
```
## Packages for R
deb http://lib.stat.cmu.edu/R/CRAN/bin/linux/ubuntu xenial/
deb http://lib.stat.cmu.edu/R/CRAN/ trusty-backports main restricted universe
```

!> You will most likely have to switch out the words 'xenial' and 'trusty' above.

> At first I only entered the first 'deb' line - and it installed just fine - but now have both based on the suggestion on the instructional webpage; not sure if its needed.

5\. Save and exit sources.list

6\. You must add specific signed keys to your system; according to the R instructions, run these commands:
```
gpg --keyserver keyserver.ubuntu.com --recv-key E084DAB9
gpg -a --export E084DAB9 | apt-key add -
```

7\. Run an update: `apt-get update`

!> When you run 'apt-get update' you will see errors - this seems to be OK?

8\. now, to install R:
```
apt-get install r-base
apt-get install r-base-dev
apt-get install libatlas3-base
apt-get install littler
apt-get install r-cran-getopt
```
9\. If you want to install python libraries:
```
apt-get install python-rpy
apt-get install python-rpy-doc
apt-get install python-rpy2
```

---  

# Monitor Setup  

## GDM3 - Login Screen Setup  

<span style='width: 20px; display:inline-block'>:bangbang:</span> <span style='width: 20px; display:inline-block'>:frog:</span>  

Modern versions of Ubuntu (as of 2024) use GDM3. Often, on Ubuntu laptops, its advantageous to have a default monitor setup for the initial login screen. If you have an account with the monitors set, you can simply take its associated `monitors.xml` file and move it to the global GDM3 spot.  

If you already have a `monitors.xml` file (for example, in the `USERNAME` username's home directory):  

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)  

2\. Copy the file: `cp /home/USERNAME/.config/monitors.xml /var/lib/gdm3/.config/monitors.xml`  
* You will have to replace `USERNAME` with the actual username.  

3\. Change ownership of the file: `chown gdm:gdm /var/lib/gdm3/.config/monitors.xml`  
