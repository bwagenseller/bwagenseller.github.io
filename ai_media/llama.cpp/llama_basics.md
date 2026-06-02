# llama.cpp Basics  
<!-- 
<font color="purple">Optional</font>
![tilix_login_shell.png](images/tilix_login_shell.png)
-->

# Links  

* [Building Llama Locally](https://github.com/ggml-org/llama.cpp/blob/master/docs/build.md#cpu-build)  
* [API Reference](https://llama-cpp-python.readthedocs.io/en/latest/api-reference/)  
* Guides  
  * [datacamp LLaMa-cpp Tutorial](https://www.datacamp.com/tutorial/llama-cpp-tutorial)  
    * Teaches the basics of using Python and LLaMa-CPP  
  * [Running LLaMA Locally with Llama.cpp: A Complete Guide](https://medium.com/hydroinformatics/running-llama-locally-with-llama-cpp-a-complete-guide-adb5f7a2e2ec)  
  * [steelph0enix.dev](https://blog.steelph0enix.dev/posts/llama-cpp-guide/)  
  * [Everything I've learned so far about running local LLMs](https://nullprogram.com/blog/2024/11/10/)  
* Stable Diffusion Prompts  
  * [New LLaMa3 Stable-diffusion prompt maker](https://www.reddit.com/r/comfyui/comments/1caeha5/new_llama3_stablediffusion_prompt_maker/)  
  * [Stable Diffusion Prompts](https://ollama.com/impactframes/llama3_ifai_sd_prompt_mkr_q4km)  
* [Run any LLM on Distributed Multiple GPUs Locally](https://medium.com/@yash9439/run-any-llm-on-distributed-multiple-gpus-locally-using-llama-cpp-2ff478a0dc3c)  
* [abliteration](https://huggingface.co/blog/mlabonne/abliteration), i.e. the process of uncensoring any LLM  

# Install  

> You also have the option of installing [LLaMa.cpp via WasmEdge](ai_media/llama.cpp/wasmedge?id=installing-wasmedge) - but I prefer installing LLaMa.cpp directly.  

## Optional Install Packages  

Install some optional packages first. You do not need to do these steps, but this can be helpful later:  

1\. [become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)  

2\. Update packages: `apt-get update`  

3\. Install packages: `apt-get install software-properties-common build-essential libopenblas-dev ninja-build pkg-config cmake-data clang git git-lfs curl wget zip unzip`  

Also, you can use `apt install` to install `nvidia-cuda-toolkit`, but <font color="red">be thoroughly warned</font> that, especially if you have a newer GPU, you are _usually much better off_ [installing the CUDA Toolkit from Nvidia directly](https://developer.nvidia.com/cuda-toolkit), and _dont_ forget to add this to `.bashrc`:  
```
# Add CUDA Path - can check with 'nvcc --version'
export PATH=/usr/local/cuda/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```


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

## LLaMa-cpp Install  

Install LLaMa-cpp locally (**no root needed**):  

1\. Pick a good directory to house the project  
* <font color="red">Please note</font> that the directory you pick is the permanent directory for llama.cpp - this isnt an install per se!  

2\. Clone the project: `git clone https://github.com/ggml-org/llama.cpp.git`  

3\. We will install the [CUDA version](https://github.com/ggml-org/llama.cpp/blob/master/docs/build.md#cuda), which will utilize the GPU  
  * Its possible to install the [CPU build instead](https://github.com/ggml-org/llama.cpp/blob/master/docs/build.md#cpu-build), but its very slow.  

4\. _If_ you have a Nvidia GPU _and_ you have not recently [put the relevant variables in memory](ai_media/llama.cpp/llama_basics?id=nvidia-cuda-library), do so now.  

5\. Actually build and install llama.cpp:  
```
cmake -B build -DGGML_CUDA=ON
cmake --build build --config Release
```  
* This - particuarly the `-DGGML_CUDA=ON` part - assumes you have a Nvidia GPU.  
  * If you do not, omit that flag  
  * The only real difference between this install and the CPU install is the 'CUDA Toolkit' install and the `-DGGML_CUDA=ON`  
* If you see a `Could not find CURL` - even if you have it installed - it probably means your current install is pointing to a local Pandas/NumPy build  
  * If this is true, run `sudo apt install curl libcurl4-openssl-dev` as per [this post](https://github.com/ggml-org/llama.cpp/issues/12933)  


# Theory Behind llama.cpp  

> Note this has nothing to do with the theory of LLMs in general - just llama.cpp specifically.  

llama.cpp relies on C++ - not python. You can use Python with llama.cpp, but....C++ is the driver.  

# Models

## Model Types

* .GGUF - this model is a python-specific model. Python, apparently, is not particularly efficient for LLMs, but - it works.  
  * Apparently, C and C++ are better - and llama.cpp is written in C++.  

## Obtaining Models  

Models are located on [huggingface](https://huggingface.co/). For the LLMs, you will see many values with a `B1 at the end - this means billions of parameters, for example:  

| Size | Description |  
| --- | --- |  
| 8B | 8 billion parameters. This is the smallest of models. It can run on CPUs. Its not terribly accurate or overwhelmingly interesting, but, it works to start. You can probably run this on smaller machines with just a bit of chugging; having a GPU will easily handle these. Their sizes range from 5-15GB typically. |  
| 30B | 30 billion parameters. These run well on a GPU with 16GB of VRAM; they can probably run on a CPU, although you may see a word every 1-2 seconds. The accuracy of these are 'OK' and are probably the minimum you would want for anything semi-serious. |  
| 70B | 70 billion parameters. These need high-end GPUs to run well, with ~75GB of VRAM; These will run on a GPU with ~16GB of VRAM, but you will see a word every 1-2 seconds. The accuracy of these are acceptable, for sure. |  
| 405B | 405 billion parameters. These need top of the line server hardware that has access to multiple high-end GPUs. These can compete with the likes of GPT4 and Claude 3.5 |  

Currently, the models will end in `.gguf` - this is the basic file type of these models. There _are_ other types (i.e. `.safetensor`), but `.gguf` is currently the most widely used.  

In addition, you may also see the word <font color="purple">-Instruct</font> and the end of some model names. this is because models without the <font color="purple">-Instruct</font> may be pre-trained base models that can work, but they have a great deal of trouble remembering things about the conversation; for example, if you want to role play as your favorite Caribbean pirate, well, it may forget its swashbuckling ways after 1 or 2 interactions. <font color="purple">-Instruct</font> also seems to have better 'memory' about the conversation in general, so, I prefer the <font color="purple">-Instruct</font> models, personally.  

Finally, you may want to take advantage of <font color="purple">quantized models</font>. <font color="purple">Quantized models</font> use a lower precision integer number to replace the high precision floating point number in model weights. <font color="purple">Quantized models</font> can _significantly_ reduce the amount of VRAM necessary to run the model, although quality does suffer - but only slightly.  For example, if you see `Q4_K_M` in the file name, this means its 4-bit k-quantization of medium size.  

> You can read more on quantizing options [here](https://github.com/ggerganov/llama.cpp/discussions/2094).  

Some basic models for you (there are thousands, if not hundreds of thousands, on [huggingface](https://huggingface.co/), but this is a good variety of starter models):  

| Model | Notes |  
| --- | --- |  
| [Meta-Llama-3.1-8B](https://huggingface.co/meta-llama/Meta-Llama-3.1-8B-Instruct) | * This is llama's 'official' model for 8B.  <br> * Not particularly powerful, but can probably run on a CPU.  <br> * Meta forces you to share some info with them - instead, you can download it from [second-state](https://huggingface.co/second-state/Meta-Llama-3.1-8B-Instruct-GGUF) |  
| [Meta-Llama-3.1-70B-Instruct](https://huggingface.co/meta-llama/Llama-3.1-70B-Instruct) | * This is llama's 'official' model for 70B.  <br> * Meta forces you to share some info with them - instead, you can download it from [second-state](https://huggingface.co/second-state/Meta-Llama-3.1-70B-Instruct-GGUF) |  
| [Midnight-Miqu](https://huggingface.co/mradermacher/Midnight-Miqu-70B-v1.5-i1-GGUF) | * A good Role-Playing model (i.e. go ahead and talk to JFK, if you wish - or George Washington)  <br> * Careful, some guardrails were removed from this model and can, potentially, lead to 'unsafe' answers. |  
| [Phi-3.5-mini-instruct](https://huggingface.co/second-state/Phi-3.5-mini-instruct-GGUF) | * This is a mini-model that can be run on CPUs  <br> * Dont expect great quality, but...expect it to run. |  
| [Luna-AI-Llama2-Uncensored](https://huggingface.co/TheBloke/Luna-AI-Llama2-Uncensored-GGUF/blob/main/README.md) | * This model is small, but its fast to respond  <br> * This model is 'OK' but limited  <br> * Careful, some guardrails were removed from this model and can, potentially, lead to 'unsafe' answers. |  
| [Llama-3.1-405B-Instruct](https://huggingface.co/meta-llama/Llama-3.1-405B-Instruct-FP8) | * This is Meta's _gigantic_ model that is akin to GPT4. * Pretty much no personal system can run this, unless you have multiple top of the line GPUs |  
* Some of the models are 'uncensored' - the process of doing this is called [abliteration](https://huggingface.co/blog/mlabonne/abliteration)  

## Embedding Models  

The models mentioned [previously](ai_media/llama.cpp/llama_basics?id=obtaining-models) were base models - they can be used as stand-alone models. If you would like to make a 'expansion' / 'add-on' to that base model, which contains additional information, you will need some form of [retrieval augmented generation](ai_media/llama.cpp/terms?id=retrieval-augmented-generation), which encompasses several different ways to do that. One way is through [vector databases](ai_media/llama.cpp/vector_databases), and <font color="purple">-Embedding Models</font> do just that.  

In order to make a vectorized database, you will need a special type of model that is not built for acting as an LLM, but built for making said vectorized databases that can augment them / act as an 'add-on'. One such model is [Nomic-embed-text-v1.5-Embedding](https://huggingface.co/gaianet/Nomic-embed-text-v1.5-Embedding-GGUF), but you can find others [on the leaderboard at huggingface](https://huggingface.co/spaces/mteb/leaderboard).  

## Compiling from .safetensors  

> I learned this [in this post](https://github.com/ggml-org/llama.cpp/discussions/12513)  

Sometimes, a `.gguf` file will not be available on huggingface - but a bunch of `.safetensors` files will be. For example, the _official_ Llama3.1-70B model is not compiled into a `.gguf` file, its [a bunch of safetensors files](https://huggingface.co/meta-llama/Llama-3.1-70B-Instruct/tree/main).  The good news is you can compile these `.safetensors` files into a single `.gguf` file pretty easily. Be warned, however, the initial collection of `.safetensors` will be 120-150 GB in total, _then_ the non-quantized file will be about that same size, and _then_ the quantized file you make will be ~50 GB; so, plan accordingly for the large files.  

Steps:  

1\. [Install LLaMa-cpp](ai_media/llama.cpp/llama_basics?id=install)  
  * Take note of where you made the install directory [during this step](ai_media/llama.cpp/llama_basics?id=llama-cpp-install).  

2\. Download the `.safetensors` files to a new directory (note the directory).  
  * An example of these files are [here](https://huggingface.co/meta-llama/Llama-3.1-70B-Instruct/tree/main)  
  * Download _all_ files, _not_ just the `.safetensors` files! They will be needed  

3\. Navigate to the [install directory](ai_media/llama.cpp/llama_basics?id=llama-cpp-install) - the file `requirements.txt` should be in this base directory.  

4\. Install additional python libraries: `pip install -r requirements.txt`  

5\. Convert all `.safetensors` files to a `.gguf` file: `python convert_hf_to_gguf.py DIR_CONTAINING_SAFETENSORS_FILES_HERE`  
  * `DIR_CONTAINING_SAFETENSORS_FILES_HERE` is the directory where you downloaded all of the `.safetensors` files  
  * When this is finished, it will make a file named something like `xxx-F16.gguf` in the `DIR_CONTAINING_SAFETENSORS_FILES_HERE` directory.  
    * the `xxx` will be different, but the point is, it will be a `gguf` file - albeit a _large_ file.  

6\. Now, [quantize](ai_media/llama.cpp/terms?id=quantization) the `.gguf` file (my favorite is `Q5_K_M` but you are free to [choose](ai_media/llama.cpp/terms?id=quantization)): `llama-quantize xxx-F16.gguf  xxx-Q5_K_M.gguf Q5_K_M`  
  * `llama-quantize` may be located in `build/bin`, so you may have to add that to the path.  
  * `xxx-F16.gguf` is the `.gguf` file created in the last step  
  * `xxx-Q5_K_M.gguf` is the name of the resultant file  
  * `Q5_K_M` is the quantization strategy - I like `Q5_K_M` but you are free to pick this.  

---  

# Chat Prompt Template  

## LLama 3.1 Instruct Template  

> This template is specific to LLama 3.1 Instruct models such as the [Meta-Llama-3.1-70B-Instruct](https://huggingface.co/meta-llama/Llama-3.1-70B-Instruct) model.  

Example template:  
```
<|begin_of_text|><|start_header_id|>system<|end_header_id|>\n\n You are a helpful assistant. Be polite!<|eot_id|>

<|start_header_id|>user<|end_header_id|>\n\n My first question?<|eot_id|>

<|start_header_id|>assistant<|end_header_id|>\n\n The first answer.<|eot_id|>

<|start_header_id|>user<|end_header_id|>\n\n My second question?<|eot_id|>

<|start_header_id|>assistant<|end_header_id|>\n\n The second answer.<|eot_id|>

<|start_header_id|>user<|end_header_id|>\n\n My third question?<|eot_id|>

<|start_header_id|>assistant<|end_header_id|>\n\n 
```  
* We always start with `<|begin_of_text|>`  
* All messages start with a role in between the `<|start_header_id|>` and `<|end_header_id|>` tags  
  * These roles are:  
    * system  
      * there seems to only be one of these, at the beginning  
      * this should include the over-arching instructions that are meant to last for the entirety of the session  
    * user  
      * i.e. you  
    * assistant  
      * i.e. the AI chatbot  
  * Note the `\n\n ` after every `<|end_header_id|>`; this is important.  
    * The above is broken into human-readable form, but...this will all be one line. The `\n\n ` is necessary at the given spots.  
* The tag `<|eot_id|>` seems to denote the end of a thought  
  * In this case, a message  
* We put the `<|start_header_id|>assistant<|end_header_id|>` at the end to indicate that we expect the model to continue after that and fill in the words the “assistant” (i…e, the LLM itself) would say.  
* `[INST]` tags are used in llama.cpp to wrap user and assistant content in chat completions.  
  * This tagging format helps the model distinguish between different parts of the conversation and generate appropriate responses.  
* <font color="red">-Why</font> does the exmple above give a back-and-forth response, like you are asking a question, the assistant answers, then you ask a question, and the assistant answers again, etc?  
  * This is because some (not all) models hold no state - so the way to have a conversation with them is by, quite literally, running it once, copying the response, making a new line in your script for your additional question, pasting the AI's provided answer in a `<|start_header_id|>`, doing this recursively for all questions you had thus far, and then ending with `<|start_header_id|>assistant<|end_header_id|>` so the AI can answer your last question.  
    * Without this, since some models do not have saved state...some models can _completely_ forget about the line you talked about right above it  
    * Note that this is not necessary for some models, but some absolutely need this.  
      * And yes, its annoying that you have to keep re-building this every time  
    * For the models that need this: eventually, the compiler will complain that the prompt is too big.  