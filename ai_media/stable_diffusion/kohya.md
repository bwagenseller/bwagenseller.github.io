# Kohya  

# Install  

From the [Kohya install page](https://github.com/bmaltais/kohya_ss#installation):  

1\. Become root.  

2\. Install venv support for Python:  
```
apt install python3.10-venv
```  

Install the cudaNN drivers by following the instructions provided in this link.

Make sure you have Python version 3.10.6 or higher (but lower than 3.11.0) installed on your system.

If you are using WSL2, set the LD_LIBRARY_PATH environment variable by executing the following command:

export LD_LIBRARY_PATH=/usr/lib/wsl/lib/
Setup
To set up the project on Linux or macOS, perform the following steps:

Open a terminal and navigate to the desired installation directory.

Clone the repository by running the following command:

git clone https://github.com/bmaltais/kohya_ss.git
Change into the kohya_ss directory:

cd kohya_ss
If you encounter permission issues, make the setup.sh script executable by running the following command:

chmod +x ./setup.sh
Run the setup script by executing the following command:

./setup.sh
Note: If you need additional options or information about the runpod environment, you can use setup.sh -h or setup.sh --help to display the help message.

Install Location
The default installation location on Linux is the directory where the script is located. If a previous installation is detected in that location, the setup will proceed there. Otherwise, the installation will fall back to /opt/kohya_ss. If /opt is not writable, the fallback location will be $HOME/kohya_ss. Finally, if none of the previous options are viable, the installation will be performed in the current directory.

For macOS and other non-Linux systems, the installation process will attempt to detect the previous installation directory based on where the script is run. If a previous installation is not found, the default location will be $HOME/kohya_ss. You can override this behavior by specifying a custom installation directory using the -d or --dir option when running the setup script.

If you choose to use the interactive mode, the default values for the accelerate configuration screen will be "This machine," "None," and "No" for the remaining questions. These default answers are the same as the Windows installation.


## Install the CUDA Toolkit  

> The original instructions are [here](https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64)  

```
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-ubuntu2204.pin
mv cuda-ubuntu2204.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/12.3.0/local_installers/cuda-repo-ubuntu2204-12-3-local_12.3.0-545.23.06-1_amd64.deb
dpkg -i cuda-repo-ubuntu2204-12-3-local_12.3.0-545.23.06-1_amd64.deb
cp /var/cuda-repo-ubuntu2204-12-3-local/cuda-*-keyring.gpg /usr/share/keyrings/
apt-get update
apt-get -y install cuda-toolkit-12-3
```

!> NOTE If you already have the NVIDIA drivers installed at this point you are probably done and do not need to do this - the below instructions bricked my computer and I dont think they are necessary if you already have the video drivers installed.  

NVIDIA Driver Instructions (choose **one** option)
* To install the legacy kernel module flavor:
```
apt-get install -y cuda-drivers
```
* To install the open kernel module flavor:
```
apt-get install -y nvidia-kernel-open-545
sudo apt-get install -y cuda-drivers-545
```  
* If you need to uninstall NVIDIA drivers because the above screwed your setup, AND if you manually installed the drivers: if you still have the `.run` file you used to install, you can uninstall with: `sudo ./NVIDIA-Linux-XXXXXXXXXX.run --uninstall`    


# Running  

to run:
```
./gui.sh --listen 127.0.0.1 --server_port 7860 --inbrowser
```
* You _can_ include `--share` as well, but - this will publish your link publicly, so anyone in the world with the address can use your host! Unless you want this do _not_ us that option  
   * You will know that it publishes to a public url if you see something like `Running on public URL: https://XXXXX.gradio.live` after you run `gui.sh`   
* You can put your LAN IP (instead of 127.0.0.1) here so people on your LAN can access it, if you wish.  

# Warnings I Got

!> Brent Delete this section 

WARNING  Could not automatically configure accelerate. Please manually configure accelerate with the option in the menu or with:     
                         accelerate config.