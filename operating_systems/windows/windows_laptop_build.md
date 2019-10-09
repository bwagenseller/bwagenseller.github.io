# Windows Laptop Build

# Running System Restore (From Image)

## winpeshl.ini File Error

When trying to load the restore from the CD you may get this error:

> A winpeshl.ini file is present, but no commands were successfully launched. This could be caused by incorrect formatting or an invalid exevutable name. Please consult the documentation for more information.

The fix was found [here](https://akosbakos.ch/solved-windows-10-1803-upgrade-with-winpeshl-ini-issue/) (and there are other solutions on this page), but to reiterate:

1\. change directory to root:
```
cd \
```

2\. Find the file `RecEnv.exe`:
```
dir RecEnv.exe /s
```

3\. Whatever directory is returned in step #2, cd to that directory.

4\. Run `RecEnv.exe` from the command line.

Now the restore program should start as normal.

---

# Random Notes

*Brave Browser
*Kaspersky Total Security - License expires ~2020-08-10
*Notepad++ v. 7.7
*Slack
*MS Office 2019 (bought from Amazon on 2019-06-06, key can be found in Amazon's 'game and software library' but there is also an email in brentwagenseller@hotmail.com for this date (email does not contain key))
*Java 11.0.3
*Java 8u211
*SQLyog 13.1.2-0.x64Community
*WinSCP 5.15.2
*putty 0.71 (64bit)
Git for Windows - https://gitforwindows.org/
*IntelliJ (Community) 2019.1.3
*Logitech Capture (which supports the Logitech HD Pro Webcam C920)
VirtualBox - https://www.virtualbox.org/wiki/Downloads
VirtualBox Extension pack - https://www.virtualbox.org/wiki/Downloads


**Putty**
Adding SSH
	Click on saved Object
	Click 'Load'
	Go to 'Connecttion -> 'Data' and look for 'Auto-login username'
		Type in your login here
	Go to 'Connecttion -> 'SSH' -> 'Auth' and look for 'Private key file for authentication'
		Find your private .ppk key and put its location (with file name) here
	Go back to 'session' and click 'Save'
	
	

***WinSCP***
Show Hidden Files
	options / preferences / panels - click 'show hidden files'

Adding Editor
	options / preferences / editors - click 'Add...'
	select 'External editor'
	browse to find notepad++ (for me, it was "C:\Program Files\Notepad++\notepad++.exe")
	click 'OK'
	select Notepad++ and press the 'Up' button until Notepad++ is the top editor
	click 'OK'
	
Adding .ssh private key
	Click 'Advanced...'
	Go to 'SSH / Authentication...'
	Find 'Private key file' and browse for your file
	Click 'OK'
	Click 'Save'
	
	
**Creating Key***
	Open 'PuTTYgen'
	Make sure RSA is selected
	click 'Generate'
	Move mouse around wildly in the blank space in the 'key' box (just under the green progress bar)
	Save public key by pressing the button 'Save public key' - name it anything
	Save private key by pressing the button 'Save private key' - it MUST end in .ppk
	Copy the enitre contents of the box labeled 'Public key for pasting into OpenSSH authorized_keys file'
		Create a file called 'authorized_keys' (no extension) and save this to that file
		
		
**setting up maven***
	Download the .zip file here: https://maven.apache.org/download.cgi
		Unzip to a directory that does NOT use spaces! I like to install the contents to C:\usr\java\{whatever the base name of the maven folder is in the zip}
	In the search box, type in 'env' and double click 'Edit the system environment variables'
	Go to the 'Advanced' tab
	Click on the 'Environment Variables' button
	Under 'System variables' section (the bottom section) find the 'Path' variable; double click it
	Click 'New' and add the path; if you installed maven to 'C:\usr\java\{whatever the base name of the maven folder is in the zip}', this will be 'C:\usr\java\{whatever the base name of the maven folder is in the zip}\bin' (NOTE THE \bin AT THE END) 
		As an example, when I installed this the path was 'C:\usr\java\apache-maven-3.6.1\bin'
	Click 'OK'
	Click 'OK'
	Click 'OK'
	
	Add Maven to PATH variable
	
	


	