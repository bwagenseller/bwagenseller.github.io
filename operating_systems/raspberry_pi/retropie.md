# RetroPie

# Introduction

RetroPie is an OS for Raspberry Pi that will allow you to play thousands of old SNES, NES, Game Boy, Genesis, Arcade, PS1, Neo Geo, and all types of other games from the past.

# RetroPie Installation

I am not going to fully explain an installation because frankly [the RetroPie GitHub page](https://github.com/RetroPie/RetroPie-Setup/wiki/First-Installation) has one that is pretty much unbeatable. I will fill in some small gaps, though.

## Configuring an Input

To configure a controller (or keyboard), press 'start' on a controller (or 'enter'). Now you will have to map buttons (or keys) to the controller. Note a few things:
* To skip the setup of a specific button (like if you have a classic Genesis or SNES controller and you do not have an analog stick), simply hold down any button for a few seconds.
* The last option is to set the `Hotkey Enable`; holding this button plus other buttons enables emulator shortcuts. **Do NOT assign the 'enter' keyboard button _OR_ the 'start' button of any controller to the Hotkey enable!** If you do you will immediately exit the game when you press enter (or the start button). Its best to either assign the hotkey button to the 'select' button _and/or_ to a random key on the keyboard that has no other function (_definitely_ not 'enter').

See [this section](operating_systems/raspberry_pi/retropie?id=the-hotkey) for more on the `Hotkey` button.

> For any controller you configure, its settings are saved in `/opt/retropie/configs/all/retroarch/autoconfig`
 


## Using dd In Linux

> You will need to [be root](operating_systems/ubuntu/linux_notes?id=becoming-root) to perform these actions.

When initially installing the RetroPie OS to the microSD, you will have to install an image file (to the microSD). The general instructions for installing an image file to a USB drive [can be found on the Raspberry Pi website](https://www.raspberrypi.org/documentation/installation/installing-images/linux.md), but the specific command that installs an image to a mounted drive in Linux is `dd`; the basic format is this:
```
dd bs=4M if=2018-11-13-raspbian-stretch.img of=/dev/sdX status=progress conv=fsync
```
* `bs=4M` means 'block size set to 4 MB'.
* `if=` is the location of the image file; you will download an image file (.img) of RetroPie [here](https://retropie.org.uk/download/) (it will initially be a *.img.gz and you will have to use [gzip](operating_systems/ubuntu/linux_notes?id=gzip-zipping-one-file) to open it.
 * Note that many 'images' exist, not just RetroPie - so keep this knowledge for installing other .img files.
* `of=` where we are installing the image; it will be a mounted drive which will most likely be in `/dev/` and _the device_ (in this case USB) will be something similar to `sdX` (you will have to replace `X`). Note the following:
 * It will probably not end in a number (so if you see sdc and sdc1, use sdc; 'sdc' is the _device_ (which we are interested in), 'sdc1' is a _partition_ on 'sdc' (which we are not interested in and will be overwritten anyway)).
 * **BE VERY CAREFUL** when determining the device
* `status=progress` just displays the status of the command as it iterates through.
 
You may be wondering how you determine the device ('sdX'); the best way is to make sure your USB is **NOT** plugged in and run [lsblk](operating_systems/ubuntu/linux_notes?id=using-lsblk); then plug your USB in a USB slot and run [lsblk](operating_systems/ubuntu/linux_notes?id=using-lsblk) again. Note the device ('name') that appeared _after_ you plugged the USB into the USB slot.

!> If you misidentify the device and use `dd` you can do damage to your computer, so make _sure_ you have it correct!

## Enabling SSH

Once your RetroPie is up and running, you should enable SSH on RetroPie. Just as it is in [Raspbian](operating_systems/raspberry_pi/raspbian?id=enabling-ssh), you will have to enable SSH with `raspi-config`. To do this in RetroPie, go into the settings and look for one that says something like `raspi-config`; from there, [follow the Raspbian instructions](operating_systems/raspberry_pi/raspbian?id=enabling-ssh) on how to enable SSH.

> SSH is a fantastic tool to interact with your RetroPie; with it, you can log in from other computers in your house and run commands, manipulate files, etc. [SSH](operating_systems/ubuntu/linux_notes?id=ssh) can be run on the command line in a Linux environment; for Windows, you can use [WinSCP](https://winscp.net/eng/index.php) to interact with files and [PuTTY](https://putty.org/) to interact with the command line.
> <br><br>
> In order to use SSH, you will need the IP of your RetroPie; to get it, go into the 'RetroPie Configuration' screen and then select 'Show IP'; The IP will be displayed at the top (provided you have [connected to Wifi](operating_systems/raspberry_pi/retropie?id=connecting-to-wifi) or have manually connected an ethernet cable).

## Changing Pi's Password

Once your RetroPie is up and running, you should change the `pi` account's password on RetroPie. You may not be able to change the login `pi` to something else (without alot of work), but you should certainly [change pi's password](operating_systems/ubuntu/linux_notes?id=setting-a-password) is the same default password is used for _all_ RetroPies (and Raspbian OSes, for that matter - the initial password is `raspberry`).

## Connecting To Wifi

To connect to Wifi, go into the 'RetroPie Configuration' screen and then select 'Wifi'-> 'Connect to Wifi network'.

## Expanding the Filesystem

Unfortunately, only a small portion of your microSD card is used by RetroPie initially; you will need to expand it to use the entire card.  To do so, enter the RetroPie Configuration screen, then select 'Raspi-Config'->'Advanced Options'->'Expand Filesystem' to expand the filesystem. You will need to reboot for this to take effect.

## Installing Optional Packages

I recommend installing the optional packages to your RetroPie, as it will enable more emulators on your unit. To do this enter the RetroPie Configuration screen, then select 'RetroPie Setup'->'Manage Packages'->'Manage Optional Packages'->'Update all optional packages from binary'.

## Updating the System

I also recommend updating the system, _provided_ you know how to [use SSH](operating_systems/ubuntu/linux_notes?id=ssh) as you may actually lock yourself out of the RetroPie and you need SSH to get back in. An example of logging in to your RetroPie with SSH (assuming your RetroPie has an IP of 10.0.0.2): `ssh pi@10.0.0.2`. Note you will need to do use SSH from a computer that is not your RetroPie unit.

To update RetroPie, enter the RetroPie Configuration screen, then select 'RetroPie Setup'->'Update packages'.

**<font size="4">If You Cannot Login After the Update</font>**

Unless you are skilled and changed the login, it will be `pi`; and if you did not follow my advice on changing the password, it will be `raspberry`. Sometimes, an update will make you put in a login and password upon starting RetroPie, and for some reason it rejects all attempts (I think it has something to do with not recognizing the login).

I found that to fix this, you will need SSH to log into the unit (so you will need some other computer to use SSH to get into RetroPie). Once you are in, type in `emulationstation` at the prompt; at this point it gets weird. EmulationStation will boot on the RetroPie itself (NOT in your SSH window). Go back to your RetroPie unit and go to the "RetroPie Configuration" screen, then "RetroPie Setup". This will open up a DOS selection screen _back on your SSH terminal_ for some reason. There, pick 'Configuration/Tools' -> 'Autostart' -> 'Start Emulationstation at boot'.

The issue should be resolved at this point.

## Sound Settings

You can change the sound output by selecting `RetroPie` -> `Retropie Setup` -> `Configuration / tools` -> `audiosettings`.

# ROMs (Games)

## Installing ROMs

[Here](https://github.com/RetroPie/RetroPie-Setup/wiki/Transferring-Roms) is a wonderful guide on transferring ROMs, and [here](https://github.com/RetroPie/RetroPie-Setup/wiki/Supported-Systems) is a complete list of systems.  

Once you get some ROMs, you will have to place them in the correct system directories. _All_ system directories are in the folder `/home/pi/RetroPie/roms/`; to date, here are the avaiable folders (and their systems):

|  System   |   Folder   |   File Extension   |  
| --- | --- | --- | 
|  3do   |   3do   |   .iso   |  
|  Amiga   |   amiga   |   .adf .adz .dms .exe .rp9 .zip   |  
|  Amstrad CPC   |   amstradcpc   |   .cpc .dsk   |  
|  Apple II   |   apple2   |   .dsk   |  
|  Atari 2600   |   atari2600   |   .7z .a26 .bin .gz .rom .zip   |  
|  Atari 5200, 8-Bit Series   |   atari800 / atari5200   |   .7z .a52 .atr .bas .bin .car .dcm .xex .xfd .zip .atr.gz .xfd.gz   |  
|  Atari 7800   |   atari7800   |   .7z .a78 .bin .zip   |  
|  Atari Jaguar   |   atarijaguar   |   .j64 .jag   |  
|  Atari Lynx   |   atarilynx   |   .7z .lnx .zip   |  
|  Atari ST, STE, TT, Falcon   |   atarist   |   .ctr .img .ipf .raw .rom .st .stx   |  
|  CoCo   |   coco   |   .asc .bas .cas .ccc .dmk .dsk .jvc .os9 .rom .sna .vdk .wav   |  
|  Colecovision   |   coleco   |   .bin .col .rom .zip   |  
|  Commodore 64, VIC-20, PET   |   c64   |   .crt .d64 .g64 .t64 .tap .x64   |  
|  Daphne   |   daphne   |   .daphne   |  
|  Dragon 32   |   dragon32   |   .asc .bas .cas .ccc .dmk .dsk .jvc .os9 .rom .sna .vdk .wav   |  
|  Dreamcast   |   dreamcast   |   .cdi .gdi   |  
|  Famicom Disk System   |   fds   |   .7z .fds .nes .zip   |  
|  FinalBurn Alpha   |   arcade / fba / Neo Geo   |   .7z .zip   |  
|  Game & Watch   |   gameandwatch   |   .mgw   |  
|  Game Boy   |   gb   |   .7z .gb .zip   |  
|  Game Boy Advance   |   gba   |   .7z .gba .zip   |  
|  Game Boy Color   |   gbc   |   .7z .gbc .zip   |  
|  Game Gear   |   gamegear   |   .7z .bin .gg .sms .zip   |  
|  GameCube   |   gc   |   .iso   |  
|  Intellivision   |   intellivision   |   .bin .int   |  
|  Macintosh   |   macintosh   |   .img .rom   |  
|  Master System   |   mastersystem   |   .7z .bin .sms .zip   |  
|  Mega Drive / Genesis   |   megadrive   |   .7z .bin .gen .md .sg .smd .zip   |  
|  Mega-CD / Sega CD   |   segacd   |   .bin .chd .cue .iso   |  
|  MSX   |   msx   |   .col .dsk .mx1 .mx2 .rom   |  
|  Multiple Arcade Machine Emulators (MAME)   |   arcade / mame-mame4all / mame-libretro / mame-advmame   |   .zip   |  
|  Neo Geo   |   Neo Geo   |   .7z .zip   |  
|  Neo Geo Pocket   |   ngp   |   .7z .ngp .zip   |  
|  Neo Geo Pocket Color   |   ngpc   |   .7z .ngc .zip   |  
|  Nintendo 64   |   n64   |   .n64 .v64 .z64 .zip   |  
|  Nintendo DS   |   nds   |   .bin .nds   |  
|  Nintendo Entertainment System   |   nes   |   .7z .fds .fig .mgd .nes .sfc .smc .swc .zip   |  
|  Oric/Atmos   |   oric   |   .dsk .tap   |  
|  PC   |   pc   |   .bat .com .exe .sh   |  
|  PC Engine / TurboGrafx-16   |   pcengine   |   .7z .ccd .chd .cue .pce .zip   |  
|  PC Engine 2 / SuperGrafx   |   pcengine   |   .7z .pce .zip   |  
|  PC-8800   |   pc88   |   .88d .cmt .d88 .t88   |  
|  PC-9800   |   pc98   |   .d88 .d98 .88d .98d .fdi .xdf .hdm .dup .2hd .tfd .hdi .thd .nhd .hdd   |  
|  PlayStation 1   |   psx   |   .cbn .cue .img .iso .m3u .mdf .pbp .toc .z .znx   |  
|  PlayStation 2   |   ps2   |   .iso .img .bin .mdf .z .z2 .bz2 .dump .cso .ima .gz   |  
|  Pokemon Mini   |   pokemini   |   .min   |  
|  PSP   |   psp   |   .cso .iso .pbp   |  
|  ResidualVM   |   residualvm   |   See Wiki Page   |  
|  SAM Coupé   |   samcoupe   |   .dsk .mgt .sbt .sad   |  
|  Saturn   |   saturn   |   .bin .cue .iso .mdf   |  
|  ScummVM   |   scummvm   |   See Wiki Page   |  
|  Sega 32X   |   sega32x   |   .32x .7z .bin .md .smd .zip   |  
|  Sega SG-1000   |   sg-1000   |   .7z .bin .sg .zip   |  
|  Sharp X1   |   x1   |   .dx1 .zip .2d .2hd .tfd .d88 .88d .hdm .xdf .dup .cmd   |  
|  Sharp X68000   |   x68000   |   .dim   |  
|  Super Nintendo Entertainment System   |   snes   |   .7z .bin .fig .mgd .sfc .smc .swc .zip   |  
|  TI-99/4A   |   ti99   |   .ctg   |  
|  TRS-80   |   trs-80   |   .dsk   |  
|  Vectrex   |   vectrex   |   .7z .bin .gam .vec .zip   |  
|  Videopac / Odyssey2   |   videopac   |   .bin   |  
|  Virtual Boy   |   virtualboy   |   .7z .vb .zip   |  
|  Wii   |   wii   |   .iso   |  
|  WonderSwan   |   wonderswan   |   .7z .ws .zip   |  
|  WonderSwan Color   |   wonderswancolor   |   .7z .wsc .zip   |  
|  ZX Spectrum   |   zxspectrum   |   .dsk .gz .img .mgt .scl .sna .szx .tap .trd .tzx .udi .z80   |  
|  ZX-81   |   zx81   |   .p .tzx .t81   |  
* For more information, see the official [RetroPie Supported Systems page](https://github.com/RetroPie/RetroPie-Setup/wiki/Supported-Systems) on GitHub.

Use SSH (or another method) to deliver the ROMs. You will have to restart RetroPie once you install ROMs.

> After each time you load a ROM you will have to re-start EmulationStation for it to be visible; you can do this by performing a hard reset of the system _or_ by getting to the 'Main Menu' of EmulationStation (usually by pressing the `start` button) and then selecting 'Quit'->'Restart EmulationStation'->'Yes'.

## ROMs and Simlinks

Its possible to simply create an additional folder on your RetroPi, save all of your games there, and use [symlinks](operating_systems/ubuntu/linux_notes?id=file-shortcuts-symlinks) that point to your ROMs but are located [in the approved ROM folders](operating_systems/raspberry_pi/retropie?id=installing-roms). The advantage of this is: if you want to keep all of your ROMs on the RetroPi, but you have many of them and they are crowding the game selection screen(s) - _or_ if you want to rename the game name in the game selection screen - you can accomplish this with a symlink (and since symlinks are small in size, they do not take up much space at all). Please note that you will have to re-start EmulationStation after every time you make (or remove) symlinks in the approved ROM folders.

An example of making a symlink would be this:
```
ls -s /actualROMs/snes/somegame.smc /home/pi/RetroPie/roms/snes/someOtherName.smc
```
* The above creates a symlink.
* '/actualROMs/snes/somegame.smc' is the location of the _actual_ file you have stored on your RetroPie.
* '/home/pi/RetroPie/roms/snes/' is the [RetroPie SNES folder](operating_systems/raspberry_pi/retropie?id=installing-roms) (where we must save the symlink).
* 'someOtherName.smc' is the new game name that will appear in the SNES game selection screen.

## Configuring Emulators

Some emulators (like for Game Boy Advance and Playstation) need you to do some things for the BIOS; to do that, [see here](https://weekendengineer.wordpress.com/2014/09/05/retropie-adding-game-roms-bios-files-and-enabling-save-states/).

## Symbols in ROMs

The development community uses symbols in the ifle name to denote not only the name of the ROM but attributes as well; here are some of those attributes:

| Attribute | Meaning |
| --- | --- |
| (U) | US Rom |
| (E) | European Rom |
| (J) | Japanese Rom |
| (JU) | USA / Japanese Rom |
| (JE) | European / Japanese Rom |
| (JUE) | European / USA / Japanese Rom |
| (G) | German Rom |
| (vX.X) | Version of the game itself (sometimes, the original games had slightly different versions) |
| (!) | Verified good ROM (should work fine) |
| \[a*] | Alternative Version |
| \[b*] | Bad Dump (probably broken somewhere) |
| \[f*] | Fixed to run better on copiers or emulators |
| \[h*] | Hack |
| \[o*] | OverDump (sometimes bad, sometimes good) |
| \[m*] | Multilanguage |  
* the asterisks indicate it can be followed by a number; so for example, \[a2] means its the second alternative version.

!> For more info on these codes, [see the wordpress on 64bitorless](https://64bitorless.wordpress.com/rom-suffix-explanations/).

## Location of ROMs

This is a tricky subject, as its usually illegal to own a ROM unless you have the game. IF you have the game you are legally allowed to download the ROM.

There is the website freeroms.com, but I highly suspect they load their ROMs with viruses, so download at your own peril.

Most people these days use torrent applicatins to download ROMs; the only one I know of is www.SpaTorrent.com.

Finally, someone actually has a microSD card for sale on Amazon with all kinds of loaded ROMs - you can get either [ComancheComputing's RetroPie 128 gb Micro SD card with 10,000 games](https://www.amazon.com/Retropie-128GB-Games-Plus-Raspberry/dp/B07JNVLY1N/ref=sr_1_fkmrnull_2?keywords=ComancheComputing+RetroPie&qid=1554738440&s=gateway&sr=8-2-fkmrnull) or [this one](https://www.amazon.com/Retropie-32GB-MicroSDHC-Class10-Raspberry/dp/B07HQV6MW5/ref=sr_1_3?keywords=RetroPie+32GB+MicroSDHC&qid=1554738816&s=gateway&sr=8-3) which is a bit cheaper (with less games).

## Selecting An Emulator

When you initially select a ROM to run, you will see a brief screen that shows you the ROM being run and the emulator that will run it; it is at this point that you have the option to modify the emulator selection (the available native systems can be found [here](https://github.com/RetroPie/RetroPie-Setup/wiki/Supported-Systems)). For most systems your best bet is to just use the default emulator; the exception to this are the [MAME (arcade) games](operating_systems/raspberry_pi/retropie?id=loading-mame-roms), where you will almost surely have to change the emulator occasionally.

## The Hotkey

Pressing the `Hotkey` button plus another button has a few uses:

| Combination | Description |
| --- | --- | 
| Hotkey+Start | Exit emulator (which is why yo udo not assign `Hotkey` to 'start') |  
| Hotkey+Right shoulder | Save state |  
| Hotkey+Left shoulder | Load saved state |  
| Hotkey+Left | Decrease current saved state slot number |  
| Hotkey+Right | Increase current saved state slot number |  
| Hotkey+X | Quick Menu |  
| Hotkey+B | Reset Game | 

# Arcade Games

ROMs for arcade games act differently than other ROMs (for SNES, NES, Genesis, etc).

## Types of Arcade Emulators

There are two basic types of arcade emulators: [MAME (Multiple Arcade Machine Emulator)](https://en.m.wikipedia.org/wiki/MAME) and [FinalBurn Alpha (Neo Geo)](https://www.fbalpha.com/); under each 'family' there are other branches

As for MAME, the [versions](https://www.mamedev.org/oldrel.html) can be a bit confusing as the numbers seem to get smaller (it goes from .99 to .100); they do not act like regular numbers and the zeros _do_ count (so .100 does indeed come after .99).

Some of these emulators do not come matively with RetroPie, but you can easily [install them when installing all optional packages](operating_systems/raspberry_pi/retropie?id=installing-optional-packages).

The current emulators for arcade are:
* [MAME](https://en.m.wikipedia.org/wiki/MAME)
 * Notes
   * I generally like MAME over FBA/Neo Geo as you can easily change the controls for each game (whereas its a production to do so for FBA/Neo Geo).
     * To do this, simply press `Tab` (or set up [the same fonctionality for controls in Retro-Arch Config](operating_systems/raspberry_pi/retropie?id=controls-in-mame)).
 * MAME4ALL
   * `mame4all`
   * Uses a very old version of MAME (MAME 0.37b5, circa 2000)
   * Optimized for great performance (at the cost of less compatibility with ROMs due to its age); 
     * This emulator is more suitable for Raspberry Pi models 1 and 2; its rare that a Pi model 3 would need to use this emulator but its possible.
 * [Libretro](https://www.libretro.com/)
   * `lr-mame2000`
     * MAME Version: 0.37b5 (July 2000)
	 * I rarely use this version.
   * `lr-mame2003`
     * MAME Version: MAME 0.78 (25 Dec 2003)
     * I like using this emulator above all others, if possible. It has a good mix of speed and compatibility.
   * `lr-mame2010`
     * MAME Version: 0.139 (August 2010)
	 * More compatibility with games but a bit slower than mame2003.
	 * Changing Retroarch controls (via the `Tab` button) seem to be mostly broken; a short list, but not exhaustive, is:
	   * It's impossible to assign buttons to the second (or 3rd or 4th) controller.
	     * The second/third/fourth controllers do have buttons mapped, but they appear as 'n/a' and you cannot set them.
		 * You can assign different buttons via the core method, but you can only assign 4 buttons at most to multiplayer controllers.
	   * It's impossible to assign many of the keyboard keys to buttons; the the most egregious is you cannot map `Esc` to the 'UI Cancel' action.
 * [Advance Projects (AdvanceMame)](https://www.advancemame.it/)
   * Notes
     * This plays games with advanced video support for visuals.
	 * They are built upon older MAME releases.
   * `advmame-0.94`
     * MAME Version: MAME 0.94 (March 2005)
   * `advmame-1.4`
     * MAME Version: MAME 0.106 (May 2006)
   * `advmame`
     * MAME Version: MAME 0.106 (May 2006)
* [FinalBurn Alpha (Neo Geo)](https://www.fbalpha.com/)
 * Notes
   * I generally avoid FBA if possible, as its not possible to easily easily change the controls for each game.
   * That said, there are many, many games that will _only_ run in FBA.
 * `lr-fbalpha`
   * FB Alpha Version: v0.2.97.43
   * This is the recommended emulator for FBA for Raspberry Pi models 2 and 3.
 * `lr-fbalpha2012`
   * FB Alpha Version: v0.2.97.30
 * `lr-fbalpha-neocd`
   * I believe this is used for Neo Geo's 'Neo CD'
   * I have yet to find ROMs for this platform
 * `gngeopi`
   * This is actually the Neo Geo console, but it can run some things.
   * It actually uses MAME Version 0.138 (May 2010)
   * The caveat to using this emulator is they **MUST** use a ROM listed in `/opt/retropie/emulators/gngeopi/share/gngeo/gngeo_data.zip` (these have the extension .drv)
     * This may not use .zip files.
	 * As a consequence, I rarely use this emulator.
 * `pifba`
   * This is an ancient version of Neo Geo.
   * This should really only be used on Raspberry Pi model 1 (models 2/3 can use more advanced emulators).

 

## Loading Arcade ROMs

When installing arcade ROMs, you have two choices: Put the ROMs in the `arcade` folder _or_ the folder that is best suited to run them (which are `mame-mame4all`, `mame-libretro`, `mame-advmame`, or `fba`); see [here](operating_systems/raspberry_pi/retropie?id=installing-roms) for more about folders. The advantage of using the `arcade` folder over the individual folders is all of your games are in one place; the disadvantage is there are roughly a dozen emulators to choose from and one does not rule them all, so you will have to [keep selecting a different emulator](operating_systems/raspberry_pi/retropie?id=selecting-an-emulator) until you find one that works (i.e. you do not get dumped back to the ROM selection screen and there are no other major problems).

The indidual folders just group the _type_ of arcade ROMs, with the major groupings as follows: MAME4ALL, LIBRETRO, AdvancedMAME, and FinalBurnAlpha (Neo Geo). The advantage of using the individual folders is you are more likely to not have to switch the emulator; the catch is there is still a chance you will have to do so (especially with LIBRETRO and AdvancedMAME arcade games). The disadvantage is its not straightforward - at all - in regards to _which_ emulator will work best, and there is a decent chance that if you picked an emulator family blindly, you are probably wrong. In addition, when using the individual folders, EmulationStation groups them individually in the game selection screen (so all of your arcade games will not be in the same place).

For each rom you play in the different folders, the file `/opt/retropie/configs/all/emulators.cfg` is modified to mark the emulator you used. The format is `folder_zipFile(without the extension) = "emulator"` Here is an example of the contents of this file:
```
mame-mame4all_ddragonu = "mame4all"
mame-mame4all_xmen6p = "lr-mame2000"
arcade_captaven = "lr-mame2010"
arcade_1942b = "lr-mame2003"
arcade_ghoulsu = "mame4all"
```
* The first entry is for the zip file `ddragonu` (without the extension) in the `mame-mame4all` folder; the emulator used is `mame4all`.
* The second entry is for the zip file `xmen6p` (without the extension) in the `mame-mame4all` folder; the emulator used is `lr-mame2000`.
* The fourth entry is for the zip file `1942b` (without the extension) in the `arcade` folder; the emulator used is `lr-mame2003`.
* The last entry is for the zip file `ghoulsu` (without the extension) in the `arcade` folder; the emulator used is `mame4all`.

See [here](https://github.com/RetroPie/RetroPie-Setup/wiki/MAME) for the main MAME page, [here](https://docs.google.com/spreadsheets/d/1antILt7D12EWOFzyJwTfB86NceghMJKXG7CdYumuHec/edit#gid=0) for a massive listing of ROMs and compatibility. There are individual emulators (and compatible ROMs) [on the RetroPie MAME page](https://github.com/RetroPie/RetroPie-Setup/wiki/MAME) (here is an [example of ROMs that are compatible with MAME4ALL](https://docs.google.com/spreadsheets/d/1SHspjyHavY9-PKbO2swDr52BS2Wl_mB_Vjx2Z1SXiD8/edit#gid=828747670) if you wish to view them individually (the individual lists are actually more complete).

**<font size="4">Arcade ROM Dependencies</font>**

> This does not seem to be true for consoles - only arcade ROMs.

To run the base game, the only file required is the .zip file in some cases; in most cases, however, other .zip files are needed. For example, if a game is an offshoot of another version (in some cases the US version is considered an offshoot from the Japanese version or vice versa), one is considered to be the _parent_ and the other the _child_; in these cases, you can run the parent as a standalone but if you wish to run the child you **must** have both .zip files. The majority of ROMs are child ROMs.

In other cases, you need a _BIOS_ file (usually a .zip file) to run the game; you just put the BIOS file in the same folder as your ROM.  
* The third entry is for the zip file `captaven` (without the extension) in the `arcade` folder; the emulator used is `lr-mame2010`.

Finally, in some cases a ROM will need to _sample_ things from another ROM (usually its sound); in these cases the sampled ROM is needed as well (put it in the folder with the ROM you wish to play).  

In some cases, it needs a parent, a BIOS, _and_ a sample file in addition to the ROM - or some other combination.  If this combination is not present, **the game will not run**.

Fortunately, these relationships have been mostly worked out - [here](https://docs.google.com/spreadsheets/d/1antILt7D12EWOFzyJwTfB86NceghMJKXG7CdYumuHec/edit#gid=0) is a massive listing of ROMs and their parent/BIOS/sample dependencies. There are individual emulator dependencies in the individual compatibility lists [on the RetroPie MAME page](https://github.com/RetroPie/RetroPie-Setup/wiki/MAME) (here is an [example of ROM dependencies on the MAME4ALL compatibility list](https://docs.google.com/spreadsheets/d/1SHspjyHavY9-PKbO2swDr52BS2Wl_mB_Vjx2Z1SXiD8/edit#gid=828747670)). The individual listings have dependencies that are not listed on the massive listing, so you may want to check everything out if a ROM is not working.


## Picking an Emulator

Recall that there are [12 different emulators for RetroPie](operating_systems/raspberry_pi/retropie?id=types-of-arcade-emulators):
* [MAME](https://en.m.wikipedia.org/wiki/MAME)
 * MAME4ALL
   * `mame4all`
 * [Libretro](https://www.libretro.com/) - a version of MAME.
   * `lr-mame2000`
   * `lr-mame2003`
   * `lr-mame2010`
 * [Advance Projects (AdvanceMame)](https://www.advancemame.it/)
   * `advmame-0.94`
   * `advmame-1.4`
   * `advmame`
* [FinalBurn Alpha (Neo Geo)](https://www.fbalpha.com/)
 * `lr-fbalpha`
 * `lr-fbalpha2012`
 * `lr-fbalpha-neocd`
 * `gngeopi`
 * `pifba`


Unfortunately, specific ROMs are only compatible with a few emulators at most. Fortunately there are a few websites that have pioneered this, at least in part: 
[here](https://docs.google.com/spreadsheets/d/1antILt7D12EWOFzyJwTfB86NceghMJKXG7CdYumuHec/edit#gid=0) is a massive listing of ROMs and compatibility. There are individual emulators (and compatible ROMs) are [listed on the RetroPie MAME page](https://github.com/RetroPie/RetroPie-Setup/wiki/MAME) (here is an [example of ROMs that are compatible with MAME4ALL](https://docs.google.com/spreadsheets/d/1SHspjyHavY9-PKbO2swDr52BS2Wl_mB_Vjx2Z1SXiD8/edit#gid=828747670) if you wish to view them individually (the individual lists are actually more complete),  

In addition, [this](https://docs.google.com/spreadsheets/d/1F5tBguhRxpj1AQcnDWF6AVSx4av_Gm3cDQedQB7IECk/edit#gid=1168974718) is also a decent reference as well (although its not for RetroPie, it should mostly apply).  


If games can run in multiple emulators, here is my order preference (when speed is an issue):  
1\. `lr-mame2003` - I like `lr-mame2003` as it seems to be able to play most games.  
2\. `mame4all` - I like `mame4all` simply because you can re-configure controls for it. It is also _highly_ optimized, so its very fast (although its the oldest emulator in the list).  
3\. `lr-fbalpha` - FBA was Neo Geo, which made many of these arcade games; as such its compatible with many ROMs. I shy away from it though when possible, as you cannot easily reconfigure the controls.  
4\. `advmame` - Pretty much on par with lr-fbalpha, with the caveat you can assign a 'pause' button and you cn reconfigure the controls. The downside to advmame is its fairly resource intensive.  
5\. `advmame-1.4`  
6\. `lr-mame2010` - lr-mame2010 would be much higher on the list, and it should be just under lr-mame2003 (as mame2010 can be slower but it usually supports a bit more games), but changing Retroarch controls (via the `Tab` button) seem to be mostly broken (a short list, but not exhaustive, is you can't assign buttons to the second controller, you cant assign many of the keyboard keys (including `Esc`) to buttons, etc). 
7\. `advmame-0.94`  
8\. The rest, although I try to stay away from `pifba` as its pretty limited (also `gngeopi` is limited and `lr-fbalpha-neocd` is an oddball choice).   


If games can run in multiple emulators, here is my order preference (when speed is _not_ an issue):  
1\. `advmame`  
2\. `lr-mame2003`  
3\. `lr-mame2010`  
4\. `lr-fbalpha`  
5\. `mame4all`  
6\. The rest.  


## Controls in MAME

!> Note this does _NOT_ apply for the Neo Geo emulators!

Unfortunately, the buttons on each arcade game are different for each ROM (unlike a console, arcade games vary) and you will have to set them initially. To do so, start the game, and once the game is launched enter the Retro-Arch config by pressing the `Tab` button. Once there, you can configure the setup for each controller (or player). Note that you will need a keyboard for this, as you will need access to a `Tab` button and an `Esc` button (which exits the Retro-Arch config). Its possible to set up the controllers so that once you do, you will never need the keyboard (for that ROM) again.

To map the controller buttons, press the `Tab` button, then select 'Input (this game)' (you can select it with a controller, but you will have to press `Enter` on the keyboard). Here you can map all actions to your controller. It is recommended that you assign a key on your keyboard _and_ a controller for each major action (like start and coin insertion) for each player.

To assign a mapping, select the action from the listing, then press `Enter`; you are now in assign mode. Press the desired key on the keyboard and wait 3 seconds. Now, press `Enter` again and press the desired button on the controller that will trigger this action. Wait 3 seconds. Now you can set other buttons. It is worth noting that you are required to wait a few seconds after you assign a button to an action, as if you try to go too fast you will assign multiple buttons to the function (usually you accidentally assign the `up` and `down` arrow keys unintentionally). In addition, you have to assign all buttons in one shot - you cannot leave one action and come back to it later and assign more. If you assign a key to an action, and later you want to assign a key _and_ a controller button to that action, you have to re-apply the key _and_ the controller button to the action. Every time you leave an action and come back (and want to assign more buttons to that action) you _must_ re-enter _all_ buttons for that action.

It is suggested that you map the 'coin' action to the `select` button on your controller (and the 'start' action to the `start` button on your controller. Most games still simulate quarters being put in the machine, so you will need some way of doing this on the controller. This will have to be done for each player.

Once all actions are mapped to a button, pressing `Esc` will get you back to the game (just don't repeatedly press `Esc` as pressing this with no config screen displayed can exit the game).

> Its possible to map a combination of button presses to an action; this can be helpful at times. For example, MAME4ALL offers a 'pause' button, but it may be the case where all of your controller buttons are used. In this case, when you assign an action you can press _both_ desired buttons at the same time, which will assign that combination to the action (so in the case of pause, if you assign `Select`+`A` on your controller to 'pause', pressing `Select`+`A` will pause the game).

If you ever get stuck, here are some common controls on the keyboard to do certain things:

| Action | Keyboard Button |
| --- | --- |
| p1 start | Left Alt |
| p2 start | a |
| p3 start | ? |
| p4 start | Right Shift |
| p1 quarter insert | 5 |
| p2 quarter insert | 6 |
| p3 quarter insert | 7 |
| p4 quarter insert | 8 |


There are also controls to interact with the emulator. The important ones are:

| Action | Keyboard Button |
| --- | --- |
| Retro-Arch Config | `Tab` | 
| Quit | Esc |
| Quit (Alternate) | Ctrl+Esc |
| Settings | F2 |
| Reset Game | F3 |

Each emulator has a config file for general controls; for example, MAME4ALL's config file is `/opt/retropie/configs/mame-mame4all/cfg/default.cfg`; if you ever do anything that will cripple the controls (like deleting the mapping of the `Tab` button) you can simply delete the emulator's config file and it will be recreated as the default config file (although I suggest backing the config file up instead of just deleting it).

**<font size="4">My General Controls</font>**

I have a specific setup I use for general controls; these are:

| Action | Keyboard Button | Conroller Button | Notes |  
| --- | --- | --- |  --- |  
| Retro-Arch Config | `Tab` | `Select`+`Down` | Gets into the Retroarch config screen. | 
| UI Select | `Return` | `A` | Will make selections in the UI (i.e. the screen where you map actions to buttons). It is recommended that you set the `Enter` key to this in addition to the 'A' button on your controller. |  
| UI Cancel | `Esc` | `Select`+`Up` | Will escape out of the current UI screen. Pressing this combination will close the UI screen, or if it is closed, it will exit the game. It is recommended that you set the `Esc` key to this in addition to the combination `Select`+`Up` on your controller. |  
| UI Pause | `P` | `Select`+`A` | Will pause the game. It is suggested you pick some combination of buttons for this (like `Select`+`A`, for example) as well as the 'P' button on the keyboard. |  
| Coin | 5 through 8 | `Select` | Simulates putting a coin in for the player. |
| Player Start | 1 through 4 | `Start`  |  'Start' for the player. |  


**<font size="4">MAME4ALL Specific Controls</font>**

There are a few 'general' buttons you should set for the MAME4ALL emulator. To get into these settings, get the game started and then press the `Tab` button; once there, select 'Input (general)' (you will have to press the `Enter` button to select this). 


!> If using `mame-mame4all` and you make a mistake (like deleting the `Tab` button), wipe out the config file by deleting the file `/opt/retropie/configs/mame-mame4all/cfg/default.cfg`. In addition, each individual game has a config file in `/opt/retropie/configs/mame-mame4all/cfg`. On rare occasion a game will stop working, and deleting its config file may get it to work again.

**<font size="4">FinalBurnAlpha (FBA) Specific Controls</font>**

Unfortunately, FBA is _NOT_ a MAME emulator, so pressing `Tab` will do nothing; in addition, most of the controls are pre-mapped (although they can be changed).

That said, [the hotkey](operating_systems/raspberry_pi/retropie?id=the-hotkey) still works; if you need to pause a FBA game you can just press `hotkey`+`X` which will pause any game.

# Controller Setup

Usually, controller setup is straightforward; however, there are a few that must be setup a bit differently.

## Matricom G-Pad XYBA Controller

!> In order to get this to work, you must be able to [use SSH](operating_systems/ubuntu/linux_notes?id=ssh) to log into the RetroPie unit; you will have to either have the [WiFi connected](operating_systems/raspberry_pi/retropie?id=connecting-to-wifi) or have ethernet hardwired in. Once either is configured, you will need to get the IP via `RetroPie` -> `Show IP`. You will also need the login (almost always `pi`) and the password (which you can avoid if you [set up the SSH key](operating_systems/ubuntu/linux_notes?id=ssh-key-setup), but if not, the default password is `raspberry` if you did not change it). Once you have the login, the password, and the IP, you can use SSH to get into the unit.

The [Matricom G-Pad XYBA Controller](https://www.amazon.com/gp/product/B01EO5QPN2/ref=ppx_yo_dt_b_asin_title_o00_s00?ie=UTF8&psc=1) is a cheap wireless controller that works with RetroPie; that said, the setup can be a bit odd. Here are the steps (note this cannot be the first controller you set up):

1\. On the controller, hold the `home` and `X` buttons until the Android LED blinks red.

2\. Pair the controller in RetroPie by selecting `Retropie`->`Bluetooth`->`Register and Connect to Bluetooth Device`. The name of the device is `Gamepad`.

3\. Make sure `8bitdo mapping hack` is turned OFF - go to `Retropie`->`Retropie Setup`->`Configuration / Tools`->`bluetooth`; this setting is in this list.

4\. Setup udev rule for `Gamepad` - go to `Retropie`->`Retropie Setup`->`Configuration / Tools`->`Set up udev rule for Joypad (required for joypads from 8Bitdo etc)` and select `Gamepad`.

5\. [Use SSH](operating_systems/ubuntu/linux_notes?id=ssh) to log into the unit (you will need the login (usually `pi` and IP, and possbily the password (if you did not [set up the SSH key](operating_systems/ubuntu/linux_notes?id=ssh-key-setup)).

6\. Step 4 will create the file `/opt/retropie/configs/all/retroarch/autoconfig/Gamepad.cfg`; we are going to completely reconfigure this file. Now that we are connected via SSH, open that file to edit, delete everything, and paste one of the configurations in. 

**PLEASE NOTE** that there are _two_ versions of this controller that you can get from ordering this controller: one called 'Gamepad' and one called 'ZhiXu Tech. GamePad'. To absolutely know which one you have, try 'adding' a controller to RetroPie via the usual method (on another controller, press 'start', then 'configure input' - then on the alleged 'Gamepad' controller press and hold any button; either 'Gamepad' **OR** 'ZhiXu Tech. GamePad' will briefly appear on the screen before the setup screen appears).

**IF** you have 'ZhiXu Tech. GamePad', you will have to modify two spots in these directions:
* Change `input_device = "Gamepad"` to `input_device = "ZhiXu Tech. GamePad"`.
* Change the file name from `Gamepad.cfg` to `"ZhiXu Tech. GamePad.cfg"`.


If you want the defualt, use this:
```
input_device = "Gamepad"
input_driver = "udev"
input_start_btn = "14"
input_exit_emulator_btn = "14"
input_select_btn = "13"
input_enable_hotkey_btn = "13"
input_a_btn = "4"
input_b_btn = "3"
input_x_btn = "7"
input_y_btn = "6"
input_l_btn = "9"
input_r_btn = "10"
input_l2_btn = "11"
input_r2_btn = "12"
input_l3_btn = "16"
input_r3_btn = "17"
input_up_btn = "h0up"
input_down_btn = "h0down"
input_left_btn = "h0left"
input_right_btn = "h0right"
input_l_x_minus_axis = "-0"
input_l_x_plus_axis = "+0"
input_l_y_plus_axis = "+1"
input_l_y_minus_axis = "-1"
input_r_x_minus_axis = "-2"
input_r_x_plus_axis = "+2"
input_r_y_plus_axis = "+3"
input_r_y_minus_axis = "-3"
input_reset_btn = "3"
input_load_state_btn = "9"
input_save_state_btn = "10"
input_menu_toggle_btn = "7"
input_state_slot_increase_btn = "h0right"
input_state_slot_decrease_btn = "h0left"
```

I personally like the below better - Note the use of  \*\_axis in addition to the use of \*\_btn below! **ALSO NOTE** this was rendered invalid as of the late May 2019 update (the update that removed lr-fbalpha and started using lr-fbneo), but it seems to be **back as the standard** as of November 2019: 
```
input_device = "Gamepad"
input_driver = "udev"
input_start_btn = "14"
input_exit_emulator_btn = "14"
input_select_btn = "13"
input_enable_hotkey_btn = "13"
input_a_btn = "4"
input_b_btn = "3"
input_x_btn = "7"
input_y_btn = "6"
input_l_btn = "9"
input_r_btn = "10"
input_l2_btn = "11"
input_r2_btn = "12"
input_l3_btn = "16"
input_r3_btn = "17"
input_up_btn = "h0up"
input_down_btn = "h0down"
input_left_btn = "h0left"
input_right_btn = "h0right"
input_up_axis = "-1"
input_down_axis = "+1"
input_left_axis = "-0"
input_right_axis = "+0"
input_r_x_minus_axis = "-2"
input_r_x_plus_axis = "+2"
input_r_y_plus_axis = "+3"
input_r_y_minus_axis = "-3"
input_reset_btn = "3"
input_load_state_btn = "9"
input_save_state_btn = "10"
input_menu_toggle_btn = "7"
input_state_slot_increase_axis = "+0"
input_state_slot_decrease_axis = "-0"
```

This is the setup that was valid from May 2019 to possibly November 2019, but post November 2019 **this is no longer valid - use the above configuration instead**.  I am leaving this here for historical purposes, especially if this changes yet again:
```
input_device = "Gamepad"
input_driver = "udev"
input_start_btn = "11"
input_exit_emulator_btn = "11"
input_select_btn = "10"
input_enable_hotkey_btn = "10"
input_a_btn = "1"
input_b_btn = "0"
input_x_btn = "4"
input_y_btn = "3"
input_l_btn = "6"
input_r_btn = "7"
input_l2_btn = "8"
input_r2_btn = "9"
input_l3_btn = "13"
input_r3_btn = "14"
input_up_axis = "-1"
input_down_axis = "+1"
input_left_axis = "-0"
input_right_axis = "+0"
input_up_btn = "h0up"
input_down_btn = "h0down"
input_left_btn = "h0left"
input_right_btn = "h0right"
input_l_y_minus_axis = "-1"
input_l_y_plus_axis = "+1"
input_l_x_minus_axis = "-0"
input_l_x_plus_axis = "+0"
input_r_y_minus_axis = "-3"
input_r_y_plus_axis = "+3"
input_r_x_minus_axis = "-2"
input_r_x_plus_axis = "+2"
input_reset_btn = "0"
input_load_state_btn = "6"
input_save_state_btn = "7"
input_menu_toggle_btn = "4"
input_state_slot_increase_axis = "+0"
input_state_slot_decrease_axis = "-0"
```

7\. Save and exit.

8\. Re-boot the system. The controller should now work!

!> The above works for a Raspberry Pi 3+, RetroPie version 4.1.5+.
