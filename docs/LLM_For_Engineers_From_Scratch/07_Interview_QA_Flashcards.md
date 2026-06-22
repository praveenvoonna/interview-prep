# 07 — Interview Q&A Flashcards

Rapid-fire. Cover the answer, say it aloud, then check. Read twice. The last section is your
**paper pitch** — rehearse it.

---

## Fundamentals

**Q: What is an LLM?**
A next-token predictor: it models P(next token | context) and generates autoregressively.

**Q: What are parameters?**
The learned weights; a 7B model has 7 billion of them, tuned to minimize next-token error.

**Q: Training vs inference?**
Training adjusts weights on data (once, expensive); inference runs the frozen model on prompts.

**Q: The training stages?**
Pre-training (language) → instruction tuning (follow instructions) → RLHF (align to preferences).

---

## Tokens & embeddings

**Q: What's a token?**
A sub-word chunk mapped to an integer ID; ~¾ of a word; cost/limits are measured in tokens.

**Q: What's an embedding?**
A learned vector where semantic similarity = geometric closeness (cosine similarity).

**Q: What's a vector DB for?**
Fast nearest-neighbour search over embeddings — powers semantic search and RAG.

---

## Transformers

**Q: What is self-attention?**
Each token's Query matches every token's Key to weight their Values — context captured in parallel.

**Q: Why multi-head?**
Different heads learn different relationships (syntax, reference, topic) simultaneously.

**Q: What's the context window?**
Max tokens attended at once; attention cost is quadratic in length.

---

## Prompting & CoT

**Q: Zero- vs few-shot?**
Zero-shot = just ask; few-shot = include examples to show the pattern.

**Q: What is Chain-of-Thought?**
Asking for step-by-step reasoning; each reasoning token conditions the next, improving multi-step
tasks.

**Q: Temperature?**
Low = deterministic/precise; high = creative/varied.

---

## RAG vs fine-tuning

**Q: What is RAG?**
Retrieve relevant chunks via embedding search, inject into the prompt → grounded, current, citable.

**Q: RAG vs fine-tuning?**
Knowledge gap → RAG; behaviour/skill/format gap → fine-tune. They combine.

**Q: What dominates RAG quality?**
Retrieval — chunking, embeddings, hybrid search, re-ranking.

---

## Fine-tuning & LoRA

**Q: What is LoRA?**
Freeze the base, train small low-rank adapters (~0.1–1% of params) — cheap, no catastrophic
forgetting, swappable.

**Q: What is QLoRA?**
Quantize the frozen base to 4-bit, then LoRA on top — fine-tune big models on one GPU.

---

## Serving

**Q: Prefill vs decode?**
Prefill processes the prompt in parallel (first token); decode generates sequentially.

**Q: Key serving optimizations?**
KV-cache, continuous batching (vLLM), quantization, distillation.

**Q: API vs self-host?**
API = fast, no infra, data leaves; self-host = control, privacy, fixed cost at scale.

---

## 🎯 Example paper pitch (AppLLM)

*"AppLLM runs an LLM inside the VPP data plane. It uses structured **Chain-of-Thought** for reasoning
quality and **LoRA** for parameter-efficient fine-tuning, because the data plane has tight latency
and memory budgets — full fine-tuning was infeasible. So it's a real-systems LLM story: CoT for
quality, LoRA + quantization for fitting the constraints."*

---

🎉 **You finished LLM for Engineers.** Three-sentence core: **next-token predictor**, **attention
captures context**, **adapt via RAG (knowledge) or LoRA fine-tuning (behaviour)** — then land the
AppLLM example.
