# WasmEdge  

# What is WasmEdge  

[WasmEdge](https://wasmedge.org/) is a lightweight, high-performance, and extensible WebAssembly runtime for cloud native, edge, and decentralized applications.  

In our context, though, we will use it to launch llama.cpp (although it can be used for many other things).  WasmEdge is a lightweight Rust source code file which acts as a wrapper around the C++ llama.cpp libraries; it allows you to remotely call llama.cpp binaries.  

!> WasmEdge is not necessary - matter of fact, I prefer to simply [install LLaMa.cpp directly](ai_media/llama.cpp/llama_basics?id=install) - however, if, for whatever reason, you cannot do that, or if this makes more sense...then by all means, use WasmEdge.  

# Installing WasmEdge  

!> WasmEdge is not necessary - matter of fact, I prefer to simply [install LLaMa.cpp directly](ai_media/llama.cpp/llama_basics?id=install) - however, if, for whatever reason, you cannot do that, or if this makes more sense...then by all means, use WasmEdge.  

To install WasmEdge:  

1\. [become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)  

2\. Update packages: `apt-get update`  

3\. Install packages: `apt-get install software-properties-common build-essential libopenblas-dev ninja-build pkg-config cmake-data clang git git-lfs curl wget zip unzip`  

4\. Install optional packages, _if_ you have an Nvidia GPU _and_ you wish to install them: `apt install nvidia-cuda-toolkit`  

5\. Exit root - you dont want to be root for the actual install.  

6\. Install WasmEdge: `curl -sSf https://raw.githubusercontent.com/WasmEdge/WasmEdge/master/utils/install_v2.sh | bash`  
  * Source the directory: `source $HOME/.wasmedge/env`   
  * Note that this will source in `~/.wasmedge/env` in `~/.bashrc` _and_ `~/.profile` _as well as_ create a `~/.bash_profile` if `~/.bash_profile` does not exist  
    * Be warned that if `~/.bash_profile` exists, your `~/.profile` will be ignored by bash  

# Using llama-simple  

`llama-simple` is a WasmEdge app that allows for a simple interaction with llama.cpp. Its not interactive - so everything is done in 'one take' - but its good for a single request-response. 

First, you will need to download the `llama-simple` wasm file:  
```
curl -LO https://github.com/LlamaEdge/LlamaEdge/releases/latest/download/llama-simple.wasm  
```  

A typical `llama-simple` command:  
```
wasmedge --dir .:. --nn-preload default:GGML:AUTO:/path/to/GGUF/file/Llama-3.1-8B-Instruct-Q5_K_S.gguf /path/to/wasm/file/you.just/downloaded/llama-simple.wasm --prompt "The wacky adventures of Al Bundy includes a time when "
```  
* You will need to [download a llama.cpp model](ai_media/llama.cpp/llama_basics?id=obtaining-models) for this  
  * The above assumes you downloaded `Llama-3.1-8B-Instruct-Q5_K_S`, but its your choice  
  * Not all models will work with this - I tried the model `Midnight-Miqu-70B-v1.5.i1-Q5_K_S.gguf`, and I got an error: `When executing function name: "_start"`  
* The --dir argument allows WasmEdge to read the GGUF model file from the local filesystem.  
* The `--nn-preload` points to the GGUF model you are using.  
* The `--prompt` at the end is for asking your question of the model.  

Here are other options for `llama-simple.wasm`:  

| Option | Description | Default |   
| --- | --- |  --- |  
| -p, --prompt "YOUR_PROMPT_HERE" | Sets the prompt string, including system message if required. |  |  
| -m, --model-alias ALIAS_HERE | Sets the model alias | default |   
| -c, --ctx-size CTX_SIZE_HERE | Sets the prompt context size | 4096 |   
| -n, --n-predict N_PRDICT_HERE | Number of [tokens](ai_media/llama.cpp/terms?id=token) to predict (remember, tokens may be whole words, part of words, or punctuation - these are not always full words) | 1024 |  
| -g, --n-gpu-layers N_GPU_LAYERS_HERE | Number of layers to run on the GPU | 100 |  
| -b, --batch-size BATCH_SIZE_HERE | Batch size for prompt processing | 4096 |  
| -r, --reverse-prompt REVERSE_PROMPT_HERE | Halt generation at PROMPT, return control. |  |  
| --log-enable | Enable trace logs |  |  
| -h, --help | Print help |  |  

> You can build a conversation [using a template](ai_media/llama.cpp/llama_basics?id=chat-prompt-template), but you may be better off entering interactive mode with [llama-chat]

# Using llama-chat  

`llama-chat` is an interactive mode that allows you to 'chat' with the model.  

First, you will need to download the `llama-chat` wasm file:  
```
curl -LO https://github.com/LlamaEdge/LlamaEdge/releases/latest/download/llama-chat.wasm  
```  

A typical `llama-chat` command:  
```
wasmedge --dir .:. --nn-preload default:GGML:AUTO:/path/to/GGUF/file/Llama-3.1-8B-Instruct-Q5_K_S.gguf /path/to/wasm/file/you.just/downloaded/llama-chat.wasm --prompt-template llama-3-chat --system-prompt "You are Al Bundy from the hit TV show 'Married With Children'"  
```  
* You will notice that this is interactive - you can have a real chat with the AI!  
* You will need to [download a llama.cpp model](ai_media/llama.cpp/llama_basics?id=obtaining-models) for this  
  * The above assumes you downloaded `Llama-3.1-8B-Instruct-Q5_K_S`, but its your choice  
  * Not all models will work with this - I tried the model `Midnight-Miqu-70B-v1.5.i1-Q5_K_S.gguf`, and I got an error: `When executing function name: "_start"`  
* The --dir argument allows WasmEdge to read the GGUF model file from the local filesystem.  
* The `--nn-preload` points to the GGUF model you are using.  
* The `--system-prompt` sets the atmosphere for the _entire_ life of the conversation  
  * Well, this is a lie, but...at least for an hour of it.  

# Using llama-api-server  

`llama-api-server` is a 'quick and dirty' way to get a chatbot up and running on your local network.  

> You can only interact with this via something akin to the  `curl` command.  

First, you will need to download the `llama-api-server` wasm file:  
```
curl -LO https://github.com/LlamaEdge/LlamaEdge/releases/latest/download/llama-api-server.wasm  
```  

A typical way to start the `llama-api-server` server:  
```
nohup wasmedge --dir .:. --nn-preload default:GGML:AUTO:/path/to/GGUF/file/Llama-3.1-8B-Instruct-Q5_K_S.gguf /path/to/wasm/file/you.just/downloaded/llama-api-server.wasm --prompt-template llama-3-chat --socket-addr 0.0.0.0:8080 &disown
```  
* You will need to [download a llama.cpp model](ai_media/llama.cpp/llama_basics?id=obtaining-models) for this  
  * The above assumes you downloaded `Llama-3.1-8B-Instruct-Q5_K_S`, but its your choice  
  * Not all models will work with this - I tried the model `Midnight-Miqu-70B-v1.5.i1-Q5_K_S.gguf`, and I got an error: `When executing function name: "_start"`  
* The --dir argument allows WasmEdge to read the GGUF model file from the local filesystem.  
* The `--nn-preload` points to the GGUF model you are using.  
* `nohup` allows the program to run, even after you log out  
  * It may not be entirely necessary here, but - use it as you see fit.  
* The `0.0.0.0` is the IP  
  * `0.0.0.0` is if you want to restrict it to your local laptop / desktop  
  * You are free to use your desktop's local IP here, and it would be available on your local network.  
* the `8080` is the port - you can change this if you wish  
* The `&disown` sends the process to the background  


Then, in another terminal, you can send `curl` commands to send a `POST` and get a response:  
```
curl -X POST http://localhost:8080/v1/chat/completions -H 'accept: application/json' -H 'Content-Type: application/json' -d '{"messages":[{"role":"system", "content": "You are Al Bundy from the hit TV show ''Married With Children''"}, {"role":"user", "content": "What do you think of Marcy Darcy?"}]}'
```  
* Note the `''` to print a singe quote - this is escaping the single `'` character.  
  * Sometimes, you gotta escape characters - in this case, its achieved by typing it twice.  

## A Quick Webpage  

Its possible to make a very quick webpage interface for `llama-api-server`; it requires a directory `chatbot-ui` next to the `llama-api-server` file. Whats expected in this directory is the [code from the chatbot-ui github page](https://github.com/LlamaEdge/chatbot-ui/), but you can download a tar file of the various releases [here](https://github.com/LlamaEdge/chatbot-ui/releases). To quickly download this and unzip the directory, perform these same actions in the directory that houses the [llama-api-server file](ai_media/llama.cpp/wasmedge?id=using-llama-api-server):  

```
curl -LO https://github.com/LlamaEdge/chatbot-ui/releases/latest/download/chatbot-ui.tar.gz
tar xzf chatbot-ui.tar.gz
rm chatbot-ui.tar.gz
```
* Do this in the same directory that houses your `llama-api-server.wasm` file  


> You can change around the webpage by [forking this repo](https://github.com/LlamaEdge/chatbot-ui/), making changes, building the project, and then putting the built files in the discussed `chatbot-ui` directory.  

Now that `chatbot-ui` exists and is populated, simply start the `llama-api-server` [as we did before](ai_media/llama.cpp/wasmedge?id=using-llama-api-server). To go to the page, enter [http://localhost:8080/](http://localhost:8080/) in a web browser on your local machine (or, if you used the local IP of your desktop instead of `0.0.0.0`, use that in place of `localhost`, and you can access it from any phone, desktop, or laptop on your network).  

# Using paragraph_embed  

`paragraph_embed` allows you to enact the concept of [retrieval augmented generation](ai_media/llama.cpp/terms?id=retrieval-augmented-generation) - specifically, building an 'add on' to an existing LLM model, i.e. a [vector database](ai_media/llama.cpp/vector_databases?id=vector-databases).  

First, you will need to download the `llama-chat` wasm file:  
```
curl -LO https://github.com/GaiaNet-AI/embedding-tools/raw/main/paragraph_embed/paragraph_embed.wasm
```  

It can get a bit complicated from here; firstly, you will need to identify _what_ specific info you want to embed in the add-on vector database; then, you will need to gather data in a text file in a [certain way](ai_media/llama.cpp/vector_databases?id=gathering-data).  

> Something else that needs to be mentioned - you will need to use [Qdrant](ai_media/llama.cpp/qdrant), a Docker-based tool that assists in making the vector database - which, also unfortunately, needs Docker installed. Neither are terribly hard to install, and you can follow links on how to install both [here](ai_media/llama.cpp/qdrant?id=install).  

Next, you will have to [download an Embedding Model](ai_media/llama.cpp/llama_basics?id=embedding-models) (the model `nomic-embed-text-v1.5.f16` is a good starting point for this, but there are others, too).  

Once you have gathered the data and downloaded the embedding model, you will need to [start the Qdrant server](ai_media/llama.cpp/qdrant?id=starting-qdrant-server) and then [create a collection](ai_media/llama.cpp/qdrant?id=creating-collections).  

Now, run the `paragraph_embed` WasmEdge script:  
```
wasmedge --dir .:. --nn-preload embedding:GGML:AUTO:/path/to/GGUF/file/nomic-embed-text-v1.5.f16.gguf /path/to/wasm/file/you.just/downloaded/paragraph_embed.wasm embedding marriedwithchildren 768 /path/to/qdrant/storage/marriedwithchildren.txt --ctx_size 8192  
```  
* This assumes you were using `nomic-embed-text-v1.5.f16` as the [Embedding Model you downloaded](ai_media/llama.cpp/llama_basics?id=embedding-models), but you can use a different one.  
* This assumes the [Qdrant collection you made](ai_media/llama.cpp/qdrant?id=creating-collections) was named 'marriedwithchildren', and the text file you curated is named 'marriedwithchildren.txt'  
  * You can and probably should change this, but this is the example.  
* 768 is the dimension of the vector collection / vector size.  
* `--ctx_size 8192` indicates that each text chunk has a maximum length of 8192 tokens.  
* The file 'marriedwithchildren.txt' _must_ be in the `storage` directory you created [when installing Qdrant](ai_media/llama.cpp/qdrant?id=install), and you _must_ launch the process from the directory containing 'marriedwithchildren.txt'.  
  * I tried specifying a path, i.e. `/path/to/qdrant/storage/marriedwithchildren.txt`, but...it didnt work for some reason.  
* Actually...the code references `glibc` files but its _relative to where you launched the above `wasmedge` command, which....is really poor programming.  
  * I had to, as root, install `apt install glibc-source` and then go to `/usr/src/glibc` and `tar -xvf /usr/src/glibc/glibc-2.39.tar.xz -C /usr/src/glibc` (you may have to change the glibc version), and _then_ copy the `/usr/src/glibc` directory to  `~/.wasm/edge/lib` and _then_ make a `~/.wasm/edge/lib/glibc/text` directory, put my 'marriedwithchildren.txt' in there as well, and then launch the above `wasmedge` command from there, and...still errors.  
  *This wasm library seems unusable.  

!> If you get the error `Could not attach to process. If your uid matches the uid of the target...`, I found help from [askubuntu](https://askubuntu.com/questions/41629/after-upgrade-gdb-wont-attach-to-process) - you have to run `echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope` from an account that has root access. To permanently allow it, edit `/etc/sysctl.d/10-ptrace.conf` and change the line `kernel.yama.ptrace_scope = 1` to read `kernel.yama.ptrace_scope = 0`.  


