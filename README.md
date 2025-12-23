# Mini RAG – Construction Marketplace Assistant

## Overview
This project implements a simple Retrieval-Augmented Generation (RAG) pipeline that answers user queries using internal construction documents. The assistant retrieves relevant document chunks and generates answers strictly grounded in the retrieved content.

## Embeddings & Retrieval
- TF-IDF embeddings using scikit-learn  
- Cosine similarity for semantic retrieval  
- Top-k retrieval with a similarity score threshold to filter weak matches  

## LLM
- Local open-source LLM via Ollama (`qwen2.5:3b`)  
- Chosen to avoid external APIs and demonstrate local inference  

## Grounding & Hallucination Control
The LLM is explicitly instructed to:
- Use only retrieved document chunks  
- Avoid inference and causal reasoning  
- Abstain when information is not present in the documents  

## Transparency
For every query, the system clearly prints:
- Retrieved document chunks with similarity scores  
- Final generated answer  

## Limitations
The assistant does not infer missing information. If a question cannot be answered explicitly from the documents, it returns an abstention response.
