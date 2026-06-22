# 04 — RAG (Retrieval-Augmented Generation)

## 🧠 The One Idea

**RAG is an open-book exam: instead of relying on what the model memorized, you fetch the relevant
pages and hand them to it before it answers.** You retrieve facts from *your* data, stuff them into
the prompt as context, and the model generates a grounded answer — current, citable, and far less
prone to hallucination.

The one-liner: **"RAG retrieves relevant chunks via embedding search and injects them into the
prompt, grounding the answer in your data without retraining."**

---

## 1. Why RAG exists

- LLMs have a **knowledge cutoff** and **don't know your private data**.
- Retraining for every new document is absurdly expensive.
- RAG adds knowledge **at inference time** — update the data, not the model.

---

## 2. The pipeline

```
Ingest (once):  docs → split into chunks → embed → store in vector DB
Query (runtime): question → embed → nearest-neighbour search → top-k chunks
                 → build prompt (chunks + question) → LLM → grounded answer
```

Two phases: **indexing** (offline) and **retrieval + generation** (per query).

---

## 3. The moving parts

- **Chunking:** split docs into passages (too big = noise/cost; too small = lost context). Overlap a
  little.
- **Embedding model:** turns chunks and the query into vectors (Lesson 01).
- **Vector DB + ANN:** fast approximate nearest-neighbour search (pgvector, FAISS, Pinecone).
- **Retriever:** returns top-k by cosine similarity; often **re-ranked** for quality.
- **Generator:** the LLM, prompted with retrieved context + the question (+ "cite sources").

---

## 4. RAG vs fine-tuning (the key decision)

| | RAG | Fine-tuning |
|---|---|---|
| Adds | **knowledge** (facts) | **skill/behaviour/style** |
| Freshness | live — update the data | frozen at training time |
| Cost | cheap, no training | training cost |
| Traceability | can **cite** sources | opaque |

**Rule:** *knowledge gap → RAG; behaviour/format gap → fine-tune.* They combine well.

---

## 5. Making RAG work in practice

- **Retrieval quality is everything** — garbage chunks → garbage answer. Invest in chunking,
  embeddings, and re-ranking.
- **Hybrid search:** combine keyword (BM25) + vector for the best recall.
- **Cite + ground:** ask the model to answer *only* from context and cite — and say "I don't know"
  when context lacks the answer.
- **Evaluate:** measure retrieval hit-rate and answer faithfulness, not just vibes.

---

## 🎤 Say this in the interview

- *"RAG retrieves relevant chunks with embedding search and injects them into the prompt, so the
  answer is grounded in my data — current and citable — without retraining."*
- *"I choose RAG when the gap is knowledge and fine-tuning when it's behaviour or format; they
  compose."*
- *"In practice retrieval quality dominates — chunking, embeddings, hybrid search, and re-ranking —
  and I ground the model to answer only from context and cite sources."*

➡️ **Next:** [05 — Fine-tuning & LoRA](./05_Fine_Tuning_And_LoRA.md)
