# rag-retrieval-only-system
<img width="921" height="411" alt="architecture_diagram drawio" src="https://github.com/user-attachments/assets/77365e6a-b18e-4034-b73a-b0f02a608e35" />

## LangChain

This project uses the open-source LangChain library to orchestrate the document ingestion and semantic retrieval pipeline. LangChain provides high-level abstractions that simplify building retrieval systems by managing document loading, text splitting, embedding generation, and vector similarity search.

For this assignment, LangChain is used only for the retrieval component of RAG, without any LLM-based text generation.

#### Features Used from LangChain

Document loading and indexing

Text chunking with configurable chunk size and overlap

Vector embedding generation

Semantic similarity search over vector databases

Metadata handling for traceability (document name, page number, chunk index)

Task-Specific Configuration

The following components are used in this implementation and can be replaced with alternatives if needed:

Document format, loader, and splitter:
PDF documents using PyPDFLoader and RecursiveCharacterTextSplitter

Embedding model:
HuggingFace sentence-transformers/all-MiniLM-L6-v2

Vector database:
FAISS (local, in-memory, free tier)

## Document Loading

The system begins by loading the input PDF document from the local directory using LangChain’s PyPDFLoader.
Each page of the PDF is converted into a structured Document object that contains:

- Page content (text)

- Metadata such as page number and source file name

This structured format allows downstream processing while preserving document traceability.


