# PyTorch  


# What is PyTorch?  

[PyTorch](https://pytorch.org) is a widely-used, open-source machine learning library designed for building and training deep learning models. It was originally developed by Meta AI and is now a part of the Linux Foundation.


## Key Features

* **Tensors**: The fundamental data structure in PyTorch is the Tensor.  
  * Similar to NumPy arrays, tensors are multidimensional arrays that can be used for numerical computations.  
  * A key advantage of PyTorch tensors is their ability to leverage a GPU for accelerated computing, which is essential for training large-scale deep learning models.  
* **Dynamic Computation Graphs**: Unlike some older frameworks that required you to pre-define a static computation graph, PyTorch uses a "define-by-run" approach.  
  * This means the computational graph is built on the fly as the code is executed.  
  * This dynamic nature provides a high degree of flexibility, making it easier to debug models and implement complex, data-dependent architectures.  
* **Automatic Differentiation** (autograd): PyTorch's autograd engine automatically calculates the gradients needed for backpropagation.  
  * This is a critical process in training neural networks, and autograd handles it for you, allowing you to focus on building the model itself.  
* **Pythonic Interface**: PyTorch is known for its intuitive and easy-to-use Python interface.  
  * It integrates seamlessly with popular Python libraries like NumPy and offers a development experience that feels very natural to Python programmers.  


## Applications  

PyTorch is a popular choice for both cutting-edge research and production-grade applications. It's used in a wide variety of AI fields, including:
* Computer Vision: For tasks like image classification, object detection, and image segmentation.
* Natural Language Processing (NLP): For building models used in sentiment analysis, machine translation, and text generation.
* Reinforcement Learning: For developing AI agents that learn by interacting with an environment.

The PyTorch ecosystem is also robust, with specialized libraries like <font color="purple">torchvision</font> for computer vision and <font color="purple">torchaudio</font> for audio applications, further expanding its capabilities.


---  


# Installing  

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


## Install PyTorch  

!> If you have a Nvidia GPU, you _must_ [set up CUDA](learn_to_code/python/machine_learning/pytorch?id=cuda-setup) first!  

1\. Navigate to [the PyTorch download site](https://pytorch.org/get-started/locally/) and pick your version  
* Pick PyTorch Stable or Preview (I usually pick Stable)  
* Pick your OS  
* Pick the package (for us htis is Pip, but there are other options)  
* Pick the language (Python for us)  
* Pay close attention to the 'Compute Platform' - this is your version of CUDA (or equivalent)  
  * If you have no GPU, just pick CPU (or ROCm, but you will almost always pick CPU if you have no GPU)
  * Run `nvidia-smi` and look for something like `CUDA Version: ` and then get that version  
  * If your exact version is not available, get the closest one available that is lower (for example, I am running CUDA 12.9, but only 12.8 is available)  

2\. Make _sure_ you are in the right virtual environment (or [Anaconda / Conda](learn_to_code/python/conda) environment).  

3\. Find the `Run this Command:` on the PyTorch page (after you made all selections); copy it and run it.  
  * For me, this was `pip install torch torchvision --index-url https://download.pytorch.org/whl/cu129` but it may be different for you  
  * For anything that deals with audio, you will probably need `torchaudio` too so it will be something like:  
```
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu129
```

4\. Install _completely optional_ libraries: `pip install pynvml`  
  * pynvml is for Python bindings for the NVIDIA Management Library (NVML).  
    * It is used to interact with NVIDIA GPUs by PyTorch  
    * It is, usually, completely optional - only install if you want to dig deeper into torch for power, used VRAM, etc  

---  

# Cuda Basics in PyTorch  

Here is a very basic script in PyTorch:  

```Python  
import torch

#Check if CUDA is available / if a GPU exists - otherwise, the CPU should be used
device = "cuda" if torch.cuda.is_available() else "cpu"


if device == "cuda":
    device_count = torch.cuda.device_count()
    print(f"Number of GPUs Available: {device_count}")

    # Will simply print the GPU make / model
    # Note that we will just assume one GPU (i.e. '(0)'), but if you have multiple, this will show here
    gpu_name = torch.cuda.get_device_name(0)
    print(f"GPU: {gpu_name}") 
    
    gpu_memory = torch.cuda.get_device_properties(0).total_memory / 1024**3 # Convert to GB
    device_memory_used = torch.cuda.device_memory_used(0) / 1024**3 # Convert to GB
    print(f"Overall VRAM: {gpu_memory:.1f}(GB)  Used VRAM: {device_memory_used:.1f}(GB)")
    
    power_draw = torch.cuda.power_draw() / 100 # Have to divide by 10 to get Watts
    print(f"Power Draw: {power_draw} W")

    
else:
    print("CUDA not available. Using CPU (much slower)")

```

The above shows you how to:  
* Determine if a GPU Exists  
* Determine the number of GPUs  
* Determine the make/model of the current GPU  
* Overall VRAM Available  
* Used VRAM (current snapshot)  
* Power draw (current snapshot)  

> Note the use of the `device` variable - often times, this is set to either `cuda` or `cpu`; many libraries take the string `cuda` if a GPU is available, and `cpu` if a GPU is _+not_ available.  

---  

# TorchAudio  

<font color="green">TorchAudio</font> is a Python library that serves as an audio domain extension for PyTorch, a popular open-source machine learning framework. It provides tools and functionalities specifically designed for working with audio data within the PyTorch ecosystem.

Supported formats (not exhaustive):  
* WAV (various bit depths: 8-bit, 16-bit, 32-bit)  
* FLAC (lossless compression)  
* OGG/Vorbis (open-source compressed format)  
* SPHERE (speech research format)  
* MP3 (though some backends like SoX may not support MP3 torchaudio.backend — Torchaudio nightly documentation)  
* OPUS (modern compressed format)  

It can also extract audio from movie file, _provided you have <font color="purple">ffmpeg</font> installed_:  
* MP4  
* MOV  
* AVI  
* WebM  
* MKV  
* ...and many others that FFmpeg supports  

> To install <font color="purple">ffmpeg</font>, [become root](/operating_systems/ubuntu/linux_notes?id=becoming-root), [update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages), and then run: `apt-get install ffmpeg`  


## TorchAudio - Basics  

Here is a basic implementation:  

```Python  

import os
import torch
import torchaudio


output_path = "/some/path/to/an/audio/file.wav"

if not os.path.exists(output_path):
    print("Output file not found")

else:

    # Load and analyze audio
    # The waveform is the raw audio data - its of type torch.Tensor (a PyTorch Tensor)
    # waveform.shape[0] is the number of audio channels, and waveform.shape[1] are the number of samples (i.e. related to sample rate)
    waveform, sample_rate = torchaudio.load(output_path)
    duration = waveform.shape[1] / sample_rate
    max_amplitude = torch.max(torch.abs(waveform)).item()
    
    print(f"Audio Analysis:")
    print(f"   Duration: {duration:.2f} seconds")
    print(f"   Sample Rate: {sample_rate} Hz")
    print(f"   Channels: {waveform.shape[0]}")
    print(f"   Max Amplitude: {max_amplitude:.3f}")
    
    # Check if audio seems valid
    if duration < 0.5:
        print("⚠️  Warning: Very short audio output")
    elif max_amplitude < 0.01:
        print("⚠️  Warning: Very quiet audio output")
```

This is a very basic usage of <font color="green">TorchAudio</font>, and showcases:  
* How to get the raw audio data and the sample rate  
  * The raw audio is of type torch.Tensor (a PyTorch Tensor)  
* How to get the sample rate of the audio  
* How to find the max amplitude (i.e. how loud the audio is)  
* How to find the channel count  
* How to get the duration from a file  
  * The duration can be figured out by `number of overall samples in the file / sample rate`  

> `torchaudio.load()` doesn't output any specific audio format - it outputs raw numerical data (the waveform tensor) that represents the audio, regardless of what format the input file was.  

## Explaining Audio in PyTorch Tensor  

In [the basics](learn_to_code/python/machine_learning/pytorch?id=torchaudio), we covered how `torchaudio.load()` returns a `waveform` and a `sample_rate` - and the waveform is the raw audio data, which is of type `torch.Tensor`.  

You can print waveform and it will look something like this - this is the raw audio data!  
```
tensor([[ 0.0000e+00, -1.2207e-04, -2.1362e-04,  ...,  9.1553e-05,
          3.0518e-05,  0.0000e+00]])
```  
* There is actually far, far more data here - Python truncated it, and for good reason.  

This is, essentially, a list of lists (but as a PyTorch tensor):  
* Outer dimension: Audio channels  
* Inner dimension: Audio samples over time  

Since there is only _one_ outer dimension here, we know this is mono. What are those numbers? Those are amplitude values - the "height" of the sound wave at each point in time. For example:  
* 0.0000e+00 = silence (no sound)  
* -1.2207e-04 = small negative amplitude (sound wave going down)  
* -2.1362e-04 = slightly larger negative amplitude  
* 9.1553e-05 = small positive amplitude (sound wave going up)  

Audio amplitude values are typically normalized between -1.0 and +1.0:  
* -1.0 = maximum negative amplitude (loudest sound, negative phase)  
* 0.0 = silence  
* +1.0 = maximum positive amplitude (loudest sound, positive phase)  

The values like -1.2207e-04 are very small (that's -0.00012207), indicating quiet audio - matter of fact, if you took the absolute value of these, the closer the number is to 0, the quieter it is, and the closer it is to 1, the louder it is.  

So, this is, exxentially, a list of lists. How many numbers are in a single list? Its the 'sample rate'! So, if this audio represents 24kHz, there will be 24,000 entries _per list_ - and one list represents _one second_ of audio (so, dividing the total sample counts by seconds will give the duration of the audio).  

Put another way, If your sample rate is 24kHz:  
* 24,000 entries = 1 second of audio  
* 48,000 entries = 2 seconds of audio  

If you do waveform.shape[1], that tells you the total number of samples, and waveform.shape[1] / sample_rate gives you the duration in seconds!  


## Resampling

Often, the sample rate can be altered to meet specific needs. For example, if your audio is in 8kHz but some process needs audio in 24kHz, you can 'upsample' and convert it to 24kHz; similarly, if you have a 44.1kHz audio file and you need the sample rate to be 32kHz, you can 'downsample' to that sample rate. Just keep in mind that while downsampling, you do lose audio quality, and while upsampling, you are not increasing the audio quality - you are simply formatting it to a different sample rate by simply repeating the existing samples until the upsample sample rate is met (so, the quality isnt really increasing, you are just...duplicating the existing samples).  

```Python  

import os
import torch
import torchaudio


audio_path = "/some/path/to/an/audio/file.wav"
target_sample_rate = 24000

# Load audio
waveform, sample_rate = torchaudio.load(audio_path)

# Convert to mono if stereo
if waveform.shape[0] > 1:
    waveform = torch.mean(waveform, dim=0, keepdim=True)

# Resample if needed
if sample_rate != target_sample_rate:
    resampler = torchaudio.transforms.Resample(sample_rate, target_sample_rate)
    waveform = resampler(waveform)
    
# Save processed audio to temp file
temp_path = audio_path.replace('.wav', '_processed.wav')
torchaudio.save(temp_path, waveform, target_sample_rate)

duration = waveform.shape[1] / target_sample_rate
print(f"📄 Audio processed: {duration:.1f}s duration, {target_sample_rate}Hz")
```

The resampler is a torchaudio transform object - specifically a `torchaudio.transforms.Resample` instance. Think of it as a "converter" that changes the sample rate of audio. Its officially called a <font color="purple">resampling transform</font>, but...its a function / method.  

This:  
* Converts from stereo to mono  
  * This is done by averaging the channels:  
    * waveform.shape[0] > 1 checks if there's more than 1 audio channel (stereo has 2)  
    * torch.mean(..., dim=0) averages across dimension 0 (the channel dimension)  
    * keepdim=True keeps it as a 2D tensor instead of flattening to 1D  
  * Example: If you have stereo audio with left=`[0.5, 0.3]` and right=`[0.1, 0.7]`, the mono result becomes `[(0.5+0.1)/2, (0.3+0.7)/2] = [0.3, 0.5]`  
* Resamples - it makes the transformer `resampler`, using the current sample rate and the target sample rate, then re-samples the raw audio data (i.e. the waveform object).  