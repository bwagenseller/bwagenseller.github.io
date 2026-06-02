# Vector Databases   

# Explaining Vector Databases 

A <font color="green">Vector Database</font> stores and manages [vector embeddings](ai_media/llama.cpp/terms?id=vector-embeddings).  Its one of the methods of [retrieval augmented generation](ai_media/llama.cpp/terms?id=retrieval-augmented-generation) - in other words, <font color="green">vector databases</font> is one way we can add additional data to an existing [large language model](/ai_media/llama.cpp/terms?id=large-language-model). In effect, a <font color="green">vector databases</font> acts as an 'expansion pack' to an LLM.  

# Gathering Data  

In order to eventually populate a [vector database](ai_media/llama.cpp/terms?id=vector-database) for [retrieval augmented generation](ai_media/llama.cpp/terms?id=retrieval-augmented-generation), you need to get some text files available for the process. Some general notes on the gathering of this data:  
* The text will need to be placed into chunks (like paragraphs).  
  * Each chunk will be turned into an [embedding](ai_media/llama.cpp/terms?id=vector-embeddings) and stored in a [vector database](ai_media/llama.cpp/terms?id=vector-database).  
  * Each chunk should be less than 300 words.  
  * Each chunk should contain a reasonably complete, cohesive theme.  
    * Basically follow the rules for writing paragraphs, but try to contain an entire 'thought' in each paragraph.  
      * Try not to discuss a single thought across multiple paragraphs, if possible  
        * Sometimes this is not possible, but...try.  
  * Use empty lines to separate chunks.  


# Creating Vector Databases  

To create a vector database, the first step is to [gather data](ai_media/llama.cpp/vector_databases?id=gathering-data); then, you can use a tool like [Qdrant](ai_media/llama.cpp/qdrant).  Know that you will need to download an [embedding model built for training the vector database](ai_media/llama.cpp/llama_basics?id=embedding-models).  
