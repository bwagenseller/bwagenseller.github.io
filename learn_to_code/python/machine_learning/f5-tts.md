 
# F5 TTS  


[F5-TTS](https://github.com/SWivid/F5-TTS.git) (<font color="green">Fairytaler that Fakes Fluent and Faithful speech with Flow matching</font>) is a <font color="purple">Text To Speech</font> AI model that takes text, a sample audio file, and then turns the text into human speech, using the audio file as a template. It uses something called <font color="purple">zero-shot voice cloning</font>; <font color="purple">zero-shot voice cloning</font> means you give it an audio sample, and it uses that sample to synthesize a voice that sounds like that sample (i.e. it clones the voice). It does not train anything - it simply uses the sample, performs the job, and then exits.  

<font color="green">F5-TTS</font> has no internal voices you can use - you MUST have a sample (_and_ the _exact_ words spoken in the sample).  

# VRAM Footprint  

This seems to use about 2GB in VRAM for most applications.  

# Install  

> This uses [Anaconda / Conda](learn_to_code/python/conda); you can simply use a Python virtual environment if you wish, just swap out the `conda` commands for your equivalent virtual environment commands.  

!> If there are specific Python versions, library versions, etc, this is because as of the writing of this (August 2025) this is what specifically worked; it will probably change in the future.  


To install the <font color="green">F5-TTS</font> library in Python: 


1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)

3\. Install ffmpeg: `apt-get install ffmpeg`  

4\. Exit out of root: `exit`  

5\. Delete the f5-tts conda environment, if it exists: `conda env remove --name f5-tts`  

6\. Create the Conda environment: `conda create --name f5-tts python=3.12.11 numpy scipy`  

7\. Activate the Conda environment: `conda activate f5-tts`  

8\. Install [torch](learn_to_code/python/machine_learning/pytorch?id=installing)  
  * You will need to install `torch torchvision torchaudio`  

9\. Change the directory to a directory where you can temporarily download a git directory.  
  * You do _not_ have to do this step, nor the step that clones from the Git repo and then installs it, if you simply want to just install from the PIP command: `pip install f5-tts`  
    * That said, I like using the Git repo as it gives a more recent version of F5-TTS

10\. Clone the Git Directory: `git clone https://github.com/SWivid/F5-TTS.git`  

11\. Change to the `F5-TTS` directory: `cd F5-TTS`  

12\. Run PIP install: `pip install .`  
  
13\. Install other libraries: `pip install ffmpeg-python pygame playsound`  
  * ffmpeg-python is needed for manipulating media files (i.e. wav files)
  * 'pygame' and 'playsound' are purely for playing audio over your speakers


14\. Install _completely optional_ libraries: `pip install pynvml`  
  * pynvml is for Python bindings for the NVIDIA Management Library (NVML).  
    * It is used to interact with NVIDIA GPUs by PyTorch  
    * It is completely optional - only install if you want to dig deeper into torch for power, used VRAM, etc

# Recording Settings  

Here are the recommended settings for recording an audio file that could be used for F5-TTS. Note that you will need exactly one sample, with the sweet spot of 10-15 seconds (although 8-30 seconds will work):
* Sample Rate: 22050 Hz  
  * You can use 44100, but it will probably downsample
* Channels: Mono (1 channel)  
* Bit Depth: 16-bit minimum, 24-bit preferred for quality  
* Realistically, you will use a DTYPE of `int16` or `float32`, with `float32` being preferred  
* Format: WAV (uncompressed)  
  * Other formats may be accepted, but WAV is suggested   
* Gain: 1.0 
  * Adjust if needed (0.1 to 2.0)

Tips:  
* One audio .wav file of 8-12 seconds (although this should be 10-15 seconds, [see the warning](learn_to_code/python/machine_learning/f5-tts?id=current-warnings) below)  
  * 8-30 seconds will work, too (again, [mind the warning](learn_to_code/python/machine_learning/f5-tts?id=current-warnings))  
* Try to avoid background noise and background music  
* Use natural speech - don't read (unless you want your clone to sound like its reading)  
* Remember, you must type EVERYTHING EXACTLY based off of your recording - so avoid sounds that are not words, 'uh', etc etc  
* Have one and only one speaker in your audio file  

## Current Warnings  

> More info [here](https://github.com/SWivid/F5-TTS/issues/55) 

As of August 2025, <font color="green">F5-TTS</font> has duration-based processing bugs. In theory, F5-TTS works well with 10-15 second references, but longer audio (20+ seconds) causes truncated/corrupted output; subsequently, when a voice is cloned, the speesh is _very_ fast. The speed issue isn't actually speed - it's truncation/timing corruption that makes it sound like the speech is rushed or compressed. 

Why This Happens
F5-TTS uses a flow-matching architecture where the text input is padded with filler tokens to match the length of the input speech. With longer reference audio:
* The model has to process more audio frames  
* The text-to-speech alignment becomes less stable  
* The model may truncate or compress the output to fit expected durations  
* This creates the "fast speech" effect - it's not playing faster, it's trying to fit the same amount of content into less time  

To combat this, I am using 8 second clips as samples, at least currently. An 8-second sample hits the "sweet spot" where its:
* Long enough for good voice characteristics  
* Short enough to avoid F5-TTS's duration processing issues  
* Within the stable range (10-15s is mentioned as optimal, but 8s works too)  

15-second samples are at the upper limit where F5-TTS starts having issues: 
* Still within the theoretical range, but pushing the boundaries
* May trigger truncation/compression behavior
* The specific content or speech patterns in 15s segments might be harder to process

!> For now, use 8-12 second samples for <font color="green">F5-TTS</font>. Hopefully this is fixed sometimes after August 2025!


# Heart of F5-TTS  

```Python  
"""
F5-TTS Voice Cloning Script
Complete script for cloning and testing human voices using F5-TTS
"""

import os
import torch
import torchaudio
import warnings
from f5_tts.api import F5TTS
import soundfile as sf

warnings.filterwarnings("ignore")

"""
Prepare audio file for F5-TTS (resample if needed)

Many TTS libraries REQUIRES a specific sample rate (for exmaple, f5-TTS requires 24kHz), and most require mono input (not stereo) - so enforce both

Input:
    audio_path - the path of the reference audio  
    target_sample_rate - the target sample rate
Output:
    temp_path - the path of the audio file, resampled **Note this ALSO make a .wav file if a resampling occurred.
"""
def resample_for_tts(audio_path: str, target_sample_rate: int = 24000) -> str:
    try:
        file_altered = False
        # Load audio
        waveform, sample_rate = torchaudio.load(audio_path)
        
        # Convert to mono if stereo
        if waveform.shape[0] > 1:
            file_altered = True
            waveform = torch.mean(waveform, dim=0, keepdim=True)
        
        # Resample if needed
        if sample_rate != target_sample_rate:
            file_altered = True
            resampler = torchaudio.transforms.Resample(sample_rate, target_sample_rate)
            waveform = resampler(waveform)

        if file_altered:
            # Save processed audio to temp file
            temp_path = audio_path.replace('.wav', '_processed.wav')
            torchaudio.save(temp_path, waveform, target_sample_rate)

            print(f"Sample file needed alterations - new file [{temp_path}] with sample rate {target_sample_rate}Hz generated.")
        else:
            temp_path = audio_path
        
        duration = waveform.shape[1] / target_sample_rate
        print(f"Audio processed: {duration:.1f}s duration, {target_sample_rate}Hz")
        
        return temp_path
    except Exception as e:
        print(f"Error processing audio: {e}")
        return audio_path

def clone_voice(ref_audio_path: str, ref_text: str, gen_text: str):

    # Initialize F5TTS
    f5tts = F5TTS()

    # Process audio
    processed_audio = resample_for_tts(ref_audio_path)

    # Generate audio - try different parameter combinations
    try:
        # Try minimal parameters first
        wav, generated_sample_rate, spect = f5tts.infer(
            ref_file=processed_audio,
            ref_text=ref_text,
            gen_text=gen_text,
            speed = .95, # .7 = 30% slower, .85 = 15% slower, 1 = normal, 1.15 = 15% faster, 1.3 = 30% faster
            nfe_step=64,  # NFE (Number of Function Evaluations) F5-TTS uses NFE=32 by default, but you can reduce this to NFE=7 with Sway Sampling for much faster generation with minimal quality loss
            cfg_strength=2.0  # Classifier-free guidance. CFG=2 is commonly used for better quality control (usually left off by default)
        )
    except TypeError as e:
        print(f"Trying alternative API signature: {e}")
        # Try with different parameter names
        wav, generated_sample_rate, spect = f5tts.infer(
            ref_audio=processed_audio,
            ref_text=ref_text,
            gen_text=gen_text
        )

    # Save output
    output_path = "cloned_voice_api_output.wav"
    sf.write(output_path, wav, generated_sample_rate)

    if os.path.exists(output_path):
        print(f"✅ API method successful! Output saved to: {output_path}")
        return output_path
    else:
        print("sound file generation failed - no output file generated")
        return None
            
def main():

    # Get user inputs
    print("\nVoice Cloning Setup:")
    
    # Reference audio file
    while True:
        ref_audio = input("Enter path to reference audio file (10-15 seconds): ").strip()
        if os.path.exists(ref_audio):
            break
        print("File not found. Please check the path.")
    
    # Reference text
    ref_text = input("Enter the text spoken in reference audio: ").strip()
    if not ref_text:
        print("Reference text is required for F5-TTS")
        ref_text = input("Please enter the reference text: ").strip()
    
    # Text to generate
    gen_text = input("Enter text to generate in cloned voice: ").strip()
    if not gen_text:
        gen_text = "Hello, this is a test of the cloned voice. How does it sound?"
    
    print(f"\nStarting voice cloning...")
    print(f"   Reference: {ref_audio}")
    print(f"   Reference text: '{ref_text}'")
    print(f"   Generate text: '{gen_text}'")
    
    # get the cloned audio
    output_file = clone_voice(ref_audio, ref_text, gen_text)
    
if __name__ == "__main__":
    try:
        main()
    except KeyboardInterrupt:
        print("\nInterrupted by user")
    except Exception as e:
        print(f"\n💥 Unexpected error: {e}")
        import traceback
        traceback.print_exc()

```
* `resample_for_tts` is necessary - the audio _must_ have a sample rate of 24kHz and it _must_ be mono (not stereo).  

> You will want to see the [settings section](learn_to_code/python/machine_learning/f5-tts?id=f5-settings).  

# F5 Settings  

F5-TTS has different settings:  
* `F5TTS()` settings  
  * `model=`  
    * These are pre-defined models  
    * There are native 'models' for F5-TTS:  
      * `F5TTS_v1_Base` - The current default model; can be loaded with `model='F5TTS_v1_Base'`
      * `F5TTS_Base` - Improved version  
      * `F5TTS_Small`  
      * `E2TTS_Base` - Original model  
      * `E2TTS_Small`  
  * `ckpt_file=`  
    * These are custom models  
    * Its possible to find different models on huggingface for F5-TTS - after you download them, you can put the path to it here (will probably be a .safetensors file)  
* `f5tts.infer()` setttings  
  * `speed` - .7 = 30% slower, .85 = 15% slower, 1 = normal, 1.15 = 15% faster, 1.3 = 30% faster  
    * F5-TTS usually makes the speakter talk fast, so...this is helpful to slow it down.  
    * The speed of the generated audio can depend heavily on the sample audio - if the sample talks fast, the generated audio will be _reall fast_.  
  * `nfe_step`  
    * NFE controls quality vs speed: Higher values give better quality but slower inference, default is 32
    * NFE=7: With Sway Sampling, can get close to 32-NFE quality  
      * In newer versions of F5-TTS, Sway Sampling is activated by default with NFE=7  
    * NFE=16: Faster but slightly lower quality  
      * In newer versions of F5-TTS, Sway Sampling is activated by default with NFE=16  
    * NFE=32: Default, best quality  
    * NFE=64: Can produce even better quality sound  
  * `cfg_strength` (Classifier-Free Guidance)  
    * Controls stability vs expressivity - Higher values give more stable output but less expressivity d 
    * Default is 2.0  
    * Settings  
      * CFG=1.0: More natural/expressive but less controlled  
      * CFG=2.0: Your setting - good balance (most common)  
      * CFG=3.0+: More stable but potentially robotic

---  

# Things I change  

Here are some things I change about the library in the back-end code.

## utils_infer.py  

The progress bar can be annoying. I changed this:
```Python  
    if streaming:
        for gen_text in progress.tqdm(gen_text_batches) if progress is not None else gen_text_batches:
            for chunk in process_batch(gen_text):
                yield chunk
    else:
        with ThreadPoolExecutor() as executor:
            futures = [executor.submit(process_batch, gen_text) for gen_text in gen_text_batches]
            for future in progress.tqdm(futures) if progress is not None else futures:
                result = future.result()
                if result:
                    generated_wave, generated_mel_spec = next(result)
                    generated_waves.append(generated_wave)
                    spectrograms.append(generated_mel_spec)
```

To This:
```Python  
    if streaming:
        for gen_text in gen_text_batches:
            for chunk in process_batch(gen_text):
                yield chunk
    else:
        with ThreadPoolExecutor() as executor:
            futures = [executor.submit(process_batch, gen_text) for gen_text in gen_text_batches]
            for future in futures:
                result = future.result()
                if result:
                    generated_wave, generated_mel_spec = next(result)
                    generated_waves.append(generated_wave)
                    spectrograms.append(generated_mel_spec)
```  

Also, I added a logger and changed all `print()` lines to a log line (although you will have to change some of the print lines so it uses f-string properly):  
```Python  
import logging

logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(funcName)s:%(lineno)d - %(message)s')
logger = logging.getLogger(__name__)
```

...and then converted all `print()` lines.  It should be noted that they used `show_info()` to print things in some cases - convert some of those to logger, too.  