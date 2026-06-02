# Python Package Installer  

PIP stands for "Pip Installs Packages" or "Pip Installs Python" - and yes, it is recursive. Basically, its the package installer for Python.  

# Install Python PIP

!> Modern versions of Python have this installed natively - so you probably dont have to do this. If `which pip` shows you where pip is, this is not necessary.  

Python PIP is the official method to install Python packages; when possible, use PIP to install anything related to Python.

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)

3\. Install python-pip:

```
apt-get install python3-pip
```

# A Critical Note  

Usually, packages are downloaded and saved as root - this is _not_, I repeat, *NOT* how its done in python for most packages. Sure, the base `python`, the `pip` loader, and a few other things installeld via `apt` are installed as root, but if you install anything with the `pip` command, you should almost _never_ do this as root! Do this [in an environment](learn_to_code/python/conda?id=conda-environments) as a normal user (its not great to simply be a user for most things). Why? Well, Python packages have a penchant for stepping on other packages and having package dependency issues; installing as a normal user diminishes this for the user, and installing to an environment is _far_ better.

> I strongly recommend [installing Anaconda](operating_systems/ubuntu/server_build?id=python-anaconda-install), which is a bundle of machine learning packages as well as some nifty environment tools.  


# Listing Packages  

To list IPP packages:
```
pip list
```  

sudo /usr/bin/anaconda/python3/bin/pip freeze | grep -v '^-e' | xargs sudo /usr/bin/anaconda/python3/bin/pip uninstall -y
/usr/bin/anaconda/python3/bin/pip freeze | grep -v '^#' | awk '{print $1}' | grep -v '^-e' | xargs /usr/bin/anaconda/python3/bin/pip uninstall -y
/usr/bin/anaconda/python3/bin/pip freeze | grep -v '^#' | awk '{print $1}' | grep -v '^-e' | grep -v '^TBB' | xargs /usr/bin/anaconda/python3/bin/pip uninstall -y


## View Packages  

To view packages in an environment, [activate the environment](/learn_to_code/python/conda?id=create-conda-env) and then run:
```
pip freeze > requirements.txt
```  

## PIP Check  

If you run a script that does not have the necessary libraries installed, it will go to your user environment first, then the global environment. This is not ideal, as it can cause conflicts.  

To combat this, you can run `pip check` whilst in an environment [is activated](/learn_to_code/python/conda?id=create-conda-env) to see if all packages are contained in that environment. If it outputs "No broken requirements found.", it means all dependencies are met by packages available in that specific environment. If it finds issues, it will tell you which packages have missing or incompatible dependencies.  

