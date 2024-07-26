# Hybrid Search RAG Pipeline

This README provides instructions on setting up a Hybrid Search RAG (Retrieval-Augmented Generation) Pipeline using Hugging Face, FastEmbeddings, and LlamaIndex. This pipeline involves loading and chunking documents, indexing them, retrieving relevant documents, and re-ranking these documents to provide the most accurate results.

## Installation

To set up the pipeline, you need to install several libraries:

1. **`langchain-community` and `chromadb`**: These libraries provide community-driven extensions and a vector storage system to handle the document embeddings.
2. **`langchain` and `pypdf`**: These libraries are used for handling various document types and processing PDF files.
3. **`sentence-transformers`**: This library is used for generating embeddings for the documents.

## Setting Up Environment Variables

To access Hugging Face models, you will need a Hugging Face API token. You will be prompted to enter this token, which will then be stored as an environment variable.

## Loading and Chunking Data

The document loading and chunking process involves the following steps:

1. **Loading the Document**: The `PyPDFLoader` from Langchain is used to load the document. This loader supports PDF files and extracts the text for further processing.
2. **Chunking the Document**: The `RecursiveCharacterTextSplitter` is used to split the loaded document into smaller, manageable chunks. This splitter allows for customization of chunk size and overlap to ensure the text is split in a meaningful way.

## Indexing the Document

To enable efficient retrieval of document chunks, the following steps are performed:

1. **Creating Embeddings**: The `HuggingFaceInferenceAPIEmbeddings` class is used to create embeddings for the document chunks. These embeddings represent the semantic content of each chunk.
2. **Storing Embeddings**: The `Chroma` library is used to store the generated embeddings. This vector storage system allows for fast and efficient retrieval based on vector similarity.

## Retrieving Documents

The retrieval process involves creating a retriever that uses vector similarity to find the most relevant document chunks based on a given query:

1. **Vector Search Retriever**: A retriever is created to perform a normal vector search. This retriever will retrieve a specified number of top documents (e.g., 4) based on their similarity to the query.

## Re-Ranking Documents

To improve the relevance of the retrieved documents, a re-ranking process is applied:

1. **Cross-Encoder Re-Ranker**: A cross-encoder model from Hugging Face (e.g., `BAAI/bge-reranker-base`) is used to re-rank the retrieved documents. This model scores the documents based on their relevance to the query, and the top documents are selected.
2. **Contextual Compression Retriever**: The re-ranked documents are then used to create a `ContextualCompressionRetriever`. This retriever incorporates the re-ranking scores to provide a more accurate set of documents.

## Defining the Language Model and Prompt Template

The final step involves defining a language model and a prompt template to generate the response based on the re-ranked documents:

1. **Language Model**: A Hugging Face language model (e.g., `HuggingFaceH4/zephyr-7b-alpha`) is used to generate responses. This model is configured with parameters such as temperature, maximum token length, and other relevant settings.
2. **Prompt Template**: A prompt template is defined to instruct the language model on how to generate the response. The template includes placeholders for the context (re-ranked documents) and the user query. The template ensures that the model provides truthful and direct answers based on the context.

## Langchain Expression Language (LCEL)

To streamline the workflow, Langchain Expression Language (LCEL) is used to define a chain of operations:

1. **Runnable Components**: The workflow is composed of various runnable components, including the retriever, prompt template, language model, and output parser.
2. **Chain Execution**: The chain is executed by passing the query through each component, resulting in a final response that combines retrieval, re-ranking, and generation steps.

## Full Workflow

The full workflow involves the following steps:

1. **Load and Chunk the Document**: Use `PyPDFLoader` to load the document and `RecursiveCharacterTextSplitter` to chunk it.
2. **Index the Document**: Generate embeddings using `HuggingFaceInferenceAPIEmbeddings` and store them with `Chroma`.
3. **Retrieve Documents**: Create a vector search retriever to fetch the top documents based on the query.
4. **Re-Rank Documents**: Use a cross-encoder re-ranker and `ContextualCompressionRetriever` to refine the retrieved documents.
5. **Generate Response**: Define a prompt template and use a Hugging Face language model to generate the final response.
6. **Execute the Chain**: Combine the steps into a Langchain Expression Language (LCEL) chain and execute it to obtain the response.

This setup enables efficient and accurate retrieval, ranking, and generation of responses, providing a powerful solution for handling complex queries over large document collections.
