# LLM Terms  

---  

# Large Language Model  

A <font color="green">large language model</font> (<font color="green">LLM</font>) is a type of artificial intelligence that can generate human language and perform related tasks. These models are trained on huge datasets, often containing billions of words, enabling them to learn patterns and rules of language similar to how humans learn to communicate through exposure to language.  

<font color="green">LLM</font>s are the entire point of this section of my notes.  

---  

# Retrieval-Augmented Generation  

The <font color="green">RAG</font> (<font color="green">Retrieval-Augmented Generation</font>) approach for [LLM](ai_media/llama.cpp/terms?id=large-language-model) applications is a technique that enhances the capabilities of LLMs by combining their powerful generative abilities with the ability to retrieve relevant information from external data sources (read: data they were not trained on). Essentially, it gives LLMs access to specific, up-to-date, or domain-specific knowledge that might not be included in their initial training data.  


RAG follows a few key steps:  
* **Retrieval**: When a user poses a query, a retrieval component searches an external data source (like a knowledge base or [vector database](ai_media/llama.cpp/terms?id=vector-database)) for relevant information.  
* **Augmentation**: The retrieved information is then integrated into the user's query, creating an "augmented prompt" that provides context to the LLM.  
* **Generation**: The LLM receives the augmented prompt and uses the retrieved information to generate a more accurate, relevant, and contextually informed response.  

Why is RAG important?
* **Up-to-date and accurate responses** - LLMs are trained on static data, so their knowledge has a cut-off date.  
  * <font color="green">Retrieval-Augmented Generation</font> allows them to access the latest information, enabling them to provide current and reliable answers.  
* **Reduced hallucinations** - By grounding the LLM's responses in factual, external data, <font color="green">Retrieval-Augmented Generation</font> helps reduce the model's tendency to "hallucinate" or provide incorrect information.  
* **Domain-specific knowledge** - <font color="green">Retrieval-Augmented Generation</font> allows businesses to tailor LLMs to their specific needs by connecting them to internal company data or specialized knowledge bases.  
* **Efficiency and cost-effectiveness** - <font color="green">Retrieval-Augmented Generation</font> offers a more efficient and cost-effective way to introduce new data to an LLM compared to retraining or fine-tuning the model. 

Think of it like this:  

Imagine an LLM as a student who has learned a lot from their textbooks (training data). When asked a question, they can answer based on what they've learned. But if the question requires specific or recent information not in their textbooks, they might guess or give a general answer. Now, imagine the student has access to a library (external data source) and a librarian (retrieval component) who can quickly find relevant books (information retrieval) related to the question. The student can then use the information from the books (augmented prompt) to give a more precise and informed answer (response generation).  

> <font color="green">Retrieval-Augmented Generation</font> is a powerful technique that significantly improves the performance of LLMs by enabling them to access and leverage external knowledge, leading to more accurate, reliable, and contextually relevant applications.  

# Token  

A <font color="green">token</font> is the fundamental unit of text that the model processes. It can be a whole word, a part of a word, a single character, or even a punctuation mark. LLMs break down text into tokens as part of the tokenization process, which helps them understand and generate language more effectively.  

---  

# Quantization  

<font color="green">Quantization</font> in Large Language Models (LLMs) is a technique used to reduce the size and computational requirements of these models by modifying the precision of their weights and activations. This process involves converting high-precision data, such as 32-bit floating-point numbers, into lower-precision formats like 8-bit integers or even 4-bit integers, which significantly reduces the memory footprint and computational load of the models.  

An LLM is usually <font color="green">Quantized</font>, as it significantly lowers the file size of the model without too much of an impact on the model's accuracy. It should be noted that this impact _does_ impact quality, but its usually not too noticeable; furthermore, if the model is not <font color="green">quantized</font>, you probably will not be able to fit it into your RAM / VRAM on your GPU.  

Here are common <font color="green">quantization</font> types: 

| Level | Reference | Notes |  
| --- | --- | --- |  
| 2 | Q4_0 |  |  
| 3 | Q4_1 |  |  
| 8 | Q5_0 |  |  
| 9 | Q5_1 |  |  
| 19 | IQ2_XXS |  |  
| 20 | IQ2_XS |  |  
| 28 | IQ2_S |  |  
| 29 | IQ2_M |  |  
| 24 | IQ1_S |  |  
| 31 | IQ1_M |  |  
| 36 | TQ1_0 |  |  
| 37 | TQ2_0 |  |  
| 10 | Q2_K |  |  
| 21 | Q2_K_S  |  |  
| 23 | IQ3_XXS  |  |  
| 26 | IQ3_S  |  |  
| 27 | IQ3_M  |  |  
| 12 | Q3_K  |  |  
| 22 | IQ3_XS  |  |  
| 11 | Q3_K_S  |  |  
| 12 | Q3_K_M  |  |  
| 13 | Q3_K_L  |  |  
| 25 | IQ4_NL  |  |  
| 30 | IQ4_XS  |  |  
| 15 | Q4_K  |  |  
| 14 | Q4_K_S  |  |  
| 15 | Q4_K_M  |  |  
| 17 | Q5_K  |  |  
| 16 | Q5_K_S  |  |  
| 17 | Q5_K_M | My personal favorite, as it significantly shrinks<br>the file while having the smallest impact on accuracy - but YMMV. |  
| 18 | Q6_K  |  |  
| 7 | Q8_0  |  |  
| 1 | F16  |  |  
| 32 | BF16  |  |  
| 0 | F32  |  |  
|  | COPY  | simply copies tensors - it does not <font color="green">Quantize</font> |  

---  

# Vector Database  

A <font color="green">Vector Database</font> stores and manages [vector embeddings](ai_media/llama.cpp/terms?id=vector-embeddings).  

---

# Vector Embeddings  

<font color="green">Vector Embeddings</font> are numerical representations of words, phrases, or entire documents that capture their semantic meaning. These embeddings allow LLMs to understand and compare the relationships between different pieces of text, making them a fundamental part of how LLMs process and generate language. <font color="green">Vector embeddings</font> are essentially numerical vectors, where each number in the vector represents a feature or aspect of the text being represented.  

LLMs use a process called "embedding" to convert text into these numerical representations. This process involves mapping words or phrases to a high-dimensional vector space, where similar words or phrases are clustered together.  

LLMs use these embeddings to understand the meaning of text and the relationships between different words and phrases. This allows them to perform tasks such as:  
* Semantic search: Finding similar documents or text based on their meaning.  
* Text summarization: Generating concise summaries of text.  
* Machine translation: Translating text from one language to another.  
* Question answering: Answering questions based on the content of a document.  

**Example**  
The word "apple" might be represented as a vector like [0.1, 0.5, 0.2], and the word "orange" might be represented as a vector like [0.2, 0.6, 0.1]. LLMs would then use the distances and relationships between these vectors to understand that "apple" and "orange" are related.  