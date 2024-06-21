# AI相关的一些概念描述

## RAG

参考文档

```
https://www.deeplearning.ai/resources/generative-ai-courses-guide/
```

> Retrieval Augmented Generation(增强式检索);现在常用的是对文档的检索。

### RAG的流程

RAG的执行过程通常由两部分组成：

> 1. 解析文档到数据库，通常是一个向量数据库(vector database);
> 2. 检索数据库中的数据；

基本流程如下图

![](./image/1-RAG.png)

> - **Extract**: Documents come in all sorts of file formats (.doc, .pdf, etc.) and contain all sorts of data formats (text, tables, images, movies). These must be extracted and put into a format that can be processed by the next stages. 
> - **Chunking**: Text data is broken into smaller chunks – a process inventively named ‘chunking’.
> - **Embedding**: Converting a chunk into a ‘**dense vector**’ that represents the meaning of the text. 
> - **Loading:** Adding the embedding and original data to a database.
> - Database
>   - The database is going to provide storage for the embedding and data. Often these are **vector databases** due to the embedding, but **graph databases** and traditional databases are also used.
> - Query
>   - Embedding: The query is converted to a dense vector using the same embedding model.
>   - Retrieval: The stored and query vectors represent meaning, so retrieval is the process of finding the k entries in the database that are ‘closest’ to the query vector. Lots of details here!
>   - k results are provided to an LLM which uses them to form an ‘augmented’ response.

## Hugging Face

> Hugging Face Hub is an open platform that hosts models,datasets, and machine learning demos that are called Hugging Face Spaces.





## Transformer



## Different between machine learning, Natural Language Processing, and Neural Network.

Large Language Models (LLMs) like GPT-3, which powers ChatGPT, leverage several advanced technologies from the fields of machine learning, natural language processing, and neural networks. Below is an explanation of how these technologies are utilized:

#### Machine Learning (ML)

**Definition:** LLMs are fundamentally machine learning models that learn from vast amounts of text data to understand and generate human language.

**Key Techniques:**

- **Supervised Learning:** While LLMs are primarily trained in an unsupervised fashion, fine-tuning them for specific tasks often involves supervised learning using labeled datasets.
- **Unsupervised Learning:** The training process typically starts with unsupervised learning where the model learns patterns and structures in the text data without explicit labels.

#### Natural Language Processing (NLP)

**Definition:** LLMs are designed to handle a variety of NLP tasks, making them capable of understanding and generating human language.

**Key Techniques:**

- **Tokenization:** The text input is broken down into tokens, which can be words or subwords, for the model to process.
- **Word Embeddings:** Words are converted into numerical vectors that capture their meanings and relationships.
- **Attention Mechanisms:** Particularly the Transformer architecture, which uses self-attention to weigh the importance of different words in a sentence.
- **Language Modeling:** Predicting the next word in a sequence, which is fundamental to generating coherent text.

#### Neural Networks

**Definition:** LLMs like GPT-3 are based on deep neural networks, specifically the Transformer architecture, which has proven highly effective for NLP tasks.

**Key Components:**

- Transformer Architecture:

   

  Consists of layers of self-attention mechanisms and feedforward neural networks.

  - **Self-Attention:** Allows the model to consider the context of each word in relation to others in a sentence, enabling it to capture long-range dependencies.
  - **Feedforward Neural Networks:** Applied after the self-attention mechanism to transform the data further.

- **Layers:** GPT-3, for example, uses 96 layers in its largest version, allowing it to learn complex patterns and representations.

- Training Process:

  - **Pretraining:** The model is initially trained on a large corpus of text to learn general language patterns.
  - **Fine-Tuning:** Optionally, the pretrained model can be fine-tuned on specific datasets to improve performance on particular tasks.

### Summary

In essence, LLMs like GPT-3 integrate advances from all three domains:

| Feature                         | Utilization in LLMs (e.g., GPT-3)                            |
| ------------------------------- | ------------------------------------------------------------ |
| **Machine Learning**            | Uses both unsupervised learning (pretraining) and supervised learning (fine-tuning). |
| **Natural Language Processing** | Tokenization, word embeddings, attention mechanisms, and language modeling are core techniques. |
| **Neural Networks**             | Built on the Transformer architecture, employing self-attention and feedforward layers. |

### Conclusion

Large Language Models such as GPT-3 employ:

- **Machine Learning** techniques to learn from data.
- **NLP** methods to understand and generate human language.
- **Neural Networks**, particularly the Transformer architecture, to effectively model complex language patterns.

This combination enables LLMs to perform a wide array of tasks, from answering questions and generating text to translation and summarization, making them incredibly versatile and powerful tools in the field of artificial intelligence.

