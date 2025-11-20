## Simple Retrieval-Augmented Generation (RAG) System — *AstraGarden*

**Student:** Asma Begum  
**Course Assignment:** Implementing a Simple RAG System  
**Repository:** AI-ML-Assignment-5-Simple-RAG  

---

##  Overview

This project implements a simple **Retrieval-Augmented Generation (RAG)** system using a custom-built knowledge base (KB) about *AstraGarden*, a fictional orbital biodome designed for long-term agricultural research in microgravity.

The project demonstrates how RAG grounds an LLM’s responses in external context and reduces hallucinations.


## Models Used

| Component | Model | Source |
|----------|--------|--------|
| **Embedding Model** | `sentence-transformers/all-MiniLM-L6-v2` | Hugging Face |
| **LLM (Generator)** | `google/flan-t5-small` | Hugging Face |

---

##  Repository Contents

```
simple_rag.ipynb        # Main notebook implementing the full RAG pipeline
kb_astragarden.txt      # Fictional knowledge base (3 paragraphs)
requirements.txt        # Dependencies
README.md               # This documentation file
```

---

# RAG Pipeline Overview

The RAG system follows the assignment requirements:

---

## 1. Knowledge Base Creation & Chunking

A fictional KB describing AstraGarden was created with three paragraphs detailing:

- The **Crop Ring** (partial gravity farming of potatoes, dwarf wheat, leafy greens)  
- The **Research Ring** (plant biology, microgravity studies, bio-reactors)  
- The **Habitat Ring** (crew life, mental health, mission goals)

The KB is chunked by paragraph into 3 text chunks.

---

## 2. Embedding & Indexing

- Each chunk is embedded using **all-MiniLM-L6-v2**.
- Embeddings are stored in a simple **in-memory vector store**:

```python
kb_index = {
    "chunks": [...],
    "embeddings": numpy array of vectors
}
```

---

## 3. Retrieval (Similarity Search)

For each query:

1. The query is embedded using the same embedding model.
2. Cosine similarity is computed between the query vector and all chunk embeddings.
3. The top-2 most relevant chunks are returned.

This ensures the LLM receives appropriate grounded context.

---

## 4. Generation (Prompt Augmentation)

The final prompt contains:

```
Context:
- <retrieved chunk 1>
- <retrieved chunk 2>

Question: <query>

If the answer is not in the context, respond:
"I don't know based on the provided context."
```

`flan-t5-small` generates the final response, constrained by the retrieved KB text.

---

## 5. Testing (Three Required Test Cases)

### **Test Case 1 — Factual**
**Query:**  
*What kinds of crops are grown in AstraGarden?*

**Retrieved Chunks:**  
- Chunk 0 (Crop Ring: potatoes, dwarf wheat, leafy greens)  
- Another supporting chunk  

**Model Behavior:**  
Correctly provides crops from the retrieved context.

**RAG successfully grounds the answer.**

---

### **Test Case 2 — Foil / Not in KB**
**Query:**  
*Who is the current director of NASA?*

**Retrieved Chunks:**  
- Any AstraGarden-related chunk (none mention NASA)

**Expected Response:**  
“I don’t know based on the provided context.”

 **RAG prevents hallucination** by constraining the LLM.

---

### **Test Case 3 — Synthesis**
**Query:**  
*How does AstraGarden recycle waste to support long-term missions?*

**Retrieved Chunks:**  
- Chunk 1 (bio-reactors & recycling)  
- Chunk 2 (long-term mission sustainability)

**Model Behavior:**  
Combines both chunks to explain recycling → fertilizer & water → supports long missions.

**RAG enables multi-chunk synthesis.**

---

# Results Analysis

### When the answer *is in* the KB  
RAG retrieves the correct chunk and grounds the answer.

###  When the answer *is not* in the KB  
The system discourages hallucination by instructing the LLM to admit uncertainty.

###  When multi-chunk reasoning is needed  
RAG provides enough context for synthesis.

###  Final Conclusion  
RAG significantly improves reliability and prevents the LLM from inventing facts.  
It demonstrates how retrieval serves as a grounding mechanism for LLM reasoning.

