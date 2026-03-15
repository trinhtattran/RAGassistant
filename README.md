Parkinson's Disease RAG System

Overview
This project demonstrates how to build a retrieval‑augmented generation (RAG) assistant for clinical decision support in Parkinson's disease. It ingests a curated corpus of 17 PDF documents, splits them into overlapping chunks, embeds each chunk using a transformer model, stores the embeddings in a vector database (Chroma), retrieves the most relevant context for a user’s question, and generates a concise answer using a fine‑tuned language model. The system is implemented in a Google Colab notebook and is intended as a learning resource for building RAG pipelines.

Quick Start
1. **Mount Google Drive**: In Colab, run `drive.mount('/content/drive')` to access your PDFs stored in Drive.
2. **Install dependencies**: Use pip to install required packages: `langchain-core`, `langchain-community`, `langchain-text-splitters`, `sentence-transformers`, `chromadb`, `gradio`, `transformers`, and `pypdf`.
3. **Load PDFs**: Point the loader to your data directory (e.g. `DATA_DIR = '/content/drive/MyDrive/RAG/data'`) and read all 17 PDF files using `PyPDFLoader`.
4. **Chunk documents**: Split each document into overlapping chunks with `RecursiveCharacterTextSplitter` (e.g. 1,000 characters with 200-character overlap).
5. **Create embeddings and vector store**: Use `SentenceTransformer('all-MiniLM-L6-v2')` to embed chunks and store them in a persistent Chroma collection.
6. **Define retrieval function**: Write a function that embeds the user’s query and uses the vector store to return the top‑K most similar chunks along with their metadata.
7. **Generate answers**: Prompt a generative model such as `flan‑t5‑large` with the retrieved context and your question to produce a bullet‑listed answer.
   
Data Ingestion
All source material lives in the `data` directory of your Google Drive. The documents cover definitions, clinical features, biomarkers, imaging studies, rating scales and differential diagnosis of Parkinson's disease. Use the `PyPDFLoader` from `langchain-community` to load each PDF and convert pages to `Document` objects. When reading PDFs it’s important to mount Google Drive so that the file paths are accessible.

Chunking
After loading the PDFs, split each `Document` into smaller segments to provide the retriever with fine‑grained context. Using `RecursiveCharacterTextSplitter` with a `chunk_size` of 1000 characters and `chunk_overlap` of 200 characters ensures that adjacent chunks share context. The resulting list of chunks is used throughout the rest of the pipeline.

Embeddings and Vector Store
Embed each chunk using a sentence embedding model. The example notebook uses the `sentence-transformers/all-MiniLM-L6-v2` model for its balance of performance and speed. Compute embeddings in batches to save memory and time. Then instantiate a persistent Chroma client and add your embeddings, raw text and metadata to a collection. Using a persistent client means your vector store is saved across sessions without extra `.persist()` calls.

Retrieval and Answer Generation
Define a retrieval function that accepts a user’s question, embeds it with the same model and queries the Chroma collection. Return the top‑K documents and their metadata. For generation, assemble a prompt that includes the retrieved context with citation tags (e.g. `[1]`, `[2]`) and instruct the model to answer using only the provided context. The notebook uses `flan‑t5‑large`, loaded via Hugging Face Transformers, to generate concise answers with citations. Adjust `top_k` and `max_new_tokens` to suit your needs.

Example Usage
Below is a basic example showing how to pose a question to the system in Colab:

```python
# Ask about motor symptoms
response = answer_question(
    'What are the motor symptoms of Parkinson's disease?',
    top_k=8,
    max_new_tokens=256
)
print(response)
```

If your data contains the relevant information, the model will produce a bullet‑listed answer citing the documents that contain definitions of bradykinesia, resting tremor, rigidity and postural instability【283092665182710†L35-L60】. You can ask about non‑motor symptoms, atypical parkinsonism or imaging techniques as long as your documents include those topics.

Troubleshooting and Tips
• Increase `top_k` if the model returns 'I don't know'; this allows the retriever to pull more context.
• Verify that your PDF parser ingests the text you expect. If some PDF is poorly parsed, consider extracting key sections manually or converting them to plain text.
• Use more descriptive queries; embedding models rely on semantic similarity, so including key terms (e.g. ‘bradykinesia’, ‘levodopa’) improves retrieval.
• Experiment with chunk sizes; shorter chunks give finer granularity but may miss context, while larger chunks capture more information but reduce retrieval specificity.

Dependencies
- Python 3.10 or later
- Google Colab notebook environment
- Packages: `langchain-core`, `langchain-community`, `langchain-text-splitters`, `sentence-transformers`, `chromadb`, `gradio`, `transformers`, `pypdf`
- ~10 GB of RAM to run `flan‑t5‑large` (use a smaller model if memory is limited)


Disclaimer
This project is for educational and prototyping purposes only. It demonstrates how retrieval‑augmented generation can be applied to a corpus of Parkinson's disease literature to answer clinical questions. It is **not** intended to replace medical advice or professional guidelines. The answers generated by the model are limited by the quality and scope of the input data; always consult primary sources or healthcare professionals for clinical decision‑making.
