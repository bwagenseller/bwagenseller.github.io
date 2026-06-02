# Qdrant  

# What is Qdrant?  

<font color="green">Qdrant</font> is an 'app' (well, a [Docker](/operating_systems/docker/) image, but it acts as an app) that creates 'add-ons' for [LLM models](ai_media/llama.cpp/llama_basics?id=models). <font color="green">Qdrant</font> creates something akin to an 'expansion pack' for an existing model - the model acts as a base model, but additional information - information that does not exist in the base model - is in this 'expansion pack', and if questioned about it, the model will respond with the additional information. <font color="green">Qdrant</font> utilizes a process called [vector embeddings](ai_media/llama.cpp/terms?id=vector-embeddings) to achieve this goal. Officially, this is not called 'add-on data' or an 'expansion pack', its called the [retrieval augmented generation](ai_media/llama.cpp/terms?id=retrieval-augmented-generation) (RAG) approach.  

> While in this case, <font color="green">RAG</font> is akin to an 'add on' or an 'expansion pack' for the LLM model, other <font color="green">RAG</font> approaches utilize querying the internet or other data sources.  

!> <font color="green">Qdrant</font> requires [docker](operating_systems/docker/docker_basics?id=installation). If you dont have it, you will need to install it. If you have no idea what you are doing and do not care to really use Docker, I would suggest just using [Docker Engine](operating_systems/docker/docker_basics?id=docker-engine-installation-ubuntu), as the GUI can be a bit confusing.  

# Install  

1\. Install [docker](operating_systems/docker/docker_basics?id=installation)  
  * You can either pick [Docker Desktop](operating_systems/docker/docker_basics?id=docker-desktop-installation-ubuntu) or [Docker Engine](operating_systems/docker/docker_basics?id=docker-engine-installation-ubuntu) - its up to you.  
  * If you have no idea what you are doing and do not care to really use Docker, I would suggest just using [Docker Engine](operating_systems/docker/docker_basics?id=docker-engine-installation-ubuntu), as the GUI can be a bit confusing.  
    * I prefer [Docker Desktop](operating_systems/docker/docker_basics?id=docker-desktop-installation-ubuntu), but only if you know what you are doing.  

2\. Pull the 'qdrant' Docker image: `docker pull qdrant/qdrant`  
  * Click [here](operating_systems/docker/image_commands?id=pulling-an-image) to read more on what 'pulling' means.  

3\. You will need a storage directory and a snapshot directory - I named mine `qdrant/storage` and `qdrant/snapshots`, but you can name them whatever you wish.  

## Additional Installs  

These are not technically Qdrant, but you will need these tools to interact with it.  

1\. [become root](/operating_systems/ubuntu/linux_notes?id=becoming-root)  

2\. Update packages: `apt-get update`  

3\. Install packages: `apt-get install curl`  



# Starting Qdrant Server  

!> Before you do this, you may have to give Docker permissions to use the directories in file sharing; I am using [Docker Desktop](operating_systems/docker/docker_basics?id=docker-desktop-installation-ubuntu), so I did this via `Settings` -> `Resources` -> `File sharing` and had to add `/path/to/qdrant` under `Virtual file shares`.  

To start the Qdrant server:  
```
docker run -p 6333:6333 -p 6334:6334 -v /path/to/qdrant/storage:/qdrant/storage:z -v /path/to/qdrant/snapshots:/qdrant/snapshots:z qdrant/qdrant  
```  
* The paths you made during [the install](ai_media/llama.cpp/qdrant?id=install) are relevant in the above line.  
* The ports `6333` and `6334` are used for various things  
  * `6333` is used for collections (not an exhaustive list of what `6333` is used for)  


# Creating Collections  

To create a collection, you must [start the server](ai_media/llama.cpp/qdrant?id=starting-qdrant-server) and then run:  
```
curl -X PUT 'http://localhost:6333/collections/marriedwithchildren' -H 'Content-Type: application/json' --data-raw '{
    "vectors": {
      "size": 768,
      "distance": "Cosine",
      "on_disk": true
    }
  }'
```  
* This makes a new collection called 'marriedwithchildren'
* The port you used when [starting the server](ai_media/llama.cpp/qdrant?id=starting-qdrant-server) is important here.  

The check and see if the collection was made, run:  
```
curl 'http://localhost:6333/collections/marriedwithchildren'
```  

You should get back a result that indicates it exists.  


