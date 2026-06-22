# 01 — Tokens & Embeddings

## 🧠 The One Idea

**Models don't read words — they read numbers. Tokens chop text into chunks, and embeddings turn
each chunk into a vector whose *direction* captures meaning.** Words used in similar ways end up as
nearby vectors, which is why "king − man + woman ≈ queen" works.

The one-liner: **"Tokens are sub-word chunks turned into IDs; embeddings map each to a vector where
distance ≈ semantic similarity."**

---

## 1. Tokenization

- Text is split into **tokens** — usually **sub-word** pieces, not whole words (e.g. "tokenization"
  → `token` + `ization`).
- Sub-words handle rare/unseen words by composing them from known pieces (e.g. BPE — byte-pair
  encoding).
- Rough rule: **~1 token ≈ 4 characters ≈ ¾ of a word** in English.

Each token maps to an integer **ID** from a fixed **vocabulary**.

---

## 2. Why tokens matter to you

- **Cost & limits** are measured in tokens — context windows, API pricing, latency all scale with
  token count.
- Code, non-English, and weird formatting use **more** tokens than plain English.
- Trimming prompts = trimming tokens = cheaper + faster.

---

## 3. Embeddings

- An **embedding** is a vector (e.g. 768 or 1536 floats) representing a token (or a whole sentence/
  document).
- The model **learns** these vectors so that **semantic similarity = geometric closeness**.
- "Paris" and "London" land near each other; "Paris" and "banana" don't.

---

## 4. Why direction = meaning

- Similarity is measured by **cosine similarity** (angle between vectors).
- Relationships become **directions**: the "capital-of" or "plural-of" shift is a consistent vector
  offset.
- This geometric structure is what makes vectors searchable — the basis of **RAG** (Lesson 04).

---

## 5. Where engineers use embeddings directly

- **Semantic search:** embed a query and documents, return the nearest vectors (meaning, not keyword
  match).
- **RAG retrieval:** find the most relevant chunks to feed the LLM.
- **Clustering / dedup / classification / recommendations:** all "group by closeness in vector
  space".
- Stored and searched in a **vector database** (FAISS, pgvector, Pinecone) with **ANN** (approximate
  nearest-neighbour) for speed.

---

## 🎤 Say this in the interview

- *"Tokens are sub-word chunks mapped to integer IDs — roughly a token per ¾ word — and cost,
  context limits, and latency are all measured in tokens."*
- *"An embedding is a learned vector where semantic similarity is geometric distance, measured by
  cosine similarity."*
- *"That's what powers semantic search and RAG: embed query and documents, then nearest-neighbour
  search in a vector DB."*

➡️ **Next:** [02 — Transformer intuition](./02_Transformer_Intuition.md)
