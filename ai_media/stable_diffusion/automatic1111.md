# AUTOMATIC1111  

# Install  

From [AUTOMATIC1111](https://github.com/AUTOMATIC1111/stable-diffusion-webui)'s github page:  

1\. Switch to root.  

2\. Run: `apt install wget git python3 python3-venv libgl1 libglib2.0-0`  

3\. Run: `apt install --no-install-recommends google-perftools`  
* This is because you need the malloc command - you may already have it, but these tools help as well.  

4\. Navigate to the directory you would like the webui to be installed.

5\. Execute the following command: 
```
wget -q https://raw.githubusercontent.com/AUTOMATIC1111/stable-diffusion-webui/master/webui.sh
```

6\. Run `webui.sh`  

7\. Check `webui-user.sh` for options.  

# Dark Mode  

To enale dark mode, simply append a `/?__theme=dark` at the end of the URL.  

# Add-Ons  

!> Add-Ons could potentially contain harmful code - its unlikely, but it can happen. I am unsure if any of these tools have harmful code, so download at your own risk.  

To install these, the flag `--enable-insecure-extension-access` _must_ be a command line argument present when you start the interface.  

## Tokenizer  

!> Add-Ons could potentially contain harmful code - its unlikely, but it can happen. I am unsure if any of these tools have harmful code, so download at your own risk.  

https://github.com/AUTOMATIC1111/stable-diffusion-webui-tokenizer

This is an extension for stable-diffusion-webui that adds a tab that lets you preview how CLIP model would tokenize your text. Ths is important, as tokens (vectors) are limited based on various factors (a common upper limit is 75).  This lets yo usee how your prompt would be tokenized.  

