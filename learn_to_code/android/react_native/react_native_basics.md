# React Native - Basics  

# What is React Native?  

React Native is a JavaScript-based framework that allows the software engineer to write mobile user interfaces - for Android _or_ iOS.  

# Installation  

1\. Install the Android SDK, preferably via the [installation of Android Studio](learn_to_code/android/android_studio?id=installation).  

2\. Get a Github account on [github.com](https://github.com) and then set up your [Github public key](https://github.com/settings/keys).  
   * This step _may_ be able to be skipped if you do _not_ have any remote connections to [Git](learn_to_code/git/) or remote connections to a Git-like service (i.e. Bitbucket).  
     * You will know if you need to do this if you run `npm install` in a project and see something like this:  
```  
npm ERR! git@github.com: Permission denied (publickey).
npm ERR! fatal: Could not read from remote repository.
npm ERR! 
npm ERR! Please make sure you have the correct access rights
npm ERR! and the repository exists.
npm ERR! 
npm ERR! exited with error code: 128
```  
   * This sounds like it doesnt make sense, but many packages used by `npm` (or Node.js or React Native) require a pull from Github. 
   * Once you have a Github account, you will need to set up an SSH key - to do so (<font color="red">Do not</font> do this as `root`):  
     * Get an [SSH key pair](operating_systems/ubuntu/linux_notes?id=ssh-key-setup) if you do not have one.  
	 * Go to [Github's public key setup page](https://github.com/settings/keys).  
	   * Find the button that says `New SSH Key` - click this. 
	   * Give this key title. 
	   * Find your public key file (probably `~/.ssh/id_rsa.pub`, but it could be different if you named it differently); copy this files contents and paste it into the `Key` box on the Github SSH Keys page.
	   * Click `Add SSH Key`.
     * Back on your host, run: `ssh-add ~/.ssh/[private_key_file]`, where `[private_key_file]` is your private key file (the filename is most likely `id_rsa`; do not use the brackets).  
     * If there are any errors, run the following and re-try the step above: 
```
eval `ssh-agent`
```  

     * If there were no errors, run this to see if the setup worked: `ssh -T git@github.com`.  If it comes back with something like this it was successful:  
```  
authenticated via ssh key.
You can use git to connect to Bitbucket. Shell access is disabled
```     

3\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root).  

4\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages).  

5\. Check to see if the packages `gradle` or `cmdtest` are installed - _consider_ removing with `apt remove gradle cmdtest`
   * If you use actively use `gradle` you may want to consider leaving it installed.  
     * If you leave `gradle` installed, it may interfere with some React Native processes (as React Native pulls in gradle); that said, _only_ remove this if you know what you are doing.  
   * I am not sure why `cmdtest` causes issues with `npm` but, it does. 
     * You can try leaving it in, but you may have odd issues.  
	 * If you do not use `cmdtest` simply remove it.  

6\. Install `dos2unix` with the command: `apt-get install dos2unix`  
   * This may not be necessary, but you may have to use this in projects where the React Native code was written on a Windows machine and you are seeing odd errors while using Linux or MacOS (particularly errors that mention `\n` somewhere in the error line).  
     * To clean all Windows return characters, go to the project base and run: `find . -type f -print0 | xargs -0 dos2unix`  

7\. Install the latest LTS version of node.js. 
   * This may seem odd, but React Native relies on the `npm` command, and that is difficult to install on its own without the rest of node.js - so install node.js.  
   * To install, run these commands:
```  
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
apt-get install -y nodejs
```  
   
8\. Upgrade `npm`
   * The version of `npm` installed by node.js is probably very old, so upgrade it.  
   * Run this as root: `npm install -g npm@6.14.4`  
     * Currently the latest version is `6.14.4` but this will probably be different for you.  

9\. Install yarn.
   * While not completely necessary, `yarn` does the same thing as `npm` but far more efficiently.  
   * To install the latest stable version of `yarn`:  
```  
apt remove yarn
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
apt-get update
apt-get install yarn -   
```  

10\. Get the `expo-cli` library via `yarn` - run: `yarn global add expo-cli`  
   * You _can_ do this in `npm` as well, but I do **<font color="red">not</font>** recommend it as `npm` does not seem to handle the packages well. To install via `npm`, but _only_ if you did not install via `yarn`: `npm install --global expo-cli`  
   * This installs the `expo-cli` 'globally'.
     * Most React Native packages will load _locally_ to a project (so you will have to download them for every project as it does not share a source).  
       * That said, this doesnt save much - it just stores it locally but the files are _still_ copied over for each project it seems, but the source will simply be a local one.  
     * If you wish to load this locally (initially), ignore the `global` aspect of the above command (or in the case of `npm`, do not include `--global`).  
     * Be careful with loading this globally to a local repository - you may have to do so as root, but if you do, the files will be owned by root. If you are not on an account with sudo access, using `yarn install` will copy the files from your local repository _but_ you will have some permission troubles with it unless you [change the ownership of the files](operating_systems/ubuntu/linux_notes?id=changing-ownership-of-files) as root.  

	 
