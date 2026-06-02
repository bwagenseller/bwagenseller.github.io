# WhisperX  

# Warning  

!> I got most of this information from [Google's Gemini](https://gemini.google.com/) - I edited some notes here and there for brevity / injecting things I learned, but some of this is directly from its LLM output.  

# What is ASR  

Automatic Speech Recognition (ASR) typically refers to a model that can take speech in an audio file (.wav, etc) and convert it to its text equivalent.  

# What is Whisper  

Whisper refers to the original Automatic Speech Recognition (ASR) model open-sourced by OpenAI. It's a powerful, general-purpose ASR model trained on a massive dataset of diverse audio.  

Key Characteristics:  
* **Core Function**: Transcribes spoken audio into text. It's designed to be robust to various accents, background noise, and technical language.  
* **Timestamping**: Provides segment-level timestamps. This means it tells you the start and end time of a chunk of speech (e.g., a sentence or a few words), but not the precise start and end time of each individual word. These segment timestamps can sometimes be inaccurate or "drift" over long audio files.  
* **Speed**: While accurate, the original OpenAI Whisper implementation can be slower, especially for long audio files, as it processes audio in 30-second chunks sequentially. It doesn't natively support batching for parallel processing of multiple segments.  
* **Features**: Primarily focused on transcription and translation (translating speech from any supported language into English). It does not natively include features like speaker diarization.  
* **Implementations**: The official OpenAI Python package (openai-whisper) is one way to use it. Many other projects (like whisper.cpp, faster-whisper, transformers library implementations) are built around this core model.  

That said, I use `WhisperX`. `WhisperX` is an open-source project (developed by m-bain on GitHub) that builds upon the OpenAI Whisper model and adds several significant enhancements to address its limitations, particularly for long-form audio and advanced use cases.

Key Enhancements & Differences:  
* **Accurate Word-Level Timestamps (Forced Alignment)**: While OpenAI Whisper gives segment-level timestamps, WhisperX integrates a separate alignment model (like wav2vec2) to perform "forced alignment."  
  * This process takes the transcribed text and the original audio, then precisely aligns each word to its exact start and end time in the audio.  
  * This is crucial for applications requiring high precision, like subtitle generation, karaoke, or detailed audio analysis.  
  * This is the most significant difference.  
* **Speaker Diarization**: WhisperX integrates with pyannote.audio to perform speaker diarization.  
  * This means it can identify and label different speakers in an audio file (e.g., "SPEAKER_00: Hello there." "SPEAKER_01: How are you?"). This is invaluable for transcribing conversations, meetings, or podcasts.  
* **Improved Speed and Efficiency (Faster-Whisper & CTranslate2 Backend)**: WhisperX uses faster-whisper as its backend, which in turn leverages CTranslate2.  
  * CTranslate2 is an optimized C++ library that compiles the Whisper model into a more efficient format. This results in:  
    * Significantly faster inference speed: Often 4x to 70x faster than the original OpenAI Whisper implementation, especially with large-v2 models and batching.  
    * Reduced VRAM usage: Making it more feasible to run larger models on consumer GPUs.  
    * Batched Inference: WhisperX can process multiple audio chunks in parallel (batching), which is a key factor in its speed advantage for long recordings.  
* **Voice Activity Detection (VAD) Pre-processing**: WhisperX incorporates a VAD step that identifies segments where speech is actually present and ignores silence.  
  * This helps reduce "hallucinations" (where the ASR model generates text during silence) and allows for more efficient processing, particularly with batching.  
* **Robustness for Long Audio**: By combining VAD, optimized backend, and better handling of segments, WhisperX is generally much more stable and performs better for transcribing very long audio files (hours long) compared to the original Whisper.  

> For most practical applications requiring more than just basic transcription (especially if you need precise timing or speaker separation), `WhisperX` is the preferred choice.  

# Install and Setup  

!> Whisper is _very_ temperamental in library usage, etc - in most cases you _will_ need _specific_ versions of packages, and in some cases even specific versions of Python itself. it's strongly advised to use an environment like [Conda](learn_to_code/python/conda), but you can use Python's native virtual environment functionality, too.  

Here is how I installed whisper (really, whisperx). 

## Cuda Setup  

If you have a Nvidia GPU, you _must_ [set up CUDA](learn_to_code/python/machine_learning/pytorch?id=cuda-setup); you can do this without a GPU, but it will be very slow.  


## Whisperx Setup  

> Do not do this as root.  

After you have [installed CUDA](learn_to_code/python/machine_learning/whisperx?id=cuda-setup) you can set up your whisperx environment  

1\. Create your [conda environment](learn_to_code/python/conda): `conda create --name stt python=3.12 numpy pandas pyarrow fastparquet`  
* Yo do not have to use Conda; if you wish, you can use Python's native virtual environment functionality, too.  
* I named my environment `stt` (speech to text), but you are welcome to name it whatever you wish.  
* As of July 2025, you want to go no higher than python 3.12 - Python 3.13.5 was bombing out on me as the dependencies used by whisperx need 3.12 (or lower, but not too low).  
* While not strictly required, if you want to simulate streamed speech, `numpy` is required for audio buffer manipulation.  
  * The other packages - `pandas pyarrow fastparquet` - are optional (I like using them as a base, though).  


2\. Switch to your Conda environment: `conda activate stt`  
* I named my environment `stt` (speech to text), but you are welcome to name it whatever you wish.  
* Yo do not have to use Conda; if you wish, you can use Python's native virtual environment functionality, too.  

3\. Install `sounddevice` (if you want to simulate streaming via your microphone): `pip install sounddevice`  
* The audio buffer manipulation will also require `numpy`.  

4\. Install `webrtcvad-wheels` (if you want to simulate streaming via your microphone): `pip install webrtcvad-wheels`  
* This will allow you to use Voice Activity Detection (VAD), which allows for intelligent chunking of data when you simulate streaming.

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

7\. Install whisperx: `pip install whisperx`  

8\. In my case, as of July 2025, Whisperx installed an old version of `ctranslate2` that expects an _old_ version of CudNN - I had to upgrade to version 4.5.0 a la: `pip install ctranslate2==4.5.0`  
* Technically this version is out of the requirements range for whisperx, but you are stuck otherwise: the older version of CudNN is not available, and besides, you never want to install someting that is too ancient, expecially if it has to coexist with other libraries (i.e. the CUDA toolkit)  
  * That said, version 4.5.0 _should work_, according to Google Gemini, as its _just_ outside of the recommended range.  

> At this point, `whisperx` should be set-up properly; remember the command `nvidia-smi` to get your CUDA version (or to just check out the health of your GPU), and if you get stuck, uninstall everything with `pip uninstall whisperx ctranslate2 pyannote.audio` to try again.  

## Altered Scripts  

After the install, there are a few...annoying...aspects about the various installed python files; mostly obnoxious 'print' lines. I like to change these. You dont have to, but in case you do...

**<font color="black" size="6">pyannote.py, silero.py</font>**  

If you are using the whisperx library to simulate streaming, its entirely possible that there will be no words spoken for some segments; however, whisperx loudly tells you this is the case. To stop this, go into these files:
* `<CONDA_ENV>/lib/python3.12/site-packages/whisperx/vads/pyannote.py`
* `<CONDA_ENV>/lib/python3.12/site-packages/whisperx/vads/silero.py`

And comment out lines that say this: `print("No active speech found in audio")`  

**<font color="black" size="6">asr.py</font>**  

Unfortunately, whisperx does not return the confidence score with detected languages, even though whisper does. If you need confidence scores for the detected language, go into the file `<CONDA_ENV>/lib/python3.12/site-packages/whisperx/asr.py`, find the `detect_language` method, copy it, paste that under `detect_language`, and rename it (I like `detect_language_with_probability`). Mine looks like:
```Python
    def detect_language_with_probability(self, audio: np.ndarray) -> str:

        if audio.shape[0] < N_SAMPLES:
            print("Warning: audio is shorter than 30s, language detection may be inaccurate.")
        model_n_mels = self.model.feat_kwargs.get("feature_size")
        segment = log_mel_spectrogram(audio[: N_SAMPLES],
                                      n_mels=model_n_mels if model_n_mels is not None else 80,
                                      padding=0 if audio.shape[0] >= N_SAMPLES else N_SAMPLES - audio.shape[0])
        encoder_output = self.model.encode(segment)
        results = self.model.model.detect_language(encoder_output)
        language_token, language_probability = results[0][0]
        language = language_token[2:-2]
        #print(f"Detected language: {language} ({language_probability:.2f}) in first 30s of audio...")
        return language, language_probability
```

I took out an offensive print line and added in `language_probability` to the return.  

**<font color="black" size="6">alignment.py</font>**  

If something fails in the alignment of a segment, `<CONDA_ENV>/python3.12/site-packages/whisperx/alignment.py` will occasionally say `Failed to align segment ("{segment["text"]}"): backtrack failed, resorting to original...'`.  It does happen occasionally, and you are welcome to suppress it by commenting it out, but I usually leave it in.  


**<font color="black" size="6">reproducibility.py</font>**  

pyannote.audio is an open-source Python toolkit built on PyTorch, primarily focused on speaker diarization. Speaker diarization is the process of partitioning an audio stream into homogeneous segments according to speaker identity. In simpler terms, it answers the question: "Who spoke when?"  

It provides a suite of neural building blocks and pre-trained models for various sub-tasks critical to speaker diarization, including:  
* **Voice Activity Detection (VAD)**: Detecting when someone is speaking versus silence or background noise.  
* **Speaker Change Detection**: Identifying points in the audio where a different speaker starts talking.  
* **Overlapped Speech Detection**: Recognizing periods where multiple speakers are talking simultaneously.  
* **Speaker Embedding**: Generating unique numerical representations (embeddings) of a speaker's voice, which allows the system to distinguish between different individuals.  

pyannote.audio then combines these components into a full speaker diarization pipeline that takes an audio file as input and outputs a timeline indicating who spoke when. This is incredibly useful for tasks like:  
* **Transcribing multi-speaker conversations**: When integrated with a speech-to-text (ASR) system like Whisper (as whisperx does), it can attribute transcribed text to specific speakers, making transcripts much more readable and useful.  
* **Meeting summarization**: Quickly identifying contributions from different participants.  
* **Indexing and searching audio**: Enabling searches for specific speakers within a large audio archive.  
* **Analyzing conversational dynamics**: Understanding turn-taking, overlaps, and speaking patterns.  

Now that you know what pyannote,audio does, we can talk about its annoying feature. By default, pyannote disables TensorFloat-32 (TF32) as it might lead to reproducibility issues and lower accuracy.  

For more modern Nvidia GPUs, especially for the Ampere, Ada Lovelace, and Blackwell architectures - you will probably see a performance boost in ML tasks if this is enabled. Its suggested that you should enable it if you have a more modern GPU, and if there are accuracy issues, disable it again.

For most AI/ML tasks, having this enabled is a benefit, but apparently not for pyannote - and it FORCES you to turn it off and does NOT re-enable it for you after its done.

`<CONDA_ENV>/lib/python3.12/site-packages/pyannote/audio/utils/reproducibility.py` can CONSTANTLY complain about this. I guess many things turn it on, and pyannote turns it off explicitly:
```Python
def fix_reproducibility(device: torch.device):
    if (device.type == "cuda") and (
        torch.backends.cuda.matmul.allow_tf32 or torch.backends.cudnn.allow_tf32
    ):
        torch.backends.cuda.matmul.allow_tf32 = False
        torch.backends.cudnn.allow_tf32 = False
        warnings.warn(
            ReproducibilityWarning(
                "TensorFloat-32 (TF32) has been disabled as it might lead to reproducibility issues and lower accuracy.\n"
                "It can be re-enabled by calling\n"
                "   >>> import torch\n"
                "   >>> torch.backends.cuda.matmul.allow_tf32 = True\n"
                "   >>> torch.backends.cudnn.allow_tf32 = True\n"
                "See https://github.com/pyannote/pyannote-audio/issues/1370 for more details.\n"
            )
        )
```

Since I know its going to do it, I just comment out the warning it raises. I try to manually turn it back on (for other tasks) but putting this in the VERY beginning of scripts (after the imports):
```Python
# ---------------------------------------------------------------------
# Place these lines IMMEDIATELY after importing torch
# This enables TF32 for your NVIDIA RTX 5090 for performance
# By default, TensorFloat-32 (TF32) has been disabled as it might lead to reproducibility issues and lower accuracy.
# However, for more modern Nvidia GPUs, especially for the Ampere, Ada Lovelace, and Blackwell architectures - you will probably see a performance boost
# If, however, there are accuracy issues, do not enable these

torch.backends.cuda.matmul.allow_tf32 = True
torch.backends.cudnn.allow_tf32 = True
# ---------------------------------------------------------------------
```  


# Models  

## WhisperX Models  

> Even for the largest models, WhisperX models only take about ~10GB of VRAM.  

These are the core Automatic Speech Recognition (ASR) models that perform the initial transcription of audio into text. whisperx uses a highly optimized version of OpenAI's Whisper model (via faster-whisper and CTranslate2) as its base.  
* Primary Function: To convert spoken audio into a written transcript. They are trained on vast amounts of audio and text data to recognize speech patterns and map them to words.  
* Output: The direct output of a base Whisper model is typically a sequence of words or segments of text. While the original Whisper model does provide segment-level timestamps, they are often not precise enough for individual words and can be off by several seconds.  
* Model Sizes: Available in various sizes (e.g., tiny, base, small, medium, large-v2, large-v3), with larger models offering higher accuracy at the cost of increased computational resources (VRAM and inference time).  
* Language Specificity: Can be English-only (e.g., medium.en) for slightly better accuracy on English, or multilingual (e.g., medium, large-v3) to handle various languages.  
* Example: When you call whisperx.load_model("medium.en", ...), you are loading one of these base ASR models.  

`whisperx` automatically downloads models the first time it runs, and you can specify which models it uses.  

When you call `whisperx.load_model("medium.en", ...)`, whisperx looks for the "medium.en" Whisper ASR model. If it doesn't find it in your local cache (`~/.cache/huggingface/hub/` and `~/.cache/torch/hub/`), it downloads it automatically from Huggingface.  

Models:  

| Model Family | Size (Parameters) | Multilingual? | Primary Benefit | Notes |  
| --- | --- | --- | --- | --- |  
| tiny(.en) | 39M | Yes/No | Fastest, Smallest | tiny.en is English-only and generally better for English, while tiny is multilingual. |  
| base(.en) | 74M | Yes/No | Good balance of speed/accuracy | base.en is English-only and generally better for English, while base is multilingual. |  
| small(.en) | 244M | Yes/No | Higher accuracy than base | small.en is English-only, while small is multilingual. |  
| medium(.en) | 769M | Yes/No | Even higher accuracy | medium.en is English-only, while medium is multilingual. |  
| large / large-v1 | 1.55B | Yes | Highest accuracy | Original large model. |  
| large-v2 | 1.55B | Yes | Improved accuracy over large | Recommended over large due to additional training. |  
| large-v3 | 1.55B | Yes | State-of-the-art accuracy | Latest general-purpose large model with improvements in multilingual performance and input processing. |  
| distil-xxx <br> (-large-v2, -medium.en, <br>-small.en, -large-v3) | Varies (e.g., `~`750M for distil-large) | Mostly No (English-focused) | Significantly Faster, Smaller | Distilled versions (e.g., distil-large-v3 is faster and smaller than large-v3), with minimal accuracy drop. Primarily for English. |  
| large-v3-turbo / turbo | Pruned large-v3 (`~`800M) | Yes | Very Fast, High Accuracy | Optimized large-v3 with fewer decoder layers for speed. Accuracy comparable to large-v2, but faster than all other large models. May have reduced translation performance. |  

Choosing the right model depends on your specific needs:  
* Highest Accuracy (for any language): large-v3  
* Best English Accuracy with moderate resources: medium.en or small.en  
* Fastest Transcription (English): distil-small.en or distil-medium.en  
* Fastest Transcription (Multilingual) with good accuracy: large-v3-turbo (or turbo)  
* Balancing Accuracy and Speed: base.en (for English) or base (multilingual) can be a good starting point.  

To load a model (using `large-v3` as an example): `model = whisperx.load_model("large-v3", device="cuda", compute_type="float16")`  

As stated, the usual spot to save models is `~/.cache/huggingface/hub/` and `~/.cache/torch/hub/`; however, you can generally point whisperx to a different cache directory using environment variables like `HF_HOME` or by passing a `download_root` argument to `whisperx.load_model()`:  
```Python  

import os
os.environ["HF_HOME"] = "/path/to/your/custom/model/cache"
# Or:
# model = whisperx.load_model("large-v3", device=device, compute_type=compute_type, download_root="/path/to/your/custom/model/cache")
```  

> You primarily interact with model names, and whisperx handles the download and conversion details.  

**<font color="black" size="6">Further Model Detail</font>**  

* **tiny.en, tiny**  
  * Size: Smallest models (39M parameters).  
  * Speed: Fastest.  
  * Accuracy: Lowest among the Whisper family.  
  * tiny.en: English-only version, generally better for English transcription than the multilingual tiny.  
  * tiny: Multilingual, can transcribe and translate many languages.  
* **base.en, base**  
  * Size: Larger than tiny (74M parameters).  
  * Speed: Faster than larger models, but slower than tiny.  
  * Accuracy: Improved over tiny.  
  * base.en: English-only version, offering better English performance.  
  * base: Multilingual.  
* **small.en, small**  
  * Size: (244M parameters).  
  * Speed: Moderate.  
  * Accuracy: Good for general use.  
  * small.en: English-only version.  
  * small: Multilingual.  
* **medium.en, medium**  
  * Size: (769M parameters).  
  * Speed: Slower than smaller models.  
  * Accuracy: Very good.  
  * medium.en: English-only version.  
  * medium: Multilingual.  
* **large, large-v1, large-v2, large-v3**  
  * Size: Largest models (1.55B parameters).  
  * Speed: Slowest inference time but offer the highest accuracy.  
  * Multilingual: These are exclusively multilingual models; there are no .en versions of large models. They are trained on a vast amount of diverse audio data across many languages.  
  * Version Improvements:  
    * large: The original large model.  
    * large-v1: An early iteration or alias for the original large.  
    * large-v2: An improved version of large, trained for more epochs with regularization, generally recommended over the original large due to better performance.  
    * large-v3: The most recent general-purpose large model from OpenAI.  
      * It shows improved performance across a wide variety of languages, often with a 10-20% reduction in errors compared to large-v2.    
      * It also has minor architectural changes like using 128 Mel frequency bins instead of 80 for input spectrograms and adding a new language token for Cantonese.  
* **distil-large-v2, distil-medium.en, distil-small.en, distil-large-v3**  
  * These models are created to offer a better balance of speed and accuracy, particularly for resource-constrained environments or applications where speed is paramount.  
  * Concept: These are "distilled" versions of the larger Whisper models, primarily developed by Hugging Face.  
    * Knowledge distillation is a technique where a smaller, faster model (the student) is trained to mimic the behavior of a larger, more accurate model (the teacher).  
  * Benefits: They are significantly faster (e.g., distil-large-v2 can be 6x faster) and smaller (e.g., ~50% fewer parameters) than their full Whisper counterparts.  
  * Accuracy: They maintain accuracy within a small margin (e.g., 1% Word Error Rate difference) of the larger models they were distilled from.  
  * Language: Currently, many Distil-Whisper models are optimized for English-only speech recognition, although multilingual distilled checkpoints are being developed.  
* **large-v3-turbo, turbo**  
  * These models are created to offer a better balance of speed and accuracy, particularly for resource-constrained environments or applications where speed is paramount.  
  * Concept: These are optimized versions of large-v3 released by OpenAI (and often synonymous, with turbo being a shorthand).  
  * Key Difference: They achieve significant speed improvements by reducing the number of decoder layers (from 32 in large-v3 down to 4, similar to the tiny model).  
  * Benefits: Much faster than large-v3 while largely maintaining accuracy similar to large-v2.  
  * Trade-offs: While still very good for transcription, their performance for translation may be degraded compared to the full large-v3 because they were fine-tuned excluding translation data.  
    * They are designed to offer a "best of both worlds" balance between speed and accuracy for ASR.  


## Alignment Models  

This is a separate model (often a wav2vec2 variant) that whisperx uses for forced alignment to get precise word-level timestamps. When you call `whisperx.load_align_model(language_code="en", ...)`, it downloads a suitable pre-trained alignment model for that language (like `wav2vec2_fairseq_base_ls960_asr_ls960.pth` for English) if it's not already cached.  

Alignment models are specialized models used by whisperx for forced alignment. They take an existing text transcript (produced by the base Whisper model) and the original audio, then precisely determine the start and end timestamps for each individual word within that audio:  
* Primary Function: To accurately synchronize a given transcript with the audio.  
  * They typically work by identifying phonemes (the smallest units of sound that distinguish words) within the audio and matching them to the phoneme sequence of the words in the transcript.  
* Output: Precise word-level timestamps (start and end times for each word).  
* Underlying Technology: whisperx commonly uses wav2vec2 models (a type of phoneme-based ASR model) for this purpose.  
* Language Specificity: Alignment models are language-specific. A separate alignment model is downloaded and used for each language you're processing (e.g., a specific wav2vec2 model for English, another for German, etc.).  
  * `whisperx` would download a different, corresponding alignment model for that language when you specify `language_code="de"` or `language_code="fr"` in `whisperx.load_align_model()`.  
* Necessity for whisperx: The key innovation of whisperx is its use of these external alignment models to overcome the less precise timestamps of the original Whisper ASR models.  
* Example: When you call `whisperx.load_align_model(language_code="en", ...)`, you're loading this type of model.  
* Potential for Custom Alignment Models: While whisperx tries to automatically pick the best default, advanced users can specify a different wav2vec2 model from Hugging Face if they believe it performs better for their specific use case or language, especially for languages that might not have a "default" built-in alignment model. This is done by passing the `model_name` argument to `whisperx.load_align_model()`:  

```Python

# Example for using a custom Swedish alignment model from Hugging Face
align_model, metadata = whisperx.load_align_model(
    language_code="sv",
    device="cuda",
    model_name="KBLab/wav2vec2-large-voxrex-swedish" # A specific Hugging Face model
)
```  

# Heart of WhisperX  

> WhisperX _exclusively_ uses a 16 kHz sample rate - anything more and it will downsample. Its probably wise to sample your audio using 16 kHz from the start.  


This is a snippet of [a streaming example for WhisperX](learn_to_code/python/machine_learning/whisperx?id=streaming), which focuses on the heart of the code:  
```Python  
# Use the .detect_language() method of the asr_model object
# This returns a tuple: (language_code, {language_code: probability_score})
# where the second element is a dictionary of all language probabilities.
# We're interested in the probability of the *detected* language.

# Pad or trim the audio for language detection if it's not already 30s
# Whisper's language detection is optimized for 30s chunks.
# Convert to appropriate format for detect_language
audio_for_language_detection = whisperx.audio.pad_or_trim(audio_segment.astype(np.float32))

# It seems for whisper, the detect_language method returns the detected language and a dictionary of probabilities for all detected languages, with the first element is the most probable language code string.
# The second element is a dictionary mapping language codes to their probabilities.
# Unfortunately, whisperx only returns the language, not the score nor the other language probabilities - but you CAN go into the backend python method 'detect_language', remove the annoying print line, and have it expressly return language_probability 
# (you will note it did not before, and it should have). I didnt modify it directly, but I made a new one (in case other calling things would call this and expect just the language to be returned) in <CONDA_ENV>/lib/python3.12/site-packages/whisperx/asr.py; 
# I named it 'detect_language_with_probability', where the official one is simply 'detect_language' (so if you are copying this, you will have to either make 'detect_language_with_probability' or just use 'detect_language' with no probability returned).
detected_language, language_probability = asr_model.detect_language_with_probability(audio_for_language_detection)

# Print the detected language
print(f"🌍 Detected language: {detected_language} {language_probability}")

# Now perform the transcription, explicitly passing the detected language.
#    This suppresses the "Warning: audio is shorter than 30s..." message
#    and guides the model for better transcription of the detected language.
result = asr_model.transcribe(audio_segment, batch_size=1, language=detected_language) 
# The 'result' has a few pieces:  
# * 'segments' - a list of dictionaries, with each dictionary containing 'text' (the entire spoken segment), 'start' (the start of the entire segment in the string), and 'end' (the end of the entire segment in the string)
# * 'language' - the language code (i.e. 'en')
# * does NOT have 'language_probability' - apparently this is in whisper, but it may NOT be in whisperx? Sad!
# * Note this does NOT seem to have confidence scores or individual word start/end times - that seems to come from 'whisperx.align'

# Process and print the transcription results.
# First, check if the ASR model actually detected any speech segments.
# `result` might be empty or not contain "segments" if no speech was confidently detected.
if result and "segments" in result and result["segments"]:
    # If segments are found, proceed with forced alignment to get precise word timestamps.
    # All necessary parameters (`segments`, `align_model`, `metadata`, `audio_segment`, `device`)
    # are passed to `whisperx.align()`.
    word_segments_result = whisperx.align(result["segments"], align_model, metadata, audio_segment, device=DEVICE)
    # word_segments_result is built of
    # * segments  - like above, still containing 'start/end/text but now has a 'words' list of dictionaries, one for every word spoken
    #   * 'words' dictionary - for every entry:
    #     * 'word' - the word
    #     * 'start' - the start time of the word, relative to the segment beginning
    #     * 'end' - the end time of the word, relative to the segment beginning
    #     * 'score' - the confidence score behind the word
    # * 'word_segments' dictionary - seems to be the same as the words dictionary, just...outside of the 'segments' dictionary

    
    # After alignment, check if `word_segments_result` actually contains
    # precise word-level segments. It might be empty if alignment failed or
    # if the original ASR segments were too short/noisy for confident alignment.
    if word_segments_result and "word_segments" in word_segments_result and word_segments_result["word_segments"]:
        full_text = ""
        # Iterate directly over the word segments list.
        # Each 'word_info' will directly be a dictionary like {'word': 'going', 'start': ..., 'end': ...}
        for word_info in word_segments_result["word_segments"]:
            if "word" in word_info: # Check if the 'word' key exists in the current word_info dictionary
                full_text += f"{word_info['word']} "

                #There are other things you can use too - start, end, (confidence) score. The start and end are relative to the segment
                #full_text += f"{word_info['word']} ({word_info['start']}/{word_info['end']}) ({word_info['score']}) "
            else:
                # This warning is a safety check for a malformed word_info, less likely now.
                print(f"Warning: 'word' key missing in word_info: {word_info}", flush=True)

                    
        # If `full_text` is not empty after stripping whitespace, print the transcription.
        if full_text.strip():
            print(f"🎤 You said: {full_text.strip()}", flush=True)
    else:
        # This path is executed if ASR found speech, but alignment couldn't produce words.
        # Suppressed for cleaner output.
        pass # print("... (alignment found no words in detected speech) ...", flush=True)
else:
    # This path is executed if WhisperX's ASR model itself detected no speech
    # in the current segment passed from the VAD thread.
    # Suppressed for cleaner output.
    pass 

```  

The comments in the code above explain things well, but a closer look at an example of a dictionary returned by `whisperx.align`, where the spoken sentence was `This is a test.`:  
```
  {'segments': 
      [{'start': 0.031, 'end': 2.447, 'text': ' This is a test.', 
          'words': [
              {'word': 'This', 'start': np.float64(0.031), 'end': np.float64(1.676), 'score': np.float64(0.942)}, 
              {'word': 'is', 'start': np.float64(1.737), 'end': np.float64(1.798), 'score': np.float64(0.642)}, 
              {'word': 'a', 'start': np.float64(1.879), 'end': np.float64(1.899), 'score': np.float64(0.769)}, 
              {'word': 'test.', 'start': np.float64(1.96), 'end': np.float64(2.447), 'score': np.float64(0.877)}
          ]
      }]
  , 'word_segments': 
      [
          {'word': 'This', 'start': np.float64(0.031), 'end': np.float64(1.676), 'score': np.float64(0.942)}, 
          {'word': 'is', 'start': np.float64(1.737), 'end': np.float64(1.798), 'score': np.float64(0.642)}, 
          {'word': 'a', 'start': np.float64(1.879), 'end': np.float64(1.899), 'score': np.float64(0.769)}, 
          {'word': 'test.', 'start': np.float64(1.96), 'end': np.float64(2.447), 'score': np.float64(0.877)}
      ]}
```  

---  

# Examples  

## Streaming  

> This code was initially written by Google Gemini, with some comments by me.  

Here is an example of how you can use WhisperX via 'streaming' - what it does is it uses 'Voice Activity Detection' (VAD) to determine when you are done talking (via low volume level for 800 milliseconds), makes a 'file' of the buffered audio, and then sends that to whisperX for processing. This also can return timestamps and confidence scores for _every word_ (which is the entire point of WhisperX over Whisper).  

```Python  
import sounddevice as sd # Library for recording and playing audio from devices like microphones.
import numpy as np       # Numerical computing library; essential for handling audio data as arrays efficiently.
import whisperx          # The enhanced Whisper ASR library, optimized for speed and precise timestamps.
import queue             # Module for implementing multi-producer, multi-consumer queues; crucial for safe
                         # data exchange between different threads without race conditions.
import threading         # Module for managing separate threads of execution, allowing concurrent tasks
                         # (e.g., recording audio, processing VAD, transcribing) to run simultaneously.
import time              # Module for time-related functions; used for introducing small delays and
                         # for calculating time durations if needed.
import os                # Module for interacting with the operating system; not heavily used here,
                         # but generally useful for file paths, environment variables, etc.
import gc                # Garbage Collector interface; useful for explicitly prompting Python to
                         # reclaim unused memory, especially important in long-running applications
                         # that handle large data (like audio) or models.
import torch             # PyTorch deep learning library; used for model operations (loading, inference)
                         # and for checking GPU (CUDA) availability.
import webrtcvad         # WebRTC Voice Activity Detector; a highly efficient library for distinguishing
                         # speech from non-speech (silence, noise) in audio.


"""
I got this from Google Gemini.

This simulates streaming by chunking the audio into segments; it utilizes VAD (Voice Activity Detector) to determine silence, and if there is an 800 millisecond chunk of time that is silence (configurable), the segment is determined to have ended and is 
sent to the whisperx model for processing / to transcribe it. 

This is an improvement off of the 'streaming.py' file.
"""

# --- Configuration ---
# These variables configure how the real-time transcription process will behave.
# Adjust them based on your hardware, desired accuracy, and latency requirements.

# Specifies the Whisper ASR model to use. Smaller models like "base.en" or "small.en"
# are generally preferred for real-time streaming as they offer lower latency and faster inference,
# even if slightly less accurate than larger models.
#WHISPER_MODEL_NAME = "base.en"
WHISPER_MODEL_NAME = "large-v3"

# Determines the computational device for WhisperX models.
# `torch.cuda.is_available()` is the standard PyTorch way to check if a CUDA-enabled GPU
# is detected and properly configured. If true, "cuda" is used; otherwise, "cpu".
DEVICE = "cuda" if torch.cuda.is_available() else "cpu"

# Sets the computation precision for the models.
# "float16" (half-precision floating point) is faster and uses less VRAM on GPUs,
# typically with minimal impact on ASR accuracy during inference.
# "int8" (8-bit integer quantization) is highly efficient for CPU inference,
# offering speed benefits on systems without a strong GPU.
COMPUTE_TYPE = "float16" if DEVICE == "cuda" else "int8"

# The language code of the speech you expect to transcribe. This is crucial for:
# 1. Loading the correct language-specific alignment model.
# 2. Guiding the Whisper ASR model, especially multilingual ones.
LANGUAGE_CODE = "en"

# --- Audio Recording Parameters (for sounddevice and VAD) ---
# These settings define how audio is captured from the microphone and processed by VAD.

# The sample rate of the audio (samples per second). Whisper models and WebRTC VAD
# are optimized for 16kHz audio, so it's the recommended rate.
SAMPLE_RATE = 16000 # Hz

# The duration of each tiny audio frame (chunk) that WebRTC VAD will analyze.
# WebRTC VAD requires this to be strictly 10, 20, or 30 milliseconds.
# A smaller value allows for more precise silence detection but slightly more overhead.
VAD_FRAME_DURATION_MS = 30 # milliseconds

# The actual number of audio frames (samples) that `sounddevice` will pass to its
# callback function at each invocation. This is derived from the VAD frame duration.
BLOCK_SIZE_SOUNDDEVICE = int(SAMPLE_RATE * VAD_FRAME_DURATION_MS / 1000)

# --- VAD (Voice Activity Detection) Parameters ---

# Aggressiveness mode for WebRTC VAD (integer from 0 to 3).
# Higher values (e.g., 3) mean the VAD is more aggressive in filtering out
# non-speech, potentially missing some quiet speech. Lower values (e.g., 0)
# are less aggressive, allowing more background noise to be considered speech.
# A value of 1 or 2 is often a good balance for general use.
VAD_AGGRESSIVENESS = 1

# The duration of consecutive silence, in milliseconds, that will trigger
# the end of the current speech segment. Once this much silence is detected,
# the accumulated audio is sent for transcription.
SILENCE_DURATION_TO_END_BUFFER_MS = 800 # milliseconds

# --- Global State & Queues ---
# These queues facilitate communication and data transfer between the different threads
# in a thread-safe manner.

# Queue for raw, small audio frames (e.g., 30ms) coming directly from the microphone callback.
# These frames are consumed by the VAD processing thread.
raw_audio_frames_queue = queue.Queue()

# Queue for full speech segments that have been identified by the VAD. These segments
# are ready to be transcribed by the WhisperX transcription thread.
transcription_queue = queue.Queue()

# A threading.Event object used for signaling. When `stop_event.set()` is called,
# it signals all threads listening to this event (via `stop_event.is_set()`) to
# gracefully terminate their operations. Essential for clean shutdown.
stop_event = threading.Event()

# --- WhisperX Model Loading ---
# Models are loaded only once at the beginning of the script to minimize overhead
# and avoid repeated loading during continuous real-time processing.

print(f"Loading WhisperX model '{WHISPER_MODEL_NAME}' on {DEVICE} with {COMPUTE_TYPE}...")
# `whisperx.load_model()` loads the main ASR model (e.g., "base.en").
# It handles downloading the model if not cached and converting it to the
# optimized CTranslate2 format for faster inference.
asr_model = whisperx.load_model(WHISPER_MODEL_NAME, device=DEVICE, compute_type=COMPUTE_TYPE)
print("ASR model loaded.")

print(f"Loading WhisperX alignment model for '{LANGUAGE_CODE}' on {DEVICE}...")
# `whisperx.load_align_model()` loads the specialized model used for forced alignment.
# This model takes the ASR-transcribed text and the audio, and then pinpoints
# the exact start and end times for each individual word.
# It returns the alignment model itself and `metadata` which is also needed by `align()`.
align_model, metadata = whisperx.load_align_model(language_code=LANGUAGE_CODE, device=DEVICE)
print("Alignment model loaded.")

# Initialize WebRTC VAD. The `Vad` class constructor takes the aggressiveness mode.
vad = webrtcvad.Vad(VAD_AGGRESSIVENESS)
print(f"WebRTC VAD initialized with aggressiveness mode {VAD_AGGRESSIVENESS}.")

# --- Audio Callback Function ---
# This function is executed by `sounddevice` in a separate, internal audio thread.
# It's called automatically whenever a new block (chunk) of audio data is received from the microphone.
def audio_callback(indata, frames, time_info, status):
    """
    Callback function for `sounddevice.InputStream`.
    It receives raw audio data from the microphone and places it into `raw_audio_frames_queue`.

    Args:
        indata (np.ndarray): NumPy array containing the audio data for the current block.
                             Typically float32, with shape (frames, channels).
        frames (int): The number of frames (samples) in `indata`.
        time_info (dict): Dictionary containing stream timing information.
        status (sd.CallbackFlags): An object indicating any stream warnings or errors.
    """
    if status:
        # Print any warnings or errors reported by `sounddevice` (e.g., input overflow).
        print(f"Audio status: {status}", flush=True)

    # WebRTC VAD requires audio in 16-bit integer PCM format (Pulse-Code Modulation).
    # `sounddevice` typically provides `float32`. We convert it here:
    # 1. Multiply by 32767.0 (max value for int16) to scale `float32` (-1.0 to 1.0) to `int16` range.
    # 2. Cast to `np.int16`.
    # 3. `.tobytes()` converts the NumPy array of int16s into a raw byte string, which VAD expects.
    #    This also implicitly flattens the array.
    int16_data = (indata * 32767.0).astype(np.int16).tobytes()
    
    # Put the processed audio frame into the `raw_audio_frames_queue`. This transfers
    # the data from the `sounddevice` thread to the `vad_processing_thread` safely.
    raw_audio_frames_queue.put(int16_data)

# --- VAD Processing Thread Function ---
# This is a custom thread that continuously pulls small audio frames from `raw_audio_frames_queue`,
# applies WebRTC VAD, and intelligently buffers them into larger speech segments based on silence.
def vad_processing_thread():
    """
    Thread function to perform VAD on incoming audio frames and assemble complete speech segments.
    It identifies speech boundaries based on detected silence and puts full segments into `transcription_queue`.
    """
    print("\nStarting VAD processing thread...")

    # A `bytearray` is used to efficiently accumulate raw 16-bit PCM audio frames.
    current_audio_buffer = bytearray()

    # Counter for consecutive silent frames. Used to detect an end-of-speech segment.
    silent_frames_count = 0

    # Calculate the number of consecutive silent frames required to trigger a segment end.
    # This calculation MUST be done here, outside the `while` loop, so `silent_frames_threshold`
    # is defined when first accessed in the `if` condition within the loop.
    silent_frames_threshold = int(SILENCE_DURATION_TO_END_BUFFER_MS / VAD_FRAME_DURATION_MS)

    # The main loop of the VAD thread. It continues until `stop_event` is set.
    while not stop_event.is_set():
        try:
            # Attempt to get a small audio frame from the queue.
            # `timeout=0.1` makes the `get()` call non-blocking for a short period.
            # This allows the thread to periodically check `stop_event.is_set()`
            # even if no audio is coming in, enabling graceful shutdown.
            frame_bytes = raw_audio_frames_queue.get(timeout=0.1)
            
            # Use `vad.is_speech()` to classify the current frame as speech or non-speech.
            # It expects 16-bit PCM `bytes` and the sample rate.
            is_speech = vad.is_speech(frame_bytes, SAMPLE_RATE)

            if is_speech:
                # If speech is detected, reset the silence counter (as speech broke any silence)
                silent_frames_count = 0
                # Extend the current buffer with the new speech frame.
                current_audio_buffer.extend(frame_bytes)
            else:
                # If silence/non-speech is detected, increment the silence counter.
                silent_frames_count += 1
                # Still add the silent frame to the buffer. We'll trim leading/trailing silence later.
                current_audio_buffer.extend(frame_bytes)

                # Check if the accumulated consecutive silence exceeds the defined threshold.
                if silent_frames_count >= silent_frames_threshold:
                    # If silence threshold is met AND there's audio in the buffer (meaning speech occurred before silence).
                    if len(current_audio_buffer) > 0:
                        # Convert the `bytearray` buffer back to a NumPy array of `int16`.
                        temp_np_array_int16 = np.frombuffer(current_audio_buffer, dtype=np.int16)
                        
                        # --- Trim Trailing Silence ---
                        # Calculate the number of samples corresponding to the `silence_frames_threshold`.
                        num_samples_to_trim = silent_frames_threshold * BLOCK_SIZE_SOUNDDEVICE
                        
                        # Determine the effective end of the speech by removing the trailing silence.
                        # `max(0, ...)` ensures we don't end up with a negative index if the buffer is tiny.
                        effective_speech_samples = max(0, len(temp_np_array_int16) - num_samples_to_trim)
                        
                        # Convert the trimmed `int16` NumPy array to `float32` and scale it
                        # back to the -1.0 to 1.0 range, as required by WhisperX.
                        speech_segment_np_float32 = (temp_np_array_int16[:effective_speech_samples].astype(np.float32) / 32767.0)
                        
                        # Only put the segment into the `transcription_queue` if it contains actual audio
                        # after trimming (i.e., not an empty array).
                        if speech_segment_np_float32.shape[0] > 0:
                            transcription_queue.put(speech_segment_np_float32)
                        
                        # Reset the buffer and silence counter for the next speech segment.
                        current_audio_buffer = bytearray()
                        silent_frames_count = 0
            
            # Immediately check if the global stop event has been set, and exit the loop if it has.
            if stop_event.is_set():
                break

        except queue.Empty:
            # If `raw_audio_frames_queue.get(timeout=0.1)` times out, this exception is raised.
            # It simply means no new audio frame arrived within the timeout.
            # We then check the `stop_event` and `continue` the loop to try again.
            if stop_event.is_set():
                break
            continue # Loop back to try getting a frame again
        except Exception as e:
            # Catch any other unexpected errors in the VAD processing thread.
            print(f"Error in VAD processing thread: {e}", flush=True)
            break # Exit the thread on unexpected error

    print("VAD processing thread stopping.")
    # After the VAD thread has stopped (its loop has exited), put a `None` sentinel
    # into the `transcription_queue` to signal the `transcribe_audio_thread` to stop.
    transcription_queue.put(None)

# --- Transcription Thread Function ---
# This thread continuously pulls full speech segments (identified by VAD) from `transcription_queue`
# and processes them using WhisperX's ASR and alignment capabilities.
def transcribe_audio_thread():
    """
    Thread function to transcribe full speech segments received from the VAD thread.
    It uses WhisperX for ASR and word-level alignment, then prints the results.
    """
    print("Starting transcription thread...")
    while True: # Loop indefinitely to process incoming speech segments
        try:
            # Get a complete speech segment from the `transcription_queue`.
            # `get()` blocks until a segment (or the `None` sentinel) is available.
            audio_segment = transcription_queue.get()
            
            # Check for the `None` sentinel value; if received, it's time to stop the thread.
            if audio_segment is None:
                print("Transcription thread stopping.")
                break # Exit the loop, terminating the thread


            # Use the .detect_language() method of the asr_model object
            # This returns a tuple: (language_code, {language_code: probability_score})
            # where the second element is a dictionary of all language probabilities.
            # We're interested in the probability of the *detected* language.
            
            # Pad or trim the audio for language detection if it's not already 30s
            # Whisper's language detection is optimized for 30s chunks.
            # Convert to appropriate format for detect_language
            audio_for_language_detection = whisperx.audio.pad_or_trim(audio_segment.astype(np.float32))

            # It seems for whisper, the detect_language method returns the detected language and a dictionary of probabilities for all detected languages, with the first element is the most probable language code string.
            # The second element is a dictionary mapping language codes to their probabilities.
            # Unfortunately, whisperx only returns the language, not the score nor the other language probabilities - but you CAN go into the backend python method 'detect_language', remove the annoying print line, and have it expressly return language_probability 
            # (you will note it did not before, and it should have). I didnt modify it directly, but I made a new one (in case other calling things would call this and expect just the language to be returned) in <CONDA_ENV>/lib/python3.12/site-packages/whisperx/asr.py; 
            # I named it 'detect_language_with_probability', where the official one is simply 'detect_language' (so if you are copying this, you will have to either make 'detect_language_with_probability' or just use 'detect_language' with no probability returned).
            detected_language, language_probability = asr_model.detect_language_with_probability(audio_for_language_detection)

            # Print the detected language
            print(f"🌍 Detected language: {detected_language} {language_probability}")
            
            # Now perform the transcription, explicitly passing the detected language.
            #    This suppresses the "Warning: audio is shorter than 30s..." message
            #    and guides the model for better transcription of the detected language.
            result = asr_model.transcribe(audio_segment, batch_size=1, language=detected_language) 
            # The 'result' has a few pieces:  
            # * 'segments' - a list of dictionaries, with each dictionary containing 'text' (the entire spoken segment), 'start' (the start of the entire segment in the string), and 'end' (the end of the entire segment in the string)
            # * 'language' - the language code (i.e. 'en')
            # * does NOT have 'language_probability' - apparently this is in whisper, but it may NOT be in whisperx? Sad!
            # * Note this does NOT seem to have confidence scores or individual word start/end times - that seems to come from 'whisperx.align'

            # Process and print the transcription results.
            # First, check if the ASR model actually detected any speech segments.
            # `result` might be empty or not contain "segments" if no speech was confidently detected.
            if result and "segments" in result and result["segments"]:
                # If segments are found, proceed with forced alignment to get precise word timestamps.
                # All necessary parameters (`segments`, `align_model`, `metadata`, `audio_segment`, `device`)
                # are passed to `whisperx.align()`.
                word_segments_result = whisperx.align(result["segments"], align_model, metadata, audio_segment, device=DEVICE)
                # word_segments_result is built of
                # * segments  - like above, still containing 'start/end/text but now has a 'words' list of dictionaries, one for every word spoken
                #   * 'words' dictionary - for every entry:
                #     * 'word' - the word
                #     * 'start' - the start time of the word, relative to the segment beginning
                #     * 'end' - the end time of the word, relative to the segment beginning
                #     * 'score' - the confidence score behind the word
                # * 'word_segments' dictionary - seems to be the same as the words dictionary, just...outside of the 'segments' dictionary

                
                # After alignment, check if `word_segments_result` actually contains
                # precise word-level segments. It might be empty if alignment failed or
                # if the original ASR segments were too short/noisy for confident alignment.
                if word_segments_result and "word_segments" in word_segments_result and word_segments_result["word_segments"]:
                    full_text = ""
                    # Iterate directly over the word segments list.
                    # Each 'word_info' will directly be a dictionary like {'word': 'going', 'start': ..., 'end': ...}
                    for word_info in word_segments_result["word_segments"]:
                        if "word" in word_info: # Check if the 'word' key exists in the current word_info dictionary
                            full_text += f"{word_info['word']} "

                            #There are other things you can use too - start, end, (confidence) score. The start and end are relative to the segment
                            #full_text += f"{word_info['word']} ({word_info['start']}/{word_info['end']}) ({word_info['score']}) "
                        else:
                            # This warning is a safety check for a malformed word_info, less likely now.
                            print(f"Warning: 'word' key missing in word_info: {word_info}", flush=True)

                                
                    # If `full_text` is not empty after stripping whitespace, print the transcription.
                    if full_text.strip():
                        print(f"🎤 You said: {full_text.strip()}", flush=True)
                else:
                    # This path is executed if ASR found speech, but alignment couldn't produce words.
                    # Suppressed for cleaner output.
                    pass # print("... (alignment found no words in detected speech) ...", flush=True)
            else:
                # This path is executed if WhisperX's ASR model itself detected no speech
                # in the current segment passed from the VAD thread.
                # Suppressed for cleaner output.
                pass # print("... (no discernible speech in segment by ASR) ...", flush=True)

            # --- Memory Management ---
            # Explicitly delete the audio segment and trigger garbage collection
            # to free up memory after processing, especially important for GPU VRAM.
            del audio_segment
            gc.collect()
            if DEVICE == "cuda":
                # Clear PyTorch's internal GPU memory cache to make VRAM immediately available.
                torch.cuda.empty_cache()

        except Exception as e:
            # General error handling for the transcription thread.
            print(f"Error during transcription: {e}", flush=True)
            # Depending on the error, you might want to `break` here to stop the thread
            # or `continue` to try processing the next segment. For now, it continues.

# --- Main Execution Block ---
# This is the bullseye of the script when it's run, orchestrating the threads and microphone input.
if __name__ == "__main__":

    # By default, TensorFloat-32 (TF32) has been disabled as it might lead to reproducibility issues and lower accuracy.
    # However, for more modern Nvidia GPUs, especially for the Ampere, Ada Lovelace, and Blackwell architectures - you will probably see a performance boost
    # If, however, there are accuracy issues, do not enable these
    torch.backends.cuda.matmul.allow_tf32 = True
    torch.backends.cudnn.allow_tf32 = True

    # Create and start the VAD processing thread.
    # It will begin pulling raw audio frames from `raw_audio_frames_queue`.
    vad_thread = threading.Thread(target=vad_processing_thread)
    vad_thread.start()

    # Create and start the transcription thread.
    # It will begin pulling processed speech segments from `transcription_queue`.
    transcription_thread = threading.Thread(target=transcribe_audio_thread)
    transcription_thread.start()
    
    try:
        # Initialize and start the microphone input stream using `sounddevice`.
        # `samplerate`: The audio sample rate.
        # `channels`: 1 for mono audio, suitable for ASR.
        # `callback`: Specifies `audio_callback` to be called with incoming audio data.
        # `blocksize`: The size of audio chunks passed to `audio_callback`.
        #              This is now set to a small value (e.g., 30ms frames) for VAD.
        stream = sd.InputStream(
            samplerate=SAMPLE_RATE,
            channels=1,
            callback=audio_callback,
            blocksize=BLOCK_SIZE_SOUNDDEVICE
        )
        
        print(f"Recording audio from microphone at {SAMPLE_RATE} Hz in {VAD_FRAME_DURATION_MS} ms frames...")
        
        # The `with stream:` statement ensures the audio stream is properly
        # opened and automatically closed when the `with` block is exited.
        with stream:
            # The main thread simply stays alive here, allowing the `sounddevice`
            # audio callback, VAD thread, and transcription thread to run in the background.
            # It waits for a `KeyboardInterrupt` (Ctrl+C) to initiate shutdown.
            while True:
                time.sleep(0.1) # Small delay to prevent the main thread from busy-waiting and consuming excessive CPU.
                
    except KeyboardInterrupt:
        # This block is executed when the user presses Ctrl+C.
        print("\nStopping application via Ctrl+C...")
    except Exception as e:
        # Catch any other unexpected exceptions that might occur in the main thread.
        print(f"An error occurred in the main process: {e}")
    finally:
        # --- Graceful Shutdown Sequence ---
        # This `finally` block ensures that cleanup operations are performed
        # whether an exception occurred or the script was stopped gracefully.

        # 1. Signal all running threads to stop.
        # `stop_event.set()` changes the event's internal flag, which threads check.
        stop_event.set() 
        
        # 2. Wait for the VAD processing thread to finish its current work and exit its loop.
        # `join()` blocks the main thread until the target thread terminates.
        vad_thread.join()
        
        # 3. Wait for the transcription thread to finish processing any remaining items
        # in its queue and then exit its loop (triggered by the `None` sentinel from VAD thread).
        transcription_thread.join()
        
        # 4. Explicitly delete the loaded WhisperX models. This is important for
        # releasing GPU VRAM and system memory occupied by the models.
        del asr_model
        del align_model
        
        # 5. Trigger Python's garbage collector one last time to reclaim any remaining
        # unused memory from Python objects.
        gc.collect()
        
        # 6. If running on CUDA, clear PyTorch's internal GPU memory cache. This ensures
        # that all GPU memory is released back to the system.
        if DEVICE == "cuda":
            torch.cuda.empty_cache()
            
        print("Application stopped.")
```  
