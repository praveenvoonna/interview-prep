# 03 — Prompting & Chain-of-Thought

## 🧠 The One Idea

**The prompt is the program: since the model just continues text, *how* you frame the input steers
the output.** Give it role, context, examples, and a request for step-by-step reasoning, and the same
model produces dramatically better answers — no retraining required.

The one-liner: **"Prompting is programming an LLM in natural language; Chain-of-Thought asks it to
reason step by step, which improves multi-step tasks."**

---

## 1. The prompting ladder

- **Zero-shot:** just ask. ("Classify this review as positive/negative.")
- **Few-shot:** include a handful of **examples** in the prompt so it infers the pattern.
- **System prompt:** set persistent role/rules ("You are a precise Go code reviewer.").

More guidance → more reliable, at the cost of more tokens.

---

## 2. Anatomy of a good prompt

1. **Role/persona** — who the model should be.
2. **Context** — the relevant facts/data (where RAG plugs in).
3. **Task** — the specific instruction.
4. **Format** — exactly how to output (JSON, bullets, schema).
5. **Examples** — for few-shot.

Being explicit about **format** is the highest-leverage tweak for production use.

---

## 3. Chain-of-Thought (CoT)

- Asking the model to **"think step by step"** makes it generate intermediate reasoning before the
  answer.
- Big gains on **multi-step** problems: math, logic, planning, code.
- Why it works: each reasoning token becomes context for the next, so the model "computes" across
  steps instead of guessing the answer in one jump.

*(The published **AppLLM** paper uses structured CoT + LoRA in the VPP data plane — a good example
to narrate: CoT for reasoning quality, LoRA for cheap adaptation.)*

---

## 4. CoT variants & related techniques

- **Few-shot CoT:** show worked examples *with* their reasoning.
- **Self-consistency:** sample several reasoning paths, take the majority answer.
- **ReAct:** interleave **reasoning + actions** (tool/function calls) — the basis of agents.
- **Structured output / function calling:** force JSON or a tool schema for reliable downstream use.

---

## 5. Practical engineering notes

- **Temperature:** low (0–0.3) = deterministic/precise; high = creative/varied.
- **Hide CoT in production:** reasoning helps quality but is verbose/costly — often kept internal,
  return only the answer.
- **Guardrails:** validate/parse the output; never trust free-form text blindly.
- **Token budget:** examples and long context cost money — trim to what helps.

---

## 🎤 Say this in the interview

- *"Prompting is programming in natural language — role, context, task, format, examples — and being
  explicit about output format is the biggest reliability win."*
- *"Chain-of-Thought asks the model to reason step by step; each reasoning token conditions the next,
  so multi-step tasks improve a lot."*
- *"The AppLLM paper combined structured CoT with LoRA fine-tuning in the VPP data plane — CoT for
  reasoning quality, LoRA for cheap, parameter-efficient adaptation."*

➡️ **Next:** [04 — RAG](./04_RAG.md)
