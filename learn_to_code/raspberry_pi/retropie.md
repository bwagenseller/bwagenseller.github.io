# RetroPie

# Introduction

RetroPie is an OS for Raspberry Pi that will allow you to play thousands of old SNES, NES, Game Boy, Genesis, Arcade, PS1, NeoGeo, and all types of other games from the past.

# RetroPie Installation

I am not going to fully explain an installation because frankly [the RetroPie GitHub page](https://github.com/RetroPie/RetroPie-Setup/wiki/First-Installation) has one that is pretty much unbeatable. I will fill in some small gaps, though.

## Using dd In Linux

> You will need to [be root](ubuntu/linux_notes?id=becoming-root) to perform these actions.

When initially installing the RetroPie OS to the microSD, you will have to install an image file (to the microSD). The general instructions for installing an image file to a USB drive [can be found on the Raspberry Pi website](https://www.raspberrypi.org/documentation/installation/installing-images/linux.md), but the specific command that installs an image to a mounted drive in Linux is `dd`; the basic format is this:
```
dd bs=4M if=2018-11-13-raspbian-stretch.img of=/dev/sdX status=progress conv=fsync
```
* `bs=4M` means 'block size set to 4 MB'.
* `if=` is the location of the image file; you will download an image file (.img) of RetroPie [here](https://retropie.org.uk/download/) (it will initially be a *.img.gz and you will have to use [gzip](ubuntu/linux_notes?id=gzip-zipping-one-file) to open it.
 * Note that many 'images' exist, not just RetroPie - so keep this knowledge for installing other .img files.
* `of=` where we are installing the image; it will be a mounted drive which will most likely be in `/dev/` and _the device_ (in this case USB) will be something similar to `sdX` (you will have to replace `X`). Note the following:
 * It will probably not end in a number (so if you see sdc and sdc1, use sdc; 'sdc' is the _device_ (which we are interested in), 'sdc1' is a _partition_ on 'sdc' (which we are not interested in and will be overwritten anyway)).
 * **BE VERY CAREFUL** when determining the device
* `status=progress` just displays the status of the command as it iterates through.
 
You may be wondering how you determine the device ('sdX'); the best way is to make sure your USB is **NOT** plugged in and run [lsblk](ubuntu/linux_notes?id=using-lsblk); then plug your USB in a USB slot and run [lsblk](ubuntu/linux_notes?id=using-lsblk) again. Note the device ('name') that appeared _after_ you plugged the USB into the USB slot.

!> If you misidentify the device and use `dd` you can do damage to your computer, so make _sure_ you have it correct!

## Enabling SSH

Once your RetroPie is up and running, you should enable SSH on RetroPie. Just as it is in [Raspbian](learn_to_code/raspberry_pi/raspbian?id=enabling-ssh), you will have to enable SSH with `raspi-config`. To do this in RetroPie, go into the settings and look for one that says something like `raspi-config`; from there, [follow the Raspbian instructions](learn_to_code/raspberry_pi/raspbian?id=enabling-ssh) on how to enable SSH.

> SSH is a fantastic tool to interact with your RetroPie; with it, you can log in from other computers in your house and run commands, manipulate files, etc. [SSH](ubuntu/linux_notes?id=ssh) can be run on the command line in a Linux environment; for Windows, you can use [WinSCP](https://winscp.net/eng/index.php) to interact with files and [PuTTY](https://putty.org/) to interact with the command line.
> <br><br>
> In order to use SSH, you will need the IP of your RetroPie; to get it, go into the 'RetroPie Configuration' screen and then select 'Show IP'; The IP will be displayed at the top (provided you have [connected to Wifi](learn_to_code/raspberry_pi/retropie?id=connecting-to-wifi) or have manually connected an ethernet cable).

## Changing Pi's Password

Once your RetroPie is up and running, you should change the `pi` account's password on RetroPie. You may not be able to change the login `pi` to something else (without alot of work), but you should certainly [change pi's password](ubuntu/linux_notes?id=setting-a-password) is the same default password is used for _all_ RetroPies (and Raspbian OSes, for that matter - the initial password is `raspberry`).

## Connecting To Wifi

To connect to Wifi, go into the 'RetroPie Configuration' screen and then select 'Wifi'-> 'Connect to Wifi network'.

## Expanding the Filesystem

Unfortunately, only a small portion of your microSD card is used by RetroPie initially; you will need to expand it to use the entire card.  To do so, enter the RetroPie Configuration screen, then select 'Raspi-Config'->'Advanced Options'->'Expand Filesystem' to expand the filesystem. You will need to reboot for this to take effect.

## Installing Optional Packages

I recommend installing the optional packages to your RetroPie, as it will enable more emulators on your unit. To do this enter the RetroPie Configuration screen, then select 'RetroPie Setup'->'Manage Packages'->'Manage Optional Packages'->'Update all optional packages from binary'.

## Updating the System

I also recommend updating the system, _provided_ you know how to [use SSH](ubuntu/linux_notes?id=ssh) as you may actually lock yourself out of the RetroPie and you need SSH to get back in. An example of logging in to your RetroPie with SSH (assuming your RetroPie has an IP of 10.0.0.2): `ssh pi@10.0.0.2`. Note you will need to do use SSH from a computer that is not your RetroPie unit.

To update RetroPie, enter the RetroPie Configuration screen, then select 'RetroPie Setup'->'Update packages'.

**<font size="4">If You Cannot Login After the Update</font>**

Unless you are skilled and changed the login, it will be `pi`; and if you did not follow my advice on changing the password, it will be `raspberry`. Sometimes, an update will make you put in a login and password upon starting RetroPie, and for some reason it rejects all attempts (I think it has something to do with not recognizing the login).

I found that to fix this, you will need SSH to log into the unit (so you will need some other computer to use SSH to get into RetroPie). Once you are in, type in `emulationstation` at the prompt; at this point it gets weird. EmulationStation will boot on the RetroPie itself (NOT in your SSH window). Go back to your RetroPie unit and go to the "RetroPie Configuration" screen, then "RetroPie Setup". This will open up a DOS selection screen _back on your SSH terminal_ for some reason. There, pick 'Configuration/Tools' -> 'Autostart' -> 'Start Emulationstation at boot'.

The issue should be resolved at this point.

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
|  FinalBurn Alpha   |   arcade / fba / neogeo   |   .7z .zip   |  
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
|  Neo Geo   |   neogeo   |   .7z .zip   |  
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

Its possible to simply create an additional folder on your RetroPi, save all of your games there, and use [symlinks](ubuntu/linux_notes?id=file-shortcuts-symlinks) that point to your ROMs but are located [in the approved ROM folders](learn_to_code/raspberry_pi/retropie?id=installing-roms). The advantage of this is: if you want to keep all of your ROMs on the RetroPi, but you have many of them and they are crowding the game selection screen(s) - _or_ if you want to rename the game name in the game selection screen - you can accomplish this with a symlink (and since symlinks are small in size, they do not take up much space at all). Please note that you will have to re-start EmulationStation after every time you make (or remove) symlinks in the approved ROM folders.

An example of making a symlink would be this:
```
ls -s /actualROMs/snes/somegame.smc /home/pi/RetroPie/roms/snes/someOtherName.smc
```
* The above creates a symlink.
* '/actualROMs/snes/somegame.smc' is the location of the _actual_ file you have stored on your RetroPie.
* '/home/pi/RetroPie/roms/snes/' is the [RetroPie SNES folder](learn_to_code/raspberry_pi/retropie?id=installing-roms) (where we must save the symlink).
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

## Selecting An Emulator

When you initially select a ROM to run, you will see a brief screen that shows you the ROM being run and the emulator that will run it; it is at this point that you have the option to modify the emulator selection (the available native systems can be found [here](https://github.com/RetroPie/RetroPie-Setup/wiki/Supported-Systems)). For most systems your best bet is to just use the default emulator; the exception to this are the [MAME (arcade) games](learn_to_code/raspberry_pi/retropie?id=loading-mame-roms), where you will almost surely have to change the emulator occasionally.

# MAME Arcade Games

ROMs in MAME act differently than other ROMs (for SNES, NES, Genesis, etc).

## Loading MAME ROMs

When installing arcade ROMs, you have two choices: Put the ROMs in the `arcade` folder _or_ the folder that is best suited to run them (which are `mame-mame4all`, `mame-libretro`, `mame-advmame`, or `fba`); see [here](learn_to_code/raspberry_pi/retropie?id=installing-roms) for more about folders. The advantage of using the `arcade` folder over the individual folders is all of your games are in one place; the disadvantage is there are roughly a dozen emulators to choose from and one does not rule them all, so you will have to [keep selecting a different emulator](learn_to_code/raspberry_pi/retropie?id=selecting-an-emulator) until you find one that works (i.e. you do not get dumped back to the ROM selection screen and there are no other major problems).

The indidual folders just group the _type_ of arcade ROMs, with the major groupings as follows: MAME4ALL, LIBRETRO, AdvancedMAME, and FinalBurnAlpha (NeoGeo). The advantage of using the individual folders is you are more likely to not have to switch the emulator; the catch is there is still a chance you will have to do so (especially with LIBRETRO and AdvancedMAME arcade games). The disadvantage is its not straightforward - at all - in regards to _which_ emulator will work best, and there is a decent chance that if you picked an emulator family blindly, you are probably wrong. In addition, when using the individual folders, EmulationStation groups them individually in the game selection screen (so all of your arcade games will not be in the same place).

See [here](https://github.com/RetroPie/RetroPie-Setup/wiki/MAME) for the main MAME page, [here](https://docs.google.com/spreadsheets/d/1antILt7D12EWOFzyJwTfB86NceghMJKXG7CdYumuHec/edit#gid=0) for a massive listing of ROMs and compatibility. There are individual emulators (and compatible ROMs) [on the RetroPie MAME page](https://github.com/RetroPie/RetroPie-Setup/wiki/MAME) (here is an [example of ROMs that are compatible with MAME4ALL](https://docs.google.com/spreadsheets/d/1SHspjyHavY9-PKbO2swDr52BS2Wl_mB_Vjx2Z1SXiD8/edit#gid=828747670) if you wish to view them individually (I suggest using the master list).


## Controls in MAME

Unfortunately, the buttons on each arcade game are different for each ROM (unlike a console, arcade games vary) and you will have to set them initially. To do so, start the game, and once the game is launched enter the Retro-Arch config by pressing the `Tab` button. Once there, you can configure the setup for each controller (or player). Note that you will need a keyboard for this, as you will need access to a `Tab` button and an `Esc` button (which exits the Retro-Arch config). Its possible to set up the controllers so that once you do, you will never need the keyboard (for that ROM) again.

To map the controller buttons, press the `Tab` button, then select 'Input (this game)' (you can select it with a controller, but you will have to press `Enter` on the keyboard). Here you can map all actions to your controller. It is recommended that you assign a key on your keyboard _and_ a controller for each major action (like start and coin insertion) for each player.

To assign a mapping, select the action from the listing, then press `Enter`; you are now in assign mode. Press the desired key on the keyboard and wait 3 seconds. Now, press `Enter` again and press the desired button on the controller that will trigger this action. Wait 3 seconds. Now you can set other buttons. It is worth noting that you are required to wait a few seconds after you assign a button to an action, as if you try to go too fast you will assign multiple buttons to the function (usually you accidentally assign the `up` and `down` arrow keys unintentionally). In addition, you have to assign all buttons in one shot - you cannot leave one action and come back to it later and assign more. If you assign a key to an action, and later you want to assign a key _and_ a controller button to that action, you have to re-apply the key _and_ the controller button to the action. Every time you leave an action and come back (and want to assign more buttons to that action) you _must_ re-enter _all_ buttons for that action.

It is suggested that you map the 'coin' action to the `select` button on your controller (and the 'start' action to the `start` button on your controller. Most games still simulate quarters being put in the machine, so you will need some way of doing this on the controller. This will have to be done for each player.

Once all actions are mapped to a button, pressing `Esc` will get you back to the game (just don't repeatedly press `Esc` as pressing this with no config screen displayed can exit the game).

> Its possible to map a combination of button presses to an action; this can be helpful at times. For example, MAME4ALL offers a 'pause' button, but it may be the case where all of your controller buttons are used. In this case, when you assign an action you can press _both_ desired buttons at the same time, which will assign that combination to the action (so in the case of pause, if you assign `Select`+`Start` on your controller to 'pause', pressing `Select`+`Start` will pause the game).

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

**<font size="4">MAME4ALL Specific Controls</font>**

There are a few 'general' buttons you should set for the MAME4ALL emulator. To get into these settings, get the game started and then press the `Tab` button; once there, select 'Input (general)' (you will have to press the `Enter` button to select this). Here are some select settings you may wish to set (note anything that says 'UI' referrs to the interface of the emulator itself):

| Setting | Description |  
| --- | --- |  
| Pause | Will pause the game. It is suggested you pick some combination of buttons for this (like `Down`+`Start`, for example) as well as the 'P' button on the keyboard. |  
| UI Select | Will make selections in the UI (i.e. the screen where you map actions to buttons). It is recommended that you set the `Enter` key to this in addition to the 'A' button on your controller. |  
| UI Cancel | Will escape out of the current UI screen. Pressing this combination will close the UI screen, or if it is closed, it will exit the game. It is recommended that you set the `Esc` key to this in addition to the combination `Select`+`Start` on your controller. |  
| 1 Player Start | Set this to 1 on the keypad _and_ the `Start` button on controller 1. |  
| Coin 1 | Simulates putting a coin in for player 1. Set this to 5 on the keypad _and_ the `Select` button on controller 1. |  
| 2 Player Start | Set this to 2 on the keypad _and_ the `Start` button on controller 2. |  
| Coin 2 | Simulates putting a coin in for player 2. Set this to 6 on the keypad _and_ the `Select` button on controller 2. |  

!> If using `mame-mame4all` and you make a mistake (like deleting the `Tab` button), wipe out the config file by deleting the file `/opt/retropie/configs/mame-mame4all/cfg/default.cfg`. In addition, each individual game has a config file in `/opt/retropie/configs/mame-mame4all/cfg`. On rare occasion a game will stop working, and deleting its config file may get it to work again.



