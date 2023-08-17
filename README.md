# Llama2 Embeddings FastAPI Service

## Introduction

The Llama2 Embedding Server is designed to facilitate and optimize the process of obtaining text embeddings using different LLMs via llama_cpp and langchain.
To avoid wasting computation, these embeddings are cached in SQLite and retrieved if they have already been computed before.
To speed up the process of loading multiple LLMs, optional RAM Disks can be used, and the process for creating and managing them is handled automatically for you.


Some additional useful endpoints are provided, such as computing semantic similarity between submitted text strings (using various measures of similarity,
such as cosine similarity, but also more esoteric measures like <a href="https://blogs.sas.com/content/iml/2021/05/03/examples-hoeffding-d.html">Hoeffding's D</a> and
<a href="https://www.sciencedirect.com/science/article/abs/pii/S0950705121008297">HSIC</a>), and semantic search across all your cached embeddings using FAISS vector searching.


## Screenshot
<img src="https://github.com/Dicklesworthstone/llama_embeddings_fastapi_service/raw/main/Llama2-FastAPI-Service-%20Swagger%20Screenshot.png" alt="Llama2 FastAPI Service Swagger UI">

## TLDR: Quick Setup
 
If you want to try it quickly on a fresh Ubuntu 22+ machine (warning, this will install docker using apt):

        git clone https://github.com/Dicklesworthstone/llama_embeddings_fastapi_service
        cd llama_embeddings_fastapi_service
        chmod +x setup_dockerized_app_on_fresh_machine.sh
        sudo ./setup_dockerized_app_on_fresh_machine.sh
    
Then open a browser to <code>&lt;your_static_ip_address&gt;:8089</code> if you're using a VPS.
Or to <code>localhost:8089</code> if you're using your own machine--but remember not to run untrusted code with sudo on your own machine!
Consider using a cheap VPS for experimentation.

Watch the automated setup process in action <a href="https://asciinema.org/a/601603">here</a>.

## Features

1. <strong>Text Embedding Computation:</strong> Utilizes pre-trained LLama2 and other LLMs via llama_cpp and langchain to generate embeddings for any provided text, including token-level embeddings that capture more nuanced information about the content.
2. <strong>Embedding Caching:</strong> Efficiently stores and retrieves computed embeddings in SQLite, minimizing redundant computations. It supports caching both fixed-sized embedding vectors and token-level embeddings.
3. <strong>Advanced Similarity Measurements and Retrieval:</strong> Offers various measures of similarity like cosine similarity, Hoeffding's D, HSIC, and semantic search across cached embeddings using FAISS vector searching.
4. <strong>File Processing for Documents:</strong> Submit plaintext files or PDFs (not requiring OCR) to get back a ZIP file or JSON response containing embeddings for each sentence, organized in various ways like records, table, etc., using Pandas to_json() function.
5. <strong>Token-Level Embeddings and Combined Feature Vectors:</strong> Provides token-level embeddings to capture the context of each token in the input string. Introduces combined feature vectors by computing the column-wise mean, min, max, and std. deviation of the token-level embedding matrix, allowing comparison of unequal length strings.
6. <strong>RAM Disk Usage:</strong> Optionally uses RAM Disk to store models for faster access and execution. Automatically handles the creation and management of RAM Disks.
7. <strong>Robust Exception Handling:</strong> Features comprehensive exception management to ensure system resilience.
8. <strong>Interactive API Documentation:</strong> Integrates with Swagger UI for an interactive and user-friendly experience, accommodating large result sets without crashing.
9. <strong>Scalability and Concurrency:</strong> Built on the FastAPI framework, handles concurrent requests and supports parallel inference with configurable concurrency levels.
10. <strong>Flexible Configurations:</strong> Offers configurable settings through environment variables and input parameters, including response formats like JSON or ZIP files.
11. <strong>Comprehensive Logging:</strong> Captures essential information with detailed logs, without overwhelming storage or readability.
12. <strong>Support for Multiple Models and Measures:</strong> Accommodates multiple embedding models and similarity measures, allowing flexibility and customization based on user needs.

## Demo Screen Recording in Action
   <a href="https://asciinema.org/a/39dZ8vv9nkcNygasUl35wnBPq">Here</a> is the live console output while interacting with the Swagger page to make requests.

## Requirements
      
      fastapi
      pydantic
      uvicorn
      sqlalchemy
      python-decouple
      psutil
      aiosqlite
      faiss-cpu
      pandas
      PyPDF2
      python-multipart
      python-magic
      langchain
      scikit-learn
      llama-cpp-python
      httpx
      numba
      scipy
      hyppo

## Running the Application

You can run the application using the following command:

        python llama_2_embeddings_fastapi_server.py

The server will start on <code>0.0.0.0</code> at the port defined by the <code>LLAMA_EMBEDDING_SERVER_LISTEN_PORT</code> variable.

 Access the Swagger UI:

        http://localhost:&lt;LLAMA_EMBEDDING_SERVER_LISTEN_PORT&gt;

## Configuration

You can configure the service easily by editing the included <code>.env</code> file. Here's a list of available configuration options:

<ul>
    <li><strong>EMBEDDING_MODEL</strong>: The name of the embedding model to use (e.g., "llama2", "bert", "gpt-3").</li>
    <li><strong>EMBEDDING_DIMENSION</strong>: The dimension of the embedding vectors.</li>
    <li><strong>USE_RAM_DISK</strong>: Set to "True" to enable RAM Disk usage for faster model access.</li>
    <li><strong>CONCURRENCY_LEVEL</strong>: The level of concurrency to handle multiple requests simultaneously.</li>
    <li><strong>RESPONSE_FORMAT</strong>: The format of the response, either "json" or "zip".</li>
    <li><strong>MAX_CACHE_SIZE</strong>: The maximum number of embeddings to cache in the SQLite database.</li>
    <li><strong>SIMILARITY_MEASURE</strong>: The measure used for similarity calculation, e.g., "cosine", "hoeffding_d", "hsic".</li>
    <li><strong>ALLOWED_EXTENSIONS</strong>: List of allowed file extensions for document processing.</li>
    <li><strong>USE_SECURITY_TOKEN</strong>: Whether to use a security token for authentication (e.g., "True").</li>
    <li><strong>USE_PARALLEL_INFERENCE_QUEUE</strong>: Use parallel processing for inference (e.g., "True").</li>
    <li><strong>MAX_CONCURRENT_PARALLEL_INFERENCE_TASKS</strong>: Maximum number of parallel inference tasks.</li>
    <li><strong>DEFAULT_MODEL_NAME</strong>: Default model name to use.</li>
    <li><strong>LLM_CONTEXT_SIZE_IN_TOKENS</strong>: Context size in tokens for LLM.</li>
    <li><strong>LLAMA_EMBEDDING_SERVER_LISTEN_PORT</strong>: Port number for the service.</li>
    <li><strong>MINIMUM_STRING_LENGTH_FOR_DOCUMENT_EMBEDDING</strong>: Minimum string length for document embedding.</li>
    <li><strong>MAX_RETRIES</strong>: Maximum retries for locked database.</li>
    <li><strong>DB_WRITE_BATCH_SIZE</strong>: Database write batch size.</li>
    <li><strong>RETRY_DELAY_BASE_SECONDS</strong>: Retry delay base in seconds.</li>
    <li><strong>JITTER_FACTOR</strong>: Jitter factor for retries.</li>
    <li><strong>RAMDISK_PATH</strong>: Path to the RAM disk.</li>
    <li><strong>RAMDISK_SIZE_IN_GB</strong>: RAM disk size in GB.</li>
    <li><strong>PARALLEL_INFERENCE_PROVIDER_TYPE</strong>: Type of parallel inference provider.</li>
    <li><strong>NUM_PARALLEL_INFERENCE_PROVIDERS</strong>: Number of parallel inference providers.</li>
    <li><strong>ENABLE_SENTENCE_SPLITTING</strong>: Whether to enable sentence splitting for document processing.</li>
    <li><strong>MAX_ALLOWED_TOKENS_PER_DOCUMENT</strong>: Maximum number of tokens allowed per document.</li>
    <li><strong>EMBEDDING_CACHE_CLEANUP_INTERVAL</strong>: Interval for cleaning up the embedding cache.</li>
    <li><strong>EMBEDDING_CACHE_CLEANUP_BATCH_SIZE</strong>: Batch size for cleaning up the embedding cache.</li>
    <li><strong>... (Add any additional configurations here) ...</strong></li>
</ul>

## Contributing
If you'd like to contribute to the project, please submit a pull request.

## License
This project is licensed under the MIT License.

## Setup and Installation on a Fresh Ubuntu Machine (TLDR)

# Clone the repository
git clone https://github.com/Dicklesworthstone/llama_embeddings_fastapi_service.git
cd llama_embeddings_fastapi_service

# Make the setup script executable
chmod +x setup_dockerized_app_on_fresh_machine.sh

# Run the setup script (requires sudo)
sudo ./setup_dockerized_app_on_fresh_machine.sh
    </code></pre>
    <p>Open a browser to <code>&lt;your_static_ip_address&gt;:8089</code> if you're using a VPS, or <code>localhost:8089</code> if you're using your own machine.</p>
