# 06 — Inference & Serving

## 🧠 The One Idea

**Serving an LLM is the engineering half: the model is fixed, and your job is to make it fast, cheap,
and reliable under load.** Generation is sequential (one token at a time), memory-hungry, and
latency-sensitive — so the whole game is batching, caching, and shrinking the model without breaking
it.

The one-liner: **"Inference is autoregressive and memory-bound; you optimize with batching,
KV-caching, and quantization to trade a little quality for big latency/cost wins."**

---

## 1. The two phases of a request

- **Prefill:** process the whole prompt in parallel → produces the first token (compute-heavy).
- **Decode:** generate the rest **one token at a time**, each step depending on the last
  (latency-bound, sequential).

This is why **long outputs** are slow and why streaming tokens to the user feels better.

---

## 2. The metrics that matter

- **TTFT** (time to first token) — driven by prefill; what the user feels first.
- **TPOT / inter-token latency** — decode speed.
- **Throughput** (tokens/sec across all users) — drives cost.
- **Cost per 1K tokens** — the business number.

You trade these against each other (bigger batches = more throughput but higher latency).

---

## 3. The key optimizations

- **KV-cache:** cache past tokens' keys/values so each new token doesn't recompute the whole
  sequence — essential, but it eats GPU memory (grows with context length).
- **Continuous batching:** dynamically pack many requests through the GPU together (vLLM) → huge
  throughput gains.
- **Quantization:** store weights in 8-bit/4-bit instead of 16-bit → less memory, faster, slightly
  lower quality. Lets big models fit smaller GPUs.
- **Distillation:** train a small model to mimic a big one for cheaper serving.

---

## 4. Build vs buy

- **API (OpenAI/Anthropic/Bedrock):** no infra, pay per token, fast to ship; less control, data
  leaves your boundary.
- **Self-host (open weights + vLLM/TGI):** control, privacy, fixed cost at scale; you own the GPUs
  and ops.
- Pick by **data sensitivity, scale economics, and latency/control needs**.

---

## 5. Serving in a data plane (the AppLLM angle)

- Running a model **inside VPP** means tight **latency and memory budgets** — exactly where
  quantization, small/LoRA-adapted models, and careful batching pay off.
- It shows LLMs done not just via an API, but under **real systems constraints** — a strong,
  differentiated story.

---

## 🎤 Say this in the interview

- *"Inference has a parallel prefill then sequential decode; it's memory-bound, so I optimize TTFT
  and throughput with KV-caching and continuous batching."*
- *"Quantization to 8- or 4-bit trades a little quality for big memory and latency wins, letting big
  models fit smaller GPUs."*
- *"I weigh API vs self-host on data sensitivity, scale economics, and control — and in AppLLM a
  model runs under VPP's tight latency/memory budgets, which is where these optimizations really
  matter."*

➡️ **Next:** [07 — Interview Q&A flashcards](./07_Interview_QA_Flashcards.md)
