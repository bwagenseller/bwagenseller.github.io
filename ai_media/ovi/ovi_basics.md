# Ovi Basics  
<!-- 
<font color="purple">Optional</font>
![tilix_login_shell.png](images/tilix_login_shell.png)
-->

# What is Ovi  

Ovi is an open-source video and audio generation model from Character.AI that simultaneously generates synchronized video and audio content from text or text-plus-image inputs. It generates 5-second videos at 24 FPS with resolution areas of 720×720 and supports various aspect ratios like 9:16, 16:9, and 1:1.


**Key Features**  

* Truly synchronized output - The model achieves precise lip synchronization without explicit face bounding boxes through pure data-driven learning, naturally extending to realistic multiple speakers and multi-turn conversations  
* Runs locally - With optimizations like block swapping, it can generate videos with as low as 8.2 GB VRAM, making it accessible to GPUs starting from the RTX 3000 series  
* Apache licensed - Completely open-source and free to use, modify, and integrate into your own projects  
* Multiple input modes - Supports text-to-video, image-to-video, and text-plus-image-to-video generation  

**Technical Architecture**  

Ovi uses an 11B parameter model consisting of a 5B video branch, 5B audio branch, and 1B fusion branch. The video branch is based on WAN 2.2 architecture while the audio components use MMAudio VAE.  

**Limitations to Be Aware Of**  

The model has some constraints: it's best at human-centric content due to data bias, may have limited fine detail in complex scenes, and output varies between runs since it's only at the pretraining stage without extensive post-training MediumDEV Community.


# Links  

* [Github Page](https://github.com/character-ai/Ovi)  
* [Aaxwaz's Github.io](https://aaxwaz.github.io/Ovi/)  
* [SECourses' Patreon](https://www.patreon.com/posts/download-ovi-pro-premium-140393220)  
* [It's Finally Here: The First TRUE Open-Source AI Video & Audio Generator](https://www.youtube.com/watch?v=T00VmkMQRPQ)  

# Install  


## Nvidia CUDA Library  

!> This section is only relevant if you have a Nvidia GPU.  

1\. _If you have not done so and you have a Nvidia GPU_, install the [CUDA Toolkit](https://developer.nvidia.com/cuda-toolkit)  
* As of June 2025, the instructions didnt clearly state you must export a few things to the path 
```
# Add CUDA Path - can check with 'nvcc --version'
export PATH=/usr/local/cuda/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```
  * You are free to run this directly, but its probably best to put it in `/bashrc` or `.profile` so it takes every time you start your system.   
  * Check with `nvcc --version` to see if it took  

2\. Put some variables in memory on a temporary basis - run:  
```  
export CUDA_HOME=/usr/local/cuda
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda/lib64
export PATH=$PATH:$CUDA_HOME/bin
```  
* If you do not do this, you will likely see an `No CMAKE_CUDA_COMPILER could be found.` error as explained [here](https://github.com/jetsonhacks/buildLibrealsense2TX/issues/13)   
* If you did step 2, you may be able to get away with only exporting CUDA_HOME, as the other items are probably already set  

## Ovi Install  

> The install instructions were [initially here](https://github.com/character-ai/Ovi?tab=readme-ov-file#-installation).  

Install Ovi locally (**no root needed**):  

1\. Create a [conda environment](learn_to_code/python/conda?id=create-conda-env) - `conda create --name ovi python=3.10.11`  
  * May not be fully necessary as the code itself uses Python environments, but to be safe - I partition it off with Conda  
  * Again, If you do not wish to use [Conda](learn_to_code/python/conda) you can skip this.  
  * Note that Ovi uses Python `3.10.11` - it may be helpful to know this.  
  * IF you skip the conda environment, you will have to create that Python virtual environment and then activate it:  
```  
# Create and activate virtual environment
virtualenv ovi-env
source ovi-env/bin/activate
```  

2\. [Active the Conda environment](learn_to_code/python/conda?id=activate-conda-env) - `conda activate ovi`  
  * Again, If you do not wish to use [Conda](learn_to_code/python/conda) you can skip this.    

3\. Pick a good directory to house the project  
  * <font color="red">Please note</font> that the directory you pick is the permanent directory for Ovi  

4\. Change the directory to the `Ovi` directory: `cd Ovi`   

5\. Clone the project in the directory above: `git clone https://github.com/character-ai/Ovi.git`  

6\. The next steps require you to install PyTorch via the line: `pip install torch==2.6.0 torchvision torchaudio`  
  * That said, I had to use `pip install torch==2.8.0 torchvision torchaudio --index-url https://download.pytorch.org/whl/cu128` as I am on an RTX 5090  
  

7\. _If_ you have a Nvidia GPU _and_ you have not recently [put the relevant variables in memory](ai_media/llama.cpp/llama_basics?id=nvidia-cuda-library), do so now.  

8\. Install other requirements: `pip install -r requirements.txt`  

9\. Install other requirements: `pip install einops`  

10\. Install Flash Attention: `pip install flash_attn --no-build-isolation`  

11\. Download the weights: `python3 download_weights.py`  

