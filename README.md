# Mini RAG – Construction Marketplace Assistant

## Overview
This project implements a simple Retrieval-Augmented Generation (RAG) pipeline for a construction marketplace use case. The assistant answers user queries using a small set of internal construction documents (policies, FAQs, and specifications).

The system retrieves relevant document chunks and generates answers that are **strictly grounded in the retrieved context**, prioritizing correctness, transparency, and hallucination avoidance.

---

## System Architecture

1. Document ingestion from local Markdown files  
2. Document chunking into meaningful segments  
3. Vectorization of chunks using TF-IDF embeddings  
4. Semantic retrieval using cosine similarity  
5. Answer generation using a locally hosted LLM (Ollama) with strict grounding rules  

---

## Embeddings & Retrieval

- **Embedding method:** TF-IDF (`scikit-learn`)
- **Similarity metric:** Cosine similarity
- **Retrieval strategy:** Top-k retrieval with a similarity score threshold

TF-IDF embeddings were chosen to keep the system lightweight, fully local, and easy to reason about. While TF-IDF is lexical rather than deep semantic, it satisfies the assignment requirement of cosine-similarity–based retrieval and provides deterministic, explainable results.

### Extensibility
The retrieval layer is modular and can be easily upgraded to:
- Sentence-transformer embeddings (e.g., `all-MiniLM-L6-v2`)
- FAISS or Pinecone for scalable vector search

This tradeoff was made intentionally to prioritize clarity and robustness within the scope of the assignment.

---

## Chunking Strategy

Documents are chunked by logical paragraph boundaries with a maximum word limit per chunk. This approach preserves section-level meaning while preventing overly large chunks from dominating retrieval.

Given the small, well-structured nature of the provided documents, paragraph-based chunking provides sufficient contextual coherence without over-fragmentation.

### Extensibility
For larger or noisier datasets, this strategy could be replaced with:
- Recursive character-based chunking
- Overlapping chunks to preserve edge context

---

## LLM

- **Model:** `qwen2.5:3b`
- **Runtime:** Ollama (local inference)

A local open-source LLM was used to avoid external API dependencies and to demonstrate on-device inference. This aligns with the assignment’s bonus criteria for local LLM usage.

---

## Grounding & Hallucination Control

The LLM is explicitly instructed to:

- Use **only** the retrieved document chunks
- Avoid inference, causal reasoning, or external knowledge
- Restate or summarize content exactly as written
- Abstain when information is not present in the documents

If the answer cannot be derived explicitly from the retrieved context, the system responds with:

> “The information is not available in the provided documents.”

This strict grounding ensures reliability and prevents unsupported claims.

---

## Transparency & Explainability

For every query, the system prints:

- Retrieved document chunks along with similarity scores  
- The final generated answer  

This makes it easy to trace how each answer was produced and which parts of the documents were used.

---

## Quality Evaluation (Manual)

A small manual evaluation was conducted using representative test queries derived from the documents.

| Question | Retrieval Relevant? | Answer Grounded? | Notes |
|--------|---------------------|------------------|------|
| What operational mechanisms manage construction delays? | Yes | Yes | Correct section retrieved and summarized |
| What quality assurance measures are mentioned? | Yes | Yes | Section heading and description used |
| Does Indecimal use AI for construction planning? | No | Yes (abstained) | No hallucination |
| What factors affect construction project delays? | Partial | Yes | Closest grounded information returned |

This evaluation confirms that the system:
- Retrieves relevant chunks when available
- Avoids hallucinations
- Safely abstains when information is missing

---

## Limitations

- The system does not perform deep semantic reasoning beyond retrieved text
- TF-IDF embeddings may miss semantic matches where vocabulary differs significantly
- No automated evaluation metrics are implemented

These limitations are acknowledged and can be addressed through future enhancements.

---

## Future Improvements

- Replace TF-IDF with sentence-transformer embeddings
- Integrate FAISS or Pinecone for scalable vector search
- Add automated evaluation for retrieval relevance and answer faithfulness
- Extend the system to agentic workflows with tool usage

---

## How to Run

1. Install dependencies:
   ```bash
   pip install scikit-learn requests
   ```
2. Ensure Ollama is running locally and the model is available:
   ```bash
   ollama pull qwen2.5:3b
   ```
3. Run the Jupyter notebook:
   ```bash
   mini_rag.ipynb
   ```

