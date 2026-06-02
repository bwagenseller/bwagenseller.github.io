# Media Basics  

# Definitions  

## Sample Rate  

A <font color="green">Sample Rate</font> is the number of times per second that an analog audio signal is measured and converted into a digital value. This process, known as sampling, is how continuous sound waves are translated into discrete data that computers can store and process. If an audio file has a sample rate of 8000 Hz, this means there 8000 audio samples per second in the file.  

Think of a movie camera recording a live scene. The sample rate is like the frame rate of the camera. The higher the frame rate (e.g., 60 frames per second), the more "snapshots" the camera takes, resulting in a smoother, more detailed representation of the motion. Similarly, a higher sample rate takes more "snapshots" of a sound wave per second, which allows for a more accurate digital representation of the original sound.

Sample rate is measured in <font color="purple">Hertz</font> (<font color="purple">Hz</font>), or samples per second. Common sample rates include:  
* 8 kHz (8,000 Hz)  
  * Old telephones used this for decades (and landlines still use this)  
* 16 kHz (16,000 Hz)  
  * Some specialized VoIP telephones use this (although most still use 8 kHz as that is what landlines use, and just using that makes it easier)  
* 22 kHz (22,050 Hz)  
  * Some machine learning libraries, like Tortoise, use this sample rate to generate cloned voices.  
* 24 kHz (24,000 Hz)  
  * Some machine learning libraries, like F5-TTS, use this sample rate to generate cloned voices.  
* 44.1 kHz (44,100 Hz)  
  * This is the standard for music CDs and is sufficient to capture all frequencies audible to the human ear (up to about 20 kHz).
* 48 kHz (48,000 Hz)  
  * This is the standard for audio for video, film, and television, as it syncs better with common video frame rates.
* 96 kHz (96,000 Hz) and 192 kHz (192,000 Hz)  
  * These higher rates are used in professional audio production for increased fidelity and more flexibility during editing, although the audible difference for most listeners is debated.  

# Conda Environment  

Its good to have a good Conda environment that chan handle most media situations (microphone, speakers, video processing, audio processing, etc).  


1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)

3\. Install ffmpeg: `apt-get install ffmpeg libportaudio2`  
  * `ffmpeg` is useful in all kinds of situations  
  * `libportaudio2` is for `sounddevice` in Python
  
4\. Exit out of root: `exit`  

5\. Delete the Conda environment, if it exists: `conda env remove --name media`  

6\. Create the Conda environment: `conda create -y --name media python=3.12.11 numpy scipy pandas conda-forge libstdcxx-ng`  
* As of August 2025, Python 3.13 is not widely used - so we pick the latest 3.12 release  
* `pandas numpy scipy` are used in a bunch of situations - so grab them  
* `conda-forge libstdcxx-ng` are for using WhisperX (<font color="red">you can skip if</font> you wont use WhisperX)  
  * If you need to install this later, run: `conda install -c conda-forge libstdcxx-ng` but <font color="red">make sure your appropriate conda environment is active</font>.  

7\. Activate the Conda environment: `conda activate media`  

8\. Install [torch](learn_to_code/python/machine_learning/pytorch?id=install-pytorch)  
  * You will need to install `torch torchvision torchaudio`  
  * If you have a Nvidia GPU, you _<font color="red">must</font>_ [set up CUDA](learn_to_code/python/machine_learning/pytorch?id=cuda-setup) before installing PyTorch!  
  * For me, this was `pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu129`, but _<font color="red">it will probably be different for you</font>_.  

9\. Install the rest: `pip install sounddevice wavio pynput matplotlib ffmpeg-python librosa pygame playsound noisereduce pydub webrtcvad`  
* `sounddevice` is for microphone recording  
* `wavio` is for microphone recording  
* `pynput` is for microphone recording  
* `matplotlib` is for breaking up youtube videos
* `ffmpeg-python` is for mpeg manipulation (i.e. wav files)  
* `librosa` is for the voice training  
* `pygame playsound` are purely for playing audio over your speakers  
* `noisereduce` for reducing background noise while breaking up YouTube videos.  
* `pydub` is for converting various files to .wav files.  
* `webrtcvad` is for using WhisperX (<font color="red">you can skip if</font> you wont use WhisperX)  

!> I had trouble with this and had to install in this fashion to get the microphone recording to work - I was seeing the following error. Initially I had more installed on the initial Conda line, but it moved it to the pip line and something seemed to work. If you are having trouble, try to install in the above order! `python: src/hostapi/alsa/pa_linux_alsa.c:3636: PaAlsaStreamComponent_BeginPolling: Assertion 'ret == self->nfds' failed. Aborted (core dumped)`  

