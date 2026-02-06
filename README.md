# RAG Semantic Document Retrieval 

## Architecture Overview

<img width="921" height="411" alt="architecture_diagram drawio" src="https://github.com/user-attachments/assets/77365e6a-b18e-4034-b73a-b0f02a608e35" />

This project implements a Retrieval-Augmented Generation (RAG) style semantic
retrieval system using LangChain, HuggingFace embeddings, and a FAISS vector
database. Documents are loaded from a local directory, cleaned, chunked,
embedded, and indexed for cosine-similarity-based semantic search.

The solution is fully local, free-tier compatible, and evaluation-friendly,
with complete traceability to document source, page number, and chunk ID.

## LangChain

This project uses the open-source LangChain library to orchestrate the document ingestion and semantic retrieval pipeline. LangChain provides high-level abstractions that simplify building retrieval systems by managing document loading, text splitting, embedding generation, and vector similarity search.

For this assignment, LangChain is used only for the retrieval component of RAG, without any LLM-based text generation.

### Features Used from LangChain

- Document loading and indexing

- Text chunking with configurable chunk size and overlap

- Vector embedding generation

- Semantic similarity search over vector databases

Metadata handling for traceability (document name, page number, chunk index)

#### Task-Specific Configuration

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

## Text Chunking Strategy
- Chunking is implemented using `RecursiveCharacterTextSplitter`.
- Chunking parameters:
  - `chunk_size = 400`
  - `chunk_overlap = 80`
- Chunk IDs are generated **per document** to ensure stable identification.
- Metadata retained per chunk:
  - Document name
  - Page number
  - Chunk index

---

## 4. Text Cleaning
Before chunking and embedding, document text is normalized:
- ISO header/footer noise (e.g., `ISO/IEC 27001:2022(E)`) is removed
- Newline characters inside sentences are replaced with spaces
- Excess whitespace is normalized

This improves embedding quality and cosine similarity accuracy.

---

## 5. Embedding Model
**Model Used:**

sentence-transformers/all-MiniLM-L6-v2

### Rationale:
- Free and open-source
- Lightweight and fast (384 dimensions)
- Widely used for semantic search
- Suitable for on-prem and enterprise environments

---

## 6. Vector Database
**FAISS (Local, Free Tier)**

### Rationale:
- Fully local and free
- Easy to inspect and validate
- Supports inner-product search for cosine similarity
- Suitable for evaluation environments

---

## 7. Similarity Scoring
- Cosine similarity is used for semantic retrieval.
- Embeddings are L2-normalized.
- FAISS inner-product search is applied.
- Scores range from `0` to `1`, where higher values indicate stronger semantic relevance.

Moderate scores are expected for broad queries, while clause-specific queries
produce higher similarity values.

---

## 8. How to Run the Code

### Install dependencies
```bash
pip install sentence-transformers faiss-cpu langchain==0.1.20 langchain-community==0.0.38 pypdf
```

### Run the pipeline
```bash
python rag_pipeline.py
```

---

## 9. Sample Queries Used for Testing
1. ISO 27001 Clause 6.1.2 information security risk assessment requirements
2. ISO 27001 Clause 5.1 leadership and commitment responsibilities
3. ISO 27001 Clause 4.3 determining the scope of the ISMS
4. ISO 27001 Annex A access control requirements
5. ISO 27001 Clause 9.2 internal audit requirements

---

## 10. Sample Output Format
```json
{
  "chunk_text": "...",
  "score": 0.82,
  "source": "ISO_IEC_27001_2022.pdf",
  "page": 9,
  "chunk_id": 12
}
```

---

## 11. Notes on Determinism
- Page numbers reflect physical PDF page indices returned by the document loader.
- Chunk IDs are generated dynamically per document and may vary based on
  chunking configuration.
- These variations do not affect retrieval accuracy or reproducibility.


