# Kokoro  

[Kokoro](https://huggingface.co/hexgrad/Kokoro-82M) is a _FAST_ TTS, and seems to be very good at simple voice tasks. There aren't many bells a whistles, but it only takes just under ~2GB of VRAM to run. You cannot clone your voice wih it, but - its pretty solid. A go-to choice if all you want is a voice to read, and being fast is a priority.  

# Links  

* [Kokoro](https://huggingface.co/hexgrad/Kokoro-82M)
  * `git clone https://huggingface.co/hexgrad/Kokoro-82M`  
    * This seems to be more official than the github version  
  * `git clone https://github.com/hexgrad/kokoro.git`  
* [Overview](https://www.youtube.com/watch?v=LVINm5vUSW8)  
  * Although the install is already outdated  
* Some other voices I found on huggingface:  
  * I did not verfigy any of these for safety  
  * `git clone https://huggingface.co/Sethblocks/KokoroVoices`  
  * `git clone https://huggingface.co/onnx-community/Kokoro-82M-v1.1-zh-ONNX`  
    * For Onyx so may need some conversion
    * Seems mostly Mandarin / Chinese
  * The ONNX model file  
    * `wget https://github.com/thewh1teagle/kokoro-onnx/releases/download/model-files-v1.0/kokoro-v1.0.onnx` 
    * This is from `thewh1teagle` and not hexgrad, so...be careful.  
  * The voices file  
    * `wget https://github.com/thewh1teagle/kokoro-onnx/releases/download/model-files-v1.0/voices-v1.0.bin`  
    * This is from `thewh1teagle` and not hexgrad, so...be careful.  

# VRAM Footprint  

This seems to use about 2GB in VRAM for most applications.  

# Install  

> This uses [Anaconda / Conda](learn_to_code/python/conda); you can simply use a Python virtual environment if you wish, just swap out the `conda` commands for your equivalent virtual environment commands.  

!> If there are specific Python versions, library versions, etc, this is because as of the writing of this (August 2025) this is what specifically worked; it will probably change in the future.  

To install the <font color="green">Kokoro</font> library in Python:  


1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages)

3\. Install ffmpeg and espeak-ng: `apt-get install ffmpeg espeak-ng`  
  * `espeak-ng` is absolutely needed  

4\. Install Japanese-related installs: `apt-get install mecab libmecab-dev mecab-ipadic-utf8`  
  * only if you want to make audio in japanese - not entirely necessary otherwise.  
  * Frankly, I never got these to work  

5\. _Potential_ other libraries needed: `apt install portaudio19-dev python3-dev gcc`  
  * These MAY be needed - I am not sure.  
  * There was one install that said this was needed, but it wasnt the final install, so - skip this at first, and if it doesnt work, try installing these.  

6\. Exit out of root: `exit`  

7\. Delete the kokoro conda environment, if it exists: `conda env remove --name kokoro`  

8\. Create the Conda environment: `conda create --name kokoro python=3.12.11 numpy scipy`  
  * `scipy`  - For audio processing  

9\. Activate the Conda environment: `conda activate kokoro`  

10\. Install [torch](learn_to_code/python/machine_learning/pytorch?id=installing)  
  * You will need to install `torch torchvision torchaudio`  

11\. Install kokoro and soundfile - `pip install kokoro>=0.9.2 soundfile`  

12\. Install librosa - `pip install librosa`  
   * For the voice training - not strictly required, but its helpful   

13\. Install ordered_set pypinyin cn2an jieba: `pip install ordered_set pypinyin cn2an jieba`  
  * These are for chinese mandarin - skip if you do not care about these.  

14\. Install fugashi and misaki - `pip install 'fugashi[unidic-lite]' 'misaki[ja]'`  
  * These are for japanese, but - I couldnt get Japanese to work.  
  * These are not necessary (unless you need Japanese)  


# Language Codes 

| Language | Kokoro Code | misaki | espeak-ng fallback | Training Data |  
| --- | --- | --- | --- | --- |  
| American English | a | en | en-us |  |  
| British English | b | en | en-gb |  |  
| Spanish | e | en | es |  |  
| French | f | en | fr-fr | <11 hours |  
| Italian | i | en | it | H hours |  
| Brazilian Portuguese | p | en | pt-br |  |  
| Hindi | h | en | hi | H Hours |  
| Japanese | j | ja |  | H Hours |  
| Mandarin Chinesee | a | zh |  | H Hours |  
* You will use the Kokoro code in a line like `pipeline = KPipeline(lang_code='a')`.  

# Voices  

The voices are not saved in an obvious spot - for me, they were in: `~/.cache/huggingface/hub/models--hexgrad--Kokoro-82M/snapshots/f3ff3571791e39611d31c381e3a41a3af07b4987/voices`  
*  the `f3ff3571791e39611d31c381e3a41a3af07b4987` will be different for you  

This is HuggingFace Hub's caching system. This is actually a sophisticated approach that's becoming common with modern AI models.  

What it does: 
* Hugging Face Hub Cache: Kokoro downloads its model and voices from Hugging Face Hub  
* Git LFS + Blob Storage: The .pt files are stored as "blobs" (binary large objects) with content-addressed hashes  
* Symlinks: The human-readable filenames are symbolic links pointing to the actual blob files  
* Deduplication: Multiple models can share the same blobs, saving disk space  

## The Voices  

For up to date voices, see: https://huggingface.co/hexgrad/Kokoro-82M/blob/main/VOICES.md

For each voice, the given grades are intended to be estimates of the quality and quantity of its associated training data, both of which impact overall inference quality.

Subjectively, voices will sound better or worse to different people.

Support for non-English languages may be absent or thin due to weak G2P and/or lack of training data. Some languages are only represented by a small handful or even just one voice (French).

Most voices perform best on a "goldilocks range" of 100-200 tokens out of ~500 possible. Voices may perform worse at the extremes:

Weakness on short utterances, especially less than 10-20 tokens. Root cause could be lack of short-utterance training data and/or model architecture. One possible inference mitigation is to bundle shorter utterances together.
Rushing on long utterances, especially over 400 tokens. You can chunk down to shorter utterances or adjust the speed parameter to mitigate this.
Target Quality

How high quality is the reference voice? This grade may be impacted by audio quality, artifacts, compression, & sample rate.
How well do the text labels match the audio? Text/audio misalignment (e.g. from hallucinations) will lower this grade.
Training Duration

How much audio was seen during training? Smaller durations result in a lower overall grade.
10 hours <= HH hours < 100 hours
1 hour <= H hours < 10 hours
10 minutes <= MM minutes < 100 minutes
1 minute <= M minutes < 10 minutes


| Name | Simple Name | Language | Traits | Target Quality | Training Duration | Overall Grade | SHA256 |  
| --- | --- | --- | --- | --- | --- | --- | --- |  
| af_heart | heart | American English | woman❤️, mid voice |  |  | A | 0ab5709b|  
| af_alloy | alloy | American English | woman, low voice | B | MM minutes | C | 6d877149 |  
| af_aoede | aoede | American English | woman, mid-low voice | B | H hours | C+ | c03bd1a4 |  
| af_bella | bella | American English | woman🔥, mid-high voice | A | HH hours | A- | 8cb64e02 |  
| af_jessica | jessica | American English | woman, high voice | C | MM minutes | D | cdfdccb8 |  
| af_kore | kore | American English | woman, low voice | B | H hours | C+ | 8bfbc512 |  
| af_nicole | nicole | American English | woman🎧, whispering, mid voice | B | HH hours | B- | c5561808 |  
| af_nova | nova | American English | woman, mid voice | B | MM minutes | C | e0233676 |  
| af_river | river | American English | woman, mid-low voice | C | MM minutes | D | e149459b |  
| af_sarah | sarah | American English | woman, mid voice | B | H hours | C+ | 49bd364e |  
| af_sky | sky | American English | woman, mid-low voice | B | M minutes | C- | c799548a |  
| am_adam | adam | American English | man, black?, mid voice | D | H hours | F+ | ced7e284 |  
| am_echo | echo | American English | man, mid voice | C | MM minutes | D | 8bcfdc85 |  
| am_eric | eric | American English | man, mid-high voice | C | MM minutes | D | ada66f0e |  
| am_fenrir | fenrir | American English | man, effeminate, mid voice | B | H hours | C+ | 98e507ec |  
| am_liam | liam | American English | man, mid-low voice | C | MM minutes | D | c8255075 |  
| am_michael | michael | American English | man, low voice | B | H hours | C+ | 9a443b79 |  
| am_onyx | onyx | American English | man, low voice | C | MM minutes | D | e8452be1 |  
| am_puck | puck | American English | man, effeminate, mid-high voice | B | H hours | C+ | dd1d8973 |  
| am_santa | santa | American English | man, Santa | C | M minutes | D- | 7f2f7582 |  
| bf_alice | alice | British English, mid voice | woman | C | MM minutes | D | d292651b |  
| bf_emma | emma | British English | woman, mid-low voice | B | HH hours | B- | d0a423de |  
| bf_isabella | isabella | British English | woman, mid voice | B | MM minutes | C | cdd4c370 |  
| bf_lily | lily | British English | woman, mid-high voice | C | MM minutes | D | 6e09c2e4 |  
| bm_daniel | daniel | British English | man, low voice | C | MM minutes | D | fc3fce4e |  
| bm_fable | fable | British English | man, effeminate, mid-high voice | B | MM minutes | C | d44935f3 |  
| bm_george | george | British English | man, mid voice | B | MM minutes | C | f1bc8122 |  
| bm_lewis | lewis | British English | man, low voice | C | H hours | D+ | b5204750 |  
| ef_dora | ef_dora | Spanish | woman, mid-low voice |  |  |  | d9d69b0f |  
| em_alex | em_alex | Spanish | man, mid-low voice |  |  |  | 5eac53f7 |  
| em_santa | em_santa | Spanish | man' Santa |  |  |  | aa8620cb |  
| ff_siwis | siwis | French | woman, mid voice | B | <11 hours | B- | 8073bf2d |  
| if_sara | sara | Italian | woman, mid-high voice | B | MM minutes | C | 6c0b253b |  
| im_nicola | nicola | Italian | man, low voice | B | MM minutes | C | 234ed066 |  
| pf_dora | pf_dora | Brazilian Portuguese | woman, black?, mid-low voice |  |  |  | 07e4ff98 |  
| pm_alex | pm_alex | Brazilian Portuguese | man, mid-low voice |  |  |  | cf0ba8c5 |  
| pm_santa | pm_santa | Brazilian Portuguese | man, Santa |  |  |  | d4210316 |  
| hf_alpha | hf_alpha | Hindi | woman, mid-low voice | B | MM minutes | C | 06906fe0 |  
| hf_beta | beta | Hindi | woman, mid voice | B | MM minutes | C | 63c0a1a6 |  
| hm_omega | omega | Hindi | man, mid-low voice | B | MM minutes | C | b55f02a8 |  
| hm_psi | psi | Hindi | man, mid voice | B | MM minutes | C | 2f0f055c |  
| jf_alpha | jf_alpha | Japanese | woman, high voice, maybe child? | B | H hours | C+ | 1bf4c9dc  |  
| jf_gongitsune | gongitsune | Japanese | woman, mid voice | B | MM minutes | C | 1b171917 |  
| jf_nezumi | nezumi | Japanese | woman, mid-high voice | B | M minutes | C- | d83f007a |  
| jf_tebukuro | tebukuro | Japanese | woman, high voice, maybe child? | B | MM minutes | C | 0d691790 |  
| jm_kumo | kumo | Japanese | man, low voice | B | M minutes | C- | 98340afd |  
| zf_xiaobei | xiaobei | Mandarin Chinese | woman, mid-high voice | C | MM minutes | D | 9b76be63 |  
| zf_xiaoni | xiaoni | Mandarin Chinese | woman, high voice | C | MM minutes | D | 95b49f16 |  
| zf_xiaoxiao | xiaoxiao | Mandarin Chinese | woman, high voice | C | MM minutes | D | cfaf6f2d |  
| zf_xiaoyi | xiaoyi | Mandarin Chinese | woman, high voice, child? | C | MM minutes | D | b5235dba |  
| zm_yunjian | yunjian | Mandarin Chinese | man, mid-low voice | C | MM minutes | D | 76cbf8ba |  
| zm_yunxi | yunxi | Mandarin Chinese | man, mid-high voice | C | MM minutes | D | dbe6e1ce |  
| zm_yunxia | yunxia | Mandarin Chinese | man, high voice, child? | C | MM minutes | D | bb2b03b0 |  
| zm_yunyang | yunyang | Mandarin Chinese | man, mid-low voice | C | MM minutes | D | 5238ac22 |  
* You will set this in the pipeline object a la `generator = pipeline(text, voice='am_adam')`  
  * The pipeline object is set with the language code a la `pipeline = KPipeline(lang_code='a')`  
  * The text is the string that contains what will be turned to speech  


# Heart of Kokoro  

> This is a basic, working script for Kokoro - no bells and whistles, and many hard-coded values.  

```Python  
from kokoro import KPipeline
import soundfile as sf
import numpy as np

pipeline = KPipeline(lang_code='a') # the language code is set here (also, saving the object - in this case the 'pipeline' object - is also important)  

# Let's use a longer text to see multiple chunks
text = '''
Hello, this is the first sentence. This is the second sentence with different content. 
And here's a third sentence to demonstrate chunking. Finally, this is the last sentence.
'''

print("=== CHUNKING DEMONSTRATION ===")
print(f"Full text: {repr(text)}")
print("\n--- Processing chunks ---")

generator = pipeline(text, voice='am_santa') # The voice is set here - 'am_santa', but there are many, many others 
all_audio_chunks = []

for i, (gs, ps, audio) in enumerate(generator):
    print(f"\nChunk {i}:")
    print(f"  Graphemes (text): {repr(gs)}")
    print(f"  Phonemes (sounds): {repr(ps)}")
    print(f"  Audio shape: {audio.shape}")
    print(f"  Audio duration: {len(audio)/24000:.2f} seconds")
    
    # Save individual chunk
    sf.write(f'chunk_{i}.wav', audio, 24000)
    print(f"  Saved: chunk_{i}.wav")
    
    # Collect for combined file
    all_audio_chunks.append(audio)

# Combine all chunks into one file
print(f"\n=== COMBINING CHUNKS ===")
full_audio = np.concatenate(all_audio_chunks)
sf.write('full_speech.wav', full_audio, 24000)
print(f"Combined audio saved as 'full_speech.wav'")
print(f"Total duration: {len(full_audio)/24000:.2f} seconds")
print(f"Total chunks processed: {len(all_audio_chunks)}")

# Alternative: If you just want ONE file, you can do this:
print(f"\n=== SIMPLE APPROACH (One File) ===")
generator2 = pipeline(text, voice='am_santa')
all_audio = []
for i, (gs, ps, audio) in enumerate(generator2):
    all_audio.append(audio)

# Save as single file
combined = np.concatenate(all_audio)
sf.write('simple_output.wav', combined, 24000)
print("Saved everything as 'simple_output.wav'") 
```  

Notes:  
* Longer text is 'chunked', and needs to be joined (as it is above); this is why the for loop exists.  
* Kokoro's usage pattern makes the `pipeline` act as a callable object - you call it like a function.  
* Kokoro uses 'lazy loading', meaning - models are only loaded _if you actually use them_.  
  * This becomes apparent when you initially investigate `pipeline.voices`, which is a dictionary where the key is the 'voice' and the value is the tensor data for that voice.  
    * This list is initially empty, _until_ you load a voice - then it has once voice.  
    * This list is referenced _first_ to see if a voice is loaded - if its not here, it will attempt to download it and then load it.  

# Making Custom Voices  

While you cannot directly clone a voice, you _can_ mix existing voices to get novel voices. The trick is you have to load the voices _first_ - which gives you the tensors. Curiously, the default voices are _not_ initially loaded - they are loaded when you try to use them. Subsequent uses looks to see if the voice was loaded before - and if it was, it just uses that. Kokoro forces you to make a 'pipeline' object; its this object that you load voices into. For example, if you were using the default voice 'am_santa' (American Male Santa):  

```Python
voice_name = 'am_santa'

# Initialize pipeline
pipeline = KPipeline(lang_code=lang_code, repo_id=repo_id)
voice_tensor = pipeline.load_voice(voice_name)

test_text = f"This is American Male Santa from Kokoro. I hope you like it."
generator = pipeline(test_text, voice=voice_name)

audio_chunks = []
for _, _, audio in generator:
    if audio is not None:
        audio_chunks.append(audio)
    break
```

...and then `audio` would be your audio you could save to a file, play over speakers, etc. The part to note above is `voice_tensor = pipeline.load_voice(voice_name)` - this actually gives us a tensor which we can use to modify and then save our own voice tensor. Use the `load_kokoro_voices` To get all of the 'standard' voices:  

```Python
def load_kokoro_voices(lang_code:str, repo_id:str):
    """
    Load the default Kokoro voices - which also loads their tensors.
    Args:
         lang_code: the language code as defined by Kokoro. a = American English, British English = b, Spanish = e, French = f, Italian = i, Brazilian Portuguese = p, Hindi = h
         repo_id: The repo ID where the voices exist in HuggingFace. The default is hexgrad/Kokoro-82M
    Returns:
        A Tuple - (KPipeline (the Kokoro pipeline), a dictionary of the tensors associated with the working voices)
    """
    print("Loading Kokoro voices...")
    
    # Initialize pipeline
    pipeline = KPipeline(lang_code=lang_code, repo_id=repo_id)
    
    # Common Kokoro voice names - these are the 'default' names in Kokoro (i.e. the ones that are in the default repo)
    common_voices = ['af_alloy', 'af_aoede', 'af_bella', 'af_heart', 'af_jessica', 'af_kore', 'af_nicole', 'af_nova', 'af_river', 'af_sarah',
        'af_sky', 'am_adam', 'am_echo', 'am_eric', 'am_fenrir', 'am_liam', 'am_michael', 'am_onyx', 'am_puck', 'am_santa', 'bf_alice', 
    'bf_emma', 'bf_isabella', 'bf_lily', 'bm_daniel', 'bm_fable', 'bm_george', 'bm_lewis', 'ef_dora', 'em_alex', 'em_santa', 
    'ff_siwis', 'hf_alpha', 'hf_beta', 'hm_omega', 'hm_psi', 'if_sara', 'im_nicola', 'jf_alpha', 'jf_gongitsune', 'jf_nezumi', 
    'jf_tebukuro', 'jm_kumo', 'pf_dora', 'pm_alex', 'pm_santa', 'zf_xiaobei', 'zf_xiaoni', 'zf_xiaoxiao', 'zf_xiaoyi', 'zm_yunjian', 
    'zm_yunxi', 'zm_yunxia', 'zm_yunyang']

    print(f"Loading {len(common_voices)} common voice names...")
    
    voice_data = {}
    for voice_name in common_voices:
        #actually load the voice into Kokoro
        voice_tensor = pipeline.load_voice(voice_name)
        if voice_tensor is not None:
            #save the tensor information
            voice_data[voice_name] = voice_tensor

    return pipeline, voice_data
```

Once you have the voices, you can blend them with this method:  
```Python  
def create_voice_blend(pipeline, voice_data: dict, voices: list, weights: list, output_name:str = ""):
    """
    Create a voice blend, based on voices previously loaded into Kokoro.  
    
    Args:
        pipeline: The Kokoro pipeline.  
        voice_data: A dictionary, with the key being the voices, and the value being the tensor data for that voice
        voices: A list, containing the voices you wish to blend. The voice MUST be present in voice_data; the length of this list MUST equal that of the length of 'weights'.   
        weights: A list, containing the weights of the voices. The length MUST equal the length of 'voices', and the weights should, summed, equal 1 (you can do something else as its normalized, but best to keep the sum to 1).
        output_name: The name of the output file. Do not include an extension - this will be used for both a .wav and a .pt file.
    Returns:
         The name of the .pt file generated. 
    """
    print(f"Creating voice blend: {voices} with weights {weights}")
    
    if len(voices) != len(weights):
        print("Number of voices must match number of weights")
        return None
    
    # Normalize weights
    weights = np.array(weights)
    weights = weights / weights.sum()
    
    # Load voice tensors
    voice_tensors = []
    for voice_name in voices:
        if voice_name not in voice_data:
            print(f"Voice {voice_name} not available")
            return None
        voice_tensors.append(voice_data[voice_name])
    
    # Check all shapes match
    reference_shape = voice_tensors[0].shape
    for i, tensor in enumerate(voice_tensors):
        if tensor.shape != reference_shape:
            print(f"Shape mismatch: {voices[i]} has shape {tensor.shape}, expected {reference_shape}")
            return None
    
    # Create weighted blend - THIS IS THE PART THAT ACTUALLY DOES THE BLENDING
    blended_tensor = torch.zeros_like(voice_tensors[0])
    for tensor, weight in zip(voice_tensors, weights):
        blended_tensor += tensor * weight
    
    
    ## Normalize to keep in reasonable range
    #reference_std = voice_tensors[0].std()
    #if blended.std() > reference_std * 1.8:
    #    blended = (blended - blended.mean()) / blended.std()
    #    blended = blended * reference_std + voice_tensors[0].mean()
    #    print("   Applied normalization")
    
    # Save blend
    filename = f"{output_name}.pt"
    torch.save(blended_tensor, filename)
    print(f"PyTorch Pickle File: {filename}")
    
    # Test the blend
    try:
        test_voice_name = f"{output_name}"
        pipeline.voices[test_voice_name] = blended_tensor

        test_text = f"This is a voice blend of {', '.join(replace_prefix(voice) for voice in voices)}. I hope you like it."
        generator = pipeline(test_text, voice=test_voice_name)
        
        audio_chunks = []
        for _, _, audio in generator:
            if audio is not None:
                audio_chunks.append(audio)
            break
        
        if audio_chunks:
            full_audio = np.concatenate(audio_chunks)
            test_filename = f"{output_name}.wav"
            sf.write(test_filename, full_audio, SAMPLE_RATE)
            print(f"Test audio: {test_filename}")
            
            del pipeline.voices[test_voice_name]
            return filename
        else:
            print("No test audio generated")
            return None
            
    except Exception as e:
        print(f"Test failed: {e}")
        return None
```
* As you can see, I commented out the normalization.  
  * This works fine for some blending, but if you start trying to transform the voices - pitch, etc - you will almost certainly need to normalize, in some fashion.  


# Using Custom Voices  

While you cannot directly clone a voice, you _can_ mix existing voices to get novel voices - this is not described here ([see above](/learn_to_code/python/machine_learning/kokoro-tts?id=making-custom-voices)), but basically, you either make or find a .pt (PyTorch) file online that is specifically tailored to Kokoro; then, you load the file to Kokoro via:

```Python
...
voice_data = torch.load('/the/path/to/your.pt')
pipeline.voices['unique_name'] = voice_data
...
```
* `unique_name` can be anything  
* `pipeline` is constructed in the same fashion as [the pipeline in the heart of the code](learn_to_code/python/machine_learning/kokoro-tts?id=heart-of-kokoro)  

Then, later, you just...reference that name when generating the text:  
```
...  
generator = pipeline(text, voice='unique_name')  
...  
```  

