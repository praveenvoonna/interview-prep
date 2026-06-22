# 05 — Fine-tuning & LoRA

## 🧠 The One Idea

**Fine-tuning teaches an existing model a new skill or style by training it further on your examples.
LoRA does this cheaply by freezing the giant model and training only a tiny set of add-on weights.**
Instead of re-tuning all 7 billion parameters, you nudge a few million — far less compute, almost the
same effect.

The one-liner: **"Fine-tuning adapts behaviour by further training; LoRA freezes the base and trains
small low-rank adapters, making it cheap and modular."**

---

## 1. What fine-tuning changes (vs RAG)

- **RAG** adds *knowledge* at inference (Lesson 04).
- **Fine-tuning** changes the *weights* → adapts **behaviour, format, tone, or a specialized skill**
  the base model isn't good at.
- Use it when prompting + RAG can't get consistent enough behaviour.

---

## 2. Full fine-tuning and its cost

- **Full fine-tuning:** update **all** parameters on your dataset.
- Powerful but **expensive**: needs huge GPU memory, produces a full-size copy per task, risks
  **catastrophic forgetting** of general ability.
- Rarely the right first move for a small team.

---

## 3. LoRA — the cheap, clever shortcut

- **LoRA (Low-Rank Adaptation):** **freeze** the original weights; inject small trainable **low-rank
  matrices** alongside them. Only those adapters learn.
- Train **~0.1–1%** of the parameters → fits on modest GPUs, trains fast.
- The base model is untouched, so no catastrophic forgetting.

*(This is what the **AppLLM** paper used — LoRA fine-tuning in the VPP data plane. Be ready to
explain why: parameter-efficient adaptation under tight compute.)*

---

## 4. Why LoRA is also operationally nice

- **Tiny artifacts:** an adapter is megabytes, not gigabytes.
- **Swappable:** load different adapters on **one** base model for different tasks — hot-swap at
  serve time.
- **QLoRA:** quantize the frozen base to 4-bit, then LoRA on top → fine-tune big models on a single
  GPU.

LoRA is the headline example of **PEFT** (parameter-efficient fine-tuning).

---

## 5. When to use what

- **Prompt engineering** first — free, instant.
- **RAG** when the gap is **knowledge**.
- **LoRA fine-tune** when the gap is **behaviour/skill/format** and you have good examples.
- **Full fine-tune** only for deep specialization with serious resources.
- Often **combined:** a LoRA-tuned model *plus* RAG.

---

## 🎤 Say this in the interview

- *"Fine-tuning changes the weights to adapt behaviour, whereas RAG adds knowledge — knowledge gap →
  RAG, behaviour gap → fine-tune."*
- *"LoRA freezes the base and trains small low-rank adapters — about 0.1–1% of parameters — so it's
  cheap, avoids catastrophic forgetting, and the adapters are tiny and swappable."*
- *"That's exactly what the AppLLM paper did: LoRA for parameter-efficient adaptation under tight
  data-plane compute, paired with structured Chain-of-Thought."*

➡️ **Next:** [06 — Inference & serving](./06_Inference_And_Serving.md)
