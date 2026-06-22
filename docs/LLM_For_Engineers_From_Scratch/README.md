# 🤖 LLM for Engineers From Scratch — A Crash Course (in plain English)

A from-zero course on **how large language models actually work**, aimed at engineers — so you
can speak fluently about LLM internals (training, parameters), RAG, and fine-tuning, using the
published **AppLLM** paper (CoT + LoRA fine-tuning in the VPP data plane) as a running example.

> **How to read this:** go in order, 00 → 07. Each lesson starts with a plain-English analogy
> (**"The One Idea"**), then the real mechanics, then **"Say this in the interview"** lines.

> **Status:** 📝 *Syllabus ready — lessons to be written.* This README is the outline and the
> reference you can study from today.

---

## 📂 The lessons (planned, read in order)

### Part A — The mental model
- **00 — What is an LLM** — next-token prediction, training vs inference, what "parameters" are.
- **01 — Tokens & embeddings** — tokenization, vectors, why text becomes numbers.
- **02 — Transformer intuition** — attention in plain English, layers, context window.

### Part B — Using them well
- **03 — Prompting & Chain-of-Thought** — zero/few-shot, CoT (AppLLM uses structured CoT).
- **04 — RAG** — retrieval + generation, embeddings/vector search, grounding, when RAG beats
  fine-tuning.

### Part C — Adapting & serving
- **05 — Fine-tuning & LoRA** — full fine-tune vs parameter-efficient (LoRA), what AppLLM did
  and why it's cheap.
- **06 — Inference & serving** — latency, batching, quantization, cost; running models in a data
  plane (VPP) as in AppLLM.

### Part D — Practice
- **07 — Interview Q&A flashcards** — attention/RAG/LoRA rapid-fire + how to pitch the AppLLM example.

---

## 🧠 The whole course in three sentences
1. **An LLM is a next-token predictor:** trained on huge text, it learns a probability
   distribution over the next token given the context.
2. **Attention is how it weighs context:** the transformer lets each token "look at" the relevant
   earlier tokens to decide what comes next.
3. **You adapt it two ways:** RAG injects fresh, grounded knowledge at inference; fine-tuning
   (cheaply via LoRA) changes behaviour — pick based on whether the gap is *knowledge* or *skill*.

---

## 🔗 Companion material
- The **AppLLM** paper (CoT + LoRA in the VPP data plane) — this course is the backstory
  you can narrate around it.

When the lessons are written, start at **00**.
