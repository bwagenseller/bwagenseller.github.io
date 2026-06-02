# AUTOMATIC1111  

# Install  

From [AUTOMATIC1111](https://github.com/AUTOMATIC1111/stable-diffusion-webui)'s github page:  

1\. Switch to root.  

2\. Run: `apt install wget git python3 python3-venv libgl1 libglib2.0-0`  

3\. Run: `apt install --no-install-recommends google-perftools`  
* This is because you need the malloc command - you may already have it, but these tools help as well.  

4\. Switch <font color="red">back</font> to the normal user (i.e. not root)!  

5\. (<font color="purple">Situational</font>) Automatic1111 uses an older version of Python, and may complain on newer Python versions. you CAN get it to use a [Conda environment](learn_to_code/python/conda?id=conda-environments):  
* [Create a Conda environment](learn_to_code/python/conda?id=create-conda-env): `conda create --name my_llama_env python=3.10.6`
  * I named mine `stablediffusion`  
  * The `3.10.6` is critical  
* [Activate your Conda environment](learn_to_code/python/conda?id=activate-conda-env): `conda activate stablediffusion`  
  * Or, instead of `stablediffusion`, whatever you named it.  
  * Every time you run Automatic1111 you _will_ have to activate this conda environment if its not activated  
* Find its executable: `which python`  
* Export that to the `PYTHON_COMMAND` variable: `export PYTHON_COMMAND=/PATH/TO/YOUR/CONDA/PYTHON/EXECUTABLE`


6\. Navigate to the directory you would like the webui to be installed.

7\. Execute the following command: 
```
wget -q https://raw.githubusercontent.com/AUTOMATIC1111/stable-diffusion-webui/master/webui.sh
```

8\. Run `webui.sh`  

9\. (<font color="purple">Situational</font>) You may have a newer GPU (i.e. an Nvidia 5090) that is not supported via the torch libraries installed - if this is the case, you will have do do this.  
* Remove the `venv` directory installed by `webui.sh`: `rm -rf stable-diffusion-webui/venv`  
* Find your Torch Version  
  * The version of Torch is specific to your GPU  
  * Navigate to [the PyTorch download site](https://pytorch.org/get-started/locally/) and pick your version  
    * Pay close attention to the 'Compute Platform' - this is your version of CUDA (or equivalent)  
      * Run `nvidia-smi` and look for something like `CUDA Version: ` and then get that version  
      * If your exact version is not available, get the closest one available that is lower (for example, I am running CUDA 12.9, but only 12.8 is available)  
  * Once everything is picked, it will give you a `pip3 install` line - copy _just the https address_  
    * Make SURE you are in the right conda environment - if you are not, or you are not sure, [activate your Conda environment](learn_to_code/python/conda?id=activate-conda-env) again!  
    * For me this was `https://download.pytorch.org/whl/cu128` but <font color="red">be thoroughly warned</font> this is _may not_ be what you will use.  
* The script `stable-diffusion-webui/launch.py` calls another script that will install torch (usually in `stable-diffusion-webui/modules/launch_utils.py`, specifically the `prepare_environment()` method).  
  * Find the line `torch_index_url = os.environ.get('TORCH_INDEX_URL', "https://download.pytorch.org/whl/cu121")` (or something similar) - replace that https with yours
  * Find the line `torch_command = os.environ.get('TORCH_COMMAND', f"pip install torch==2.1.2 torchvision==0.16.2 --extra-index-url {torch_index_url}")` and replace it with `torch_command = os.environ.get('TORCH_COMMAND', f"pip install torch torchvision --extra-index-url {torch_index_url}")`  
  * _IF_ you are using <font color="green">ipx</font>, you will also have to change the line `torch_command = os.environ.get('TORCH_COMMAND', f"pip install torch==2.0.0a0 intel-extension-for-pytorch==2.0.110+gitba7f6c1 --extra-index-url {torch_index_url}")` to `torch_command = os.environ.get('TORCH_COMMAND', f"pip install torch torchvision intel-extension-for-pytorch --extra-index-url {torch_index_url}")` and that seemed to work too - ymmv  
  * Save and exit  
* Run `stable-diffusion-webui/webui.sh`  
  * Yes, its different than the original `webui.sh` - although you may be able to run either / or  


10\. Check `webui-user.sh` for options.  

# Dark Mode  

To enale dark mode, simply append a `/?__theme=dark` at the end of the URL.  

# Add-Ons  

!> Add-Ons could potentially contain harmful code - its unlikely, but it can happen. I am unsure if any of these tools have harmful code, so download at your own risk.  

To install these, the flag `--enable-insecure-extension-access` _must_ be a command line argument present when you start the interface.  

## Tokenizer  

!> Add-Ons could potentially contain harmful code - its unlikely, but it can happen. I am unsure if any of these tools have harmful code, so download at your own risk.  

https://github.com/AUTOMATIC1111/stable-diffusion-webui-tokenizer

This is an extension for stable-diffusion-webui that adds a tab that lets you preview how CLIP model would tokenize your text. Ths is important, as tokens (vectors) are limited based on various factors (a common upper limit is 75).  This lets you see how your prompt would be tokenized.  
