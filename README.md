
Parkinson’s Disease RAG Assistant

Overview
This project builds a Retrieval-Augmented Generation (RAG) system that answers questions about Parkinson’s disease and atypical parkinsonism using scientific and clinical documents.

Instead of relying only on a language model’s internal knowledge, the system:
1. Retrieves relevant passages from medical PDFs
2. Uses those passages as context
3. Generates grounded answers with citations

This improves reliability and reduces hallucination compared to standard LLM responses.

System Architecture
PDF documents
↓
Document cleaning
↓
Chunking
↓
Embedding (MiniLM)
↓
Vector database (Chroma)
↓
Hybrid retrieval
   • Dense search (embeddings)
   • BM25 keyword search
↓
Context formatting
↓
Prompt engineering
↓
LLM generation (FLAN‑T5)
↓
Answer + cited sources

Key Features

Hybrid Retrieval
The system combines two retrieval methods.

Dense Retrieval
Uses sentence embeddings to find semantic matches.
Model used: sentence-transformers/all-MiniLM-L6-v2
Vector database: ChromaDB

Sparse Retrieval (BM25)
BM25 retrieves documents using keyword matching, which helps capture:
• rare medical terms
• acronyms
• specific symptom descriptions

Hybrid Ranking
Results from both retrievers are combined and deduplicated to improve recall and precision.

Context Filtering
Medical PDFs often contain sections that are not useful for answering questions such as:
• references
• bibliographies
• copyright pages
• front matter

These sections can degrade retrieval quality. The system filters chunks that likely contain DOI lists, citation clusters, or reference sections to improve context precision.

Prompt Engineering
The system uses a structured prompt to encourage grounded answers.

Prompt rules:
• Use only the retrieved context
• Do not invent facts
• Provide evidence bullets
• Cite sources as (Doc 1), (Doc 2)

Example Output Format

Evidence:
• Early recurrent falls may indicate atypical parkinsonism (Doc 1)
• Poor response to levodopa is a key diagnostic clue (Doc 2)

Final Answer:
Early falls and poor levodopa response are important red flags suggesting atypical parkinsonism.

LLM Generation
The system uses a lightweight local model:
google/flan-t5-small

Advantages:
• runs locally in Colab
• low RAM requirements
• suitable for small RAG systems

Repository Structure

pd-rag-assistant/
│
├── data/
│   ├── Recognizing Atypical Parkinsonisms.pdf
│   ├── Parkinson’s Disease Pathogenesis and Clinical Aspects.pdf
│   └── How to approach a patient with parkinsonism.pdf
│
├── notebooks/
│   └── pd_rag_assistant.ipynb
│
├── chroma_db/
│   └── persistent vector database
│
└── README.md

Installation

pip install langchain
pip install langchain-community
pip install langchain-text-splitters
pip install chromadb
pip install sentence-transformers
pip install rank_bm25
pip install transformers
pip install pdfplumber

Running the Project

1. Place Parkinson’s disease PDFs in the data folder.
2. Run the notebook cells in order.

The pipeline will:
• Load PDFs
• Clean and filter text
• Split documents into chunks
• Generate embeddings
• Store vectors in Chroma
• Create hybrid retrievers
• Generate answers

Example Query

What are red flags that suggest atypical parkinsonism rather than idiopathic Parkinson's disease?

Example Output

Evidence:
• Absence of tremor can be a red flag for atypical parkinsonism (Doc 2)
• Early recurrent falls may suggest progressive supranuclear palsy (Doc 1)
• Poor response to levodopa is another distinguishing feature (Doc 2)

Final Answer:
Early falls, poor levodopa response, and atypical tremor presentation are common red flags suggesting atypical parkinsonism rather than idiopathic Parkinson’s disease.

Evaluation Approach

Context Precision:
Are the retrieved passages actually relevant?

Faithfulness:
Is the answer supported by the retrieved context?

Future Improvements

• reranking retrieved passages
• cross‑encoder relevance scoring
• Graph RAG for structured medical relationships
• stronger instruction‑tuned models
• automated evaluation metrics

Educational Goal

This project demonstrates:
• document ingestion
• vector databases
• hybrid retrieval
• prompt engineering
• grounded generation

The goal is to build a trustworthy medical knowledge assistant that answers questions based on verifiable sources.

