# RAGassistant
Parkinson’s Disease RAG Assistant
Overview

This project builds a Retrieval-Augmented Generation (RAG) system that answers questions about Parkinson’s disease and atypical parkinsonism using scientific and clinical documents.

Instead of relying only on a language model’s internal knowledge, the system:

Retrieves relevant passages from medical PDFs.

Uses those passages as context.

Generates grounded answers with citations.

This improves reliability and reduces hallucination compared to standard LLM responses.

The system was built as part of a Domain RAG Assistant project, focusing on medical knowledge retrieval and grounded responses.

System Architecture

The pipeline follows a standard RAG workflow.

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
   ├─ Dense search (embeddings)
   └─ BM25 keyword search
      ↓
Context formatting
      ↓
Prompt engineering
      ↓
LLM generation (FLAN-T5)
      ↓
Answer + cited sources
Key Features
Hybrid Retrieval

The system combines two retrieval methods:

Dense Retrieval

Uses sentence embeddings to find semantic matches.

Model:

sentence-transformers/all-MiniLM-L6-v2

Vector database:

ChromaDB
Sparse Retrieval (BM25)

BM25 retrieves documents using keyword matching, which helps capture:

rare medical terms

acronyms

specific symptom descriptions

Hybrid Ranking

Results from both retrievers are combined and deduplicated to improve recall and precision.

Context Filtering

Medical PDFs often contain:

references

bibliographies

copyright pages

front matter

These sections can degrade retrieval quality.

The system removes chunks that likely contain:

DOI lists

citation clusters

reference sections

copyright/license text

This improves context precision.

Prompt Engineering

The system uses a structured prompt to encourage grounded answers.

Key prompt rules:

Use only the retrieved context

Do not invent facts

Provide evidence bullets

Cite sources as (Doc 1), (Doc 2)

Example format:

Evidence:
- Early recurrent falls may indicate atypical parkinsonism (Doc 1)
- Poor response to levodopa is a key diagnostic clue (Doc 2)

Final Answer:
Early falls and poor levodopa response are important red flags suggesting atypical parkinsonism.

This approach improves faithfulness and interpretability.

LLM Generation

The current implementation uses a lightweight local model:

google/flan-t5-small

Advantages:

runs locally in Colab

low RAM requirements

sufficient for structured RAG prompting

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

Install required packages:

pip install langchain
pip install langchain-community
pip install langchain-text-splitters
pip install chromadb
pip install sentence-transformers
pip install rank_bm25
pip install transformers
pip install pdfplumber
Running the Project

Place Parkinson’s disease PDFs in:

/RAG/data

Run the notebook cells in order.

The pipeline will:

Load PDFs

Clean and filter text

Split documents into chunks

Build embeddings

Store vectors in Chroma

Create hybrid retrievers

Generate answers

Example Query
What are red flags that suggest atypical parkinsonism rather than idiopathic Parkinson's disease?

Example output:

Evidence:
- Absence of tremor can be a red flag for atypical parkinsonism (Doc 2)
- Early recurrent falls may suggest progressive supranuclear palsy (Doc 1)
- Poor response to levodopa is another distinguishing feature (Doc 2)

Final Answer:
Early falls, poor levodopa response, and atypical tremor presentation are common red flags suggesting atypical parkinsonism rather than idiopathic Parkinson’s disease.

Sources:

Doc 1: How to approach a patient with parkinsonism.pdf
Doc 2: Recognizing Atypical Parkinsonisms.pdf
Evaluation Approach

The system focuses on two important RAG metrics:

Context Precision

Are the retrieved passages actually relevant?

Faithfulness

Is the answer supported by the context?

Inspection tools are included to display retrieved chunks before generation.

Future Improvements

Potential next steps include:

reranking retrieved passages

adding cross-encoder relevance scoring

implementing Graph RAG for structured medical relationships

using stronger instruction-tuned models

adding automated evaluation metrics

Educational Goal

This project demonstrates:

document ingestion

vector databases

hybrid retrieval

prompt engineering

grounded generation

The goal is to build a trustworthy medical knowledge assistant that answers questions based on verifiable sources.
