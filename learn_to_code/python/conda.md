# Conda (Python)  

# What is Conda  

<font color="green">Conda</font> is a package and environment manager. It's a cross-platform CLI tool that manages isolated environments and installs packages; it was initially created to install a curated set of packages around machine learning, but now its its own thing. Unlike pip, it handles non-Python binaries too (C libraries, CUDA toolkits, R packages, ffmpeg, etc.), which is why the ML/scientific computing world adopted it. Open source, BSD-licensed. Originally created by the company that became Anaconda Inc., but the tool itself is its own thing.  


# Important Historical Change  

You should know a very brief history of <font color="green">Conda</font>.  

<font color="purple">Anaconda</font> is a commercial Python distribution from Anaconda Inc, which was formally open-source. It's a heavyweight installer (~3GB) that bundles Python, conda, and 250+ pre-installed scientific packages (NumPy, pandas, scikit-learn, Jupyter, the works) - otherwise known as <font color="purple">Anaconda</font>. Aimed at data scientists who want a batteries-included setup. Since <font color="purple">Anaconda</font> was so large of an install, they developed <font color="purple">Miniconda</font> - a lightweight version of <font color="purple">Anaconda</font>, but both had <font color="green">Conda</font> under the hood.  

At first this was free for everyone - but, eventually, they started charging a license fee for larger commercial use. They started monitoring who was connecting to their repositories (defaults channel) for packages - and then aggressively contacting them for licensing agreements.  

The community was not happy with this, so they forked the repo and developed <font color="purple">Miniforge</font> - <font color="green">Conda</font> under the hood, but instead of contacting <font color="purple">Anaconda</font>'s repositories it now contacts the (new-ishly developed) <font color="purple">Miniforge</font> repositories. All of the <font color="green">Conda</font> commands are the same - with the caveat that you _can_ use the `mamba` command instead of `conda` as its more efficient (you can still use `conda` though, if you wish); the only commands `mamba` cannot replicate are `conda init` and `conda config` (as of May 2026). `mamba` is _dramatically_ faster in some instances.   

!> If you are installing this on a corporate machine and do not have a corporate relationship with <font color="purple">Anaconda</font> - install / use <font color="purple">Miniforge</font> instead.  

# Install  

!> WARNING - This will be an installation that will work alongside the already installed version of Python - so there will be concurrent versions installed on the server, one in `/usr/bin/python` and the one we are installing now. If you have downloaded any other additional Python packages - or installed any Python packages - to the native version of Python you will have to re-install them again after you follow these instructions.  

## Miniforge Install  

!> Install this as a normal user, _not root_.  

Here is how to install <font color="green">Miniforge</font>:  

0\. (optional) Switch to the `/tmp` directory: `cd /tmp`  

1\. Download the installer: `wget https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-Linux-x86_64.sh`  

2\. Make it executable and run it: `chmod +x Miniforge3-Linux-x86_64.sh; bash Miniforge3-Linux-x86_64.sh`  
* This will install to: `~/miniforge3`  

3\. Source in the changes to `.bashrc`: `source ~/.bashrc`  
* `which conda` should show `~/miniforge3/bin/conda`  


## Anaconda Install
<span style='width: 20px; display:inline-block'>:snake:</span>  

!> You should really [install Miniforge](learn_to_code/python/conda?id=miniforge-install) instead of Anaconda - unless your company already has an agreement with Anaconda. Anaconda is _very_ aggressive in making sure you are paying them if you are a corporation - just use [Miniforge](learn_to_code/python/conda?id=miniforge-install) instead!  

### Downloading Anaconda

!> You should really [install Miniforge](learn_to_code/python/conda?id=miniforge-install) instead of Anaconda - unless your company already has an agreement with Anaconda. Anaconda is _very_ aggressive in making sure you are paying them if you are a corporation - just use [Miniforge](learn_to_code/python/conda?id=miniforge-install) instead!  


To download Anaconda, [go here](https://www.anaconda.com/download/) and select the linux 64 bit (x86) installer (it should end in .sh). You will have the choice of 'Distribution' or 'Miniconda' - if you are new to Python I suggest the Distribution, but...everyone else should pick Miniconda. The reason - working in conda environments is best practice, so any default packages would never be used.

### Installing Anaconda
<span style='width: 20px; display:inline-block'>:snake:</span>

!> You should really [install Miniforge](learn_to_code/python/conda?id=miniforge-install) instead of Anaconda - unless your company already has an agreement with Anaconda. Anaconda is _very_ aggressive in making sure you are paying them if you are a corporation - just use [Miniforge](learn_to_code/python/conda?id=miniforge-install) instead!  

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

### Uninstalling Anaconda
<span style='width: 20px; display:inline-block'>:snake:</span> <span style='width: 20px; display:inline-block'>:bangbang:</span>

!> If you remove Anaconda after installing Python packages, you will have to re-install those packages.

If you must uninstall Anaconda, simply remove the folder:  
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


# Conda Environments  

!> Its best practice to _always_ work in a conda environment. If you do not have Anaconda, you should still work in environments with the Python native `venv`. Note `venv` does not manage  non-Python libraries and binaries (e.g., CUDA toolkits, MKL, ImageMagick), which is a disadvantage.  

You have access to the `conda` command. The conda command is a very useful tool, As you can arbitrarily enter different 'environments' for programming, which will allow for clean environments that are far less likely to colllide. 

Basic steps: 
1\. [Create a unique environment](/learn_to_code/python/conda?id=create-conda-env)  

2\. [Activate the environment](/learn_to_code/python/conda?id=create-conda-env) _every_ time you want to act in that environment.  

3\. [Deactivate](/learn_to_code/python/conda?id=create-conda-env) when you are done (dont forget to do this).  

## Listing Environments  

To list all Conda environments:  
```Linux  
conda env list
```

## Create Conda Env

To create a conda environment:  
```
conda create --name my_llama_env python=3.13.5 numpy pandas pyarrow fastparquet
```
* `name my_llama_env` - Gives your environment a name  
  * Choose something descriptive for your project  
* `python=3.13.5` - Specifies the Python version you want in this environment.  
  * Your version may be slightly different.  
  * It's good practice to specify it.
* `numpy pandas pyarrow fastparquet`  
  * It's often best to install core scientific/data packages like these using conda if available, as conda often provides optimized, pre-compiled versions that play well together.  

> The path for all environments, by default, seems to be `~/.conda/envs/`  

### Cloning an Environment  

Sometimes you may wish to clone an environment; to do so:  
```Linux
conda create --name new_env_name --clone old_env_name
```  

## Activate Conda Env  

> Before you activate a Conda environment, you must first [create it](learn_to_code/python/conda?id=create-conda-env).  

Run `conda activate MY_LLAMA_ENVIRONMENT` (using the actual name) to activate the environment. Note your prompt will now have the environment name.  

> After an environment is activate, you can see it in your CLI prompt; this one has my 'general' Conda environment activated: `(general) brent@Velox2023:~/Documents$`   


## Deactivate Conda Env  

Run `conda deactivate` to deactivate the environment. Do this when you are done using the environment for the time being. Note this does _not_ delete it, it just deactivates it.  

## Deleting Conda Env

Run `conda env remove --name old_env_name`  to delete an environment permanently.  


## Running One-Offs  

You do not have to be in an environment to run a script - you can run it like so:
```
/home/YOUR_USERNAME_HERE/.conda/envs/my_llama_env/bin/python ~/my_project/chatbot.py
```  
* Swap out `YOUR_USERNAME_HERE` and `my_llama_env` for the real values  
* Each environment has its own python binary  
* Its best to use the absolute path  

> You can also use `/home/YOUR_USERNAME_HERE/.conda/envs/my_llama_env/bin/python` as the shebang line in the script, if you prefer.  

# Installing Packages  

If you were not in a conda environment, you would simply [use PIP](learn_to_code/python/pip) to install packages. However, its best to at least attempt to use conda's version of PIP _first_, Then_ use PIP if conda does not have what you need. Conda installs packages with the command `conda install`.  

When you use `conda install`, it tries to find a pre-compiled version of the package that is compatible with your specific operating system, Python version, and other packages in your Conda environment. This is generally the most robust approach within a Conda setup.  

The first thing you must do is [activate your environment](learn_to_code/python/conda?id=activate-conda-env); then, install with the line:
```Linux
conda install my-package-here  
```  
* The packages are identical to [PIP](learn_to_code/python/pip).  

If that doesnt work, try `conda-forge`:  
```Linux  
conda install -c conda-forge my-package-here  
```  
* Using `-c conda-forge` tells Conda to search the conda-forge channel specifically.  

If that fails, just use [PIP](learn_to_code/python/pip).  