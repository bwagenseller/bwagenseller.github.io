# EasyOCR  

# What is it?  

# Warning

!> I got most of this information from [Google's Gemini](https://gemini.google.com/) - I edited some notes here and there for brevity / injecting things I learned, but some of this is directly from its LLM output.  

# What Is It?  

<font color="green">EasyOCR</font>


# Install and Setup  


Here is how I installed EasyOCR. 

## Cuda Setup  

> This only applies if you have an Nvidia GPU - you can do this without a GPU, but it will be very slow.  

The first steps are installing the CUDA libraries for Nvidia GPU support:

1\. Switch to root.  

2\. Install the CUDA toolkit - download the package from the Nvidia site [using Nvidia's directions](https://developer.nvidia.com/cuda-toolkit) and then install.  

3\. Add items to .bashrc (or equivalent)  
```
# Add CUDA Path - can check with 'nvcc --version'
export PATH=/usr/local/cuda/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```
  * Check with `nvcc --version` to see if it took (you may have to source your .bashrc file first)  

4\. Source your .bashrc (or equivalent): `source ~/.bashrc`  

5\. Now you need to install an _additional_ CUDA package - the [Nvidia cuDNN](https://developer.nvidia.com/cudnn), which is the NVIDIA CUDA Deep Neural Network Library  
* I downloaded the 'tarball' - since you already have the CUDA toolit installed, the tarball is easier  
* Make sure to get the right one - run `nvidia-smi` and look for something like `CUDA Version: ` and then get that version  

6\. Extract the tarball - there should be a `include/` and `lib/` directories  

7\. Navigate to the tarball directory and then copy the contents of both directories to the CUDA toolkit directory:  
```Terminal    
cp include/* /usr/local/cuda/include/
cp lib/* /usr/local/cuda/lib64/ # Or /usr/local/cuda/lib/ depending on your setup
```
* This assumes the CUDA toolkit was installed to `/usr/local/cuda` (it is by default)  
* These are a bunch of `cudnn*` and `libcudnn*` files  

8\. Add other users to the `cuda` group (really, any account that will use the GPU)  
  * As advised on [askubuntu.com](https://askubuntu.com/questions/893528/restrict-user-access-to-gpu)  

  


## EasyOCR Setup  

> Do not do this as root.  

After you have [installed CUDA](learn_to_code/python/machine_learning/whisperx?id=cuda-setup) you can set up your EasyOCR environment  

1\. Create your [conda environment](learn_to_code/python/conda): `conda create --name easyocr python=3.15 numpy pandas pyarrow fastparquet`  
* Yo do not have to use Conda; if you wish, you can use Python's native virtual environment functionality, too.  
* I named my environment `easyocr`, but you are welcome to name it whatever you wish.  
* As of July 2025, you want to go no higher than python 3.12 - Python 3.15 was bombing out on me as the dependencies used by whisperx need 3.12 (or lower, but not too low).  
* While not strictly required, if you want to simulate streamed speech, `numpy` is required for audio buffer manipulation.  
  * The other packages - `pandas pyarrow fastparquet` - are optional (I like using them as a base, though).  


2\. Switch to your Conda environment: `conda activate stt`  
* I named my environment `stt` (speech to text), but you are welcome to name it whatever you wish.  
* Yo do not have to use Conda; if you wish, you can use Python's native virtual environment functionality, too.  

5\. Install a _specific_ version of `moviepy`: `pip install moviepy==1.0.3`  
* Its possible that the version can be different later, but as of July 2025, version 1.0.3 was the specific version that worked.  

6\. Install Torch  
* The version of Torch is specific to your GPU  
* Navigate to [the PyTorch download site](https://pytorch.org/get-started/locally/) and pick your version  
  * Pay close attention to the 'Compute Platform' - this is your version of CUDA (or equivalent)  
    * Run `nvidia-smi` and look for something like `CUDA Version: ` and then get that version  
    * If your exact version is not available, get the closest one available that is lower (for example, I am running CUDA 12.9, but only 12.8 is available)  
* Once everything is picked, it will give you a `pip3 install` line - copy that and run it to install Torch  
  * For me this was `pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu128` but <font color="red">be thoroughly warned</font> this is _probably_ not the install line you will use.  

7\. Install EasyOCR: `pip install easyocr`  

8\. Install OpenCV - you may need to clean images up, and a good tool for that is OpenCV: `pip install opencv-python`  


8\. In my case, as of July 2025, Whisperx installed an old version of `ctranslate2` that expects an _old_ version of CudNN - I had to upgrade to version 4.5.0 a la: `pip install ctranslate2==4.5.0`  
* Technically this version is out of the requirements range for whisperx, but you are stuck otherwise: the older version of CudNN is not available, and besides, you never want to install someting that is too ancient, expecially if it has to coexist with other libraries (i.e. the CUDA toolkit)  
  * That said, version 4.5.0 _should work_, according to Google Gemini, as its _just_ outside of the recommended range.  

> At this point, `whisperx` should be set-up properly; remember the command `nvidia-smi` to get your CUDA version (or to just check out the health of your GPU), and if you get stuck, uninstall everything with `pip uninstall whisperx ctranslate2 pyannote.audio` to try again.  