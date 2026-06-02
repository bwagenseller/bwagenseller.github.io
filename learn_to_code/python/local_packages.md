# Local Packages in Python  

Its possible to re-use functions, methods, and classes in Python; the general idea is to store them in a directory specified by you, and then have Python look in that directory for the class, function, or package.  

# How Python Finds Modules  

When you use an `import` statement (e.g., `import my_module` or `from my_package import my_module`), Python looks for the module in a specific order of locations. This list of locations is stored in `sys.path`. You can see it yourself by running:
```Python
import sys
print(sys.path)
```  

Typically, sys.path includes:
* The directory of the script being run.
* Directories listed in the [PYTHONPATH](learn_to_code/python/local_packages?id=adding-to-python-path) environment variable.  
* Standard library directories.  
* Site-packages directories (where third-party packages are installed).  


# Adding to Python PATH
<span style='width: 20px; display:inline-block'>:snake:</span>

If we want to eventually reference _other_ python scripts and packages, we need a universal way to find them. This allows us to build Python functions in the designated folder, then we can import them into other Python scripts with a simple `import` at the top of the file. We can do that by setting the variable `PYTHONPATH` in the file `~/.bashrc`.  

As an example, We are going to add `/home/YOUR_LOGIN_HERE/GeneralPythonFunctions` to the `PYTHONPATH`, so any Python function we write in this folder can be quickly imported into other Python scripts with the `import` function in Python.  

Setup:

1\. Edit the .bashrc file in your home directory:  
```linux  
vi ~/.bashrc
```

2\. Add these two lines at the bottom:
> PYTHONPATH=/home/YOUR_LOGIN_HERE/GeneralPythonFunctions:$PYTHONPATH <br>
> export PYTHONPATH

3\. Press escape, then type this to save and quit: `wq!`

4\. Source in the .bashrc file: 
```linux
source ~/.bashrc
```

!> You will have to do this for all users that will use this functionality in Python.  

# Creating Local Packages  

To create a Python package, pick a directory you listed in [PYTHONPATH](learn_to_code/python/local_packages?id=adding-to-python-path), and then create a subdirectory in that directory (we will assume thats a directory called `my_package`), and then structure it like so:
```directory
my_package/
  ├── __init__.py       # Makes 'my_package' a Python package
  └── my_modules/
    ├── __init__.py   # Makes 'my_modules' a subpackage
    └── some_python_class.py
```
* Note the use of `__init__.py` multiple times.  
  * This is critical to tell Python that this is a distinct package  
  * `__init__.py` can be a totally blank file, but...it has to exist.  
  * Without this file, Python would treat the directory as a regular folder and wouldn't be able to import modules directly from it using dot notation (e.g., `from my_package.my_modules import ...`).  
  * Each `__init__.py` defines a "package boundary."  
    * When you have nested directories that you want Python to treat as packages and subpackages, each level needs its own `__init__.py` to declare it as such.  
* `some_python_class.py` is your class.  

Also, its not a hard requirement to have a `my_modules` subdirectory under `my_package` - if you only had a few modules you could do that like so:  
```directory
my_package/
  ├── __init__.py       # Makes 'my_package' a Python package
  └── some_python_class.py
```

> Its possible to simply put `some_python_class.py` directly in the [PYTHONPATH](learn_to_code/python/local_packages?id=adding-to-python-path) directory, although it is preferred if you make a package.  