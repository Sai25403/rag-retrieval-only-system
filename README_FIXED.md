
# Semantic Document Retrieval System (RAG – Retrieval Only)

## Overview
This project implements a **backend-only semantic document retrieval system**, focused exclusively on the **retrieval component of Retrieval-Augmented Generation (RAG)**.  
No Large Language Models (LLMs) or text generation are used.

The system accepts a natural language query and returns the **Top-K most semantically relevant document chunks**, along with full metadata for traceability.

---

## High-Level Architecture

```
User Query
   ↓
Query Embedding
   ↓
Vector Similarity Search (FAISS – Cosine Similarity)
   ↓
Top-K Relevant Chunks
   ↓
Chunks + Metadata (page, source, chunk_id, score)
```

---

## Tech Stack
- **Python**
- **LangChain** (PDF loading & chunking)
- **Sentence-Transformers**
  - `sentence-transformers/all-MiniLM-L6-v2`
- **FAISS** (local, free-tier vector database)

---

## Document Loading
- PDF documents are loaded from a local directory using `PyPDFLoader`.
- Each page retains metadata such as:
  - Source file name
  - Page number
- Metadata is preserved end-to-end for evaluation and auditing.

---

## Text Chunking Strategy
- Documents are split using `RecursiveCharacterTextSplitter`.
- Chunking parameters are configurable:

| Parameter | Value |
|---------|------|
| Chunk Size | 400 |
| Chunk Overlap | 80 |

### Rationale
- Smaller chunks improve semantic precision.
- Overlap prevents context loss at boundaries.
- Clause-level structure is preserved for standards-style documents (ISO 27001).

Each chunk is assigned a **global sequential chunk ID**, ensuring unique identification across the dataset.

---

## Text Cleaning Strategy
- Structural newlines, headings, and clause formatting are **preserved during embedding** to maintain semantic accuracy.
- Only excessive blank lines or noise are removed.
- Formatting normalization (indentation, spacing) is applied **only at output time** for readability.

This separation ensures retrieval quality is not degraded by aggressive preprocessing.

---

## Embedding Model
**Model Used:**  
`sentence-transformers/all-MiniLM-L6-v2`

### Why this model?
- Free and open-source
- Lightweight and fast
- Produces 384-dimensional embeddings
- Well-suited for semantic similarity tasks

Embeddings are generated manually using `SentenceTransformer` for full control and transparency.

---

## Vector Database (FAISS)
- FAISS is used in **local, in-memory mode**
- Index type: `IndexFlatIP`
- All vectors are **L2-normalized**
- Inner Product similarity is used to compute **Cosine Similarity**

### Similarity & Distance
- **Similarity Score:** Cosine similarity (range: 0–1)
- **Distance:** Computed as `1 − similarity_score`

---

## Semantic Retrieval Logic
1. User query is embedded using the same embedding model.
2. FAISS performs vector similarity search.
3. Top-K most similar chunks are retrieved.
4. Each result includes:
   - Chunk text
   - Similarity score
   - Distance
   - Source document
   - Page number
   - Chunk ID

The value of **K is configurable** (default: 5).

---

## Sample Queries Used
1. *What does ISO 27001 say about information security risk assessment?*
2. *What are the requirements for information security objectives?*
3. *How often should risk assessments be performed according to ISO 27001?*

---

## Sample Output Format
```json
{
  "chunk_text": "...",
  "score": 0.34,
  "distance": 0.66,
  "source": "example.pdf",
  "page": 9,
  "chunk_id": 42
}
```

---

## Notes on Retrieval Behavior
- Semantic retrieval may rank broader governance sections higher due to contextual overlap.
- Increasing Top-K ensures the most precise clause (e.g., Clause 6.1.2) is retrieved.
- This behavior is expected and documented as part of the evaluation.

---

## How to Run
1. Install dependencies:
   ```bash
   pip install sentence-transformers faiss-cpu langchain langchain-community
   ```

2. Place PDF files inside the `data/` directory.

3. Run:
   ```bash
   python semantic_retrieval.py
   ```

---

## Evaluation Readiness
- Fully backend-only
- Free-tier tools only
- Deterministic and reproducible
- GitHub-render friendly (Markdown compliant)

This project is designed for easy review, validation, and extension.
