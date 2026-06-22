# 02 — Transformer Intuition

## 🧠 The One Idea

**Attention lets each word look at every other word and decide which ones matter for understanding
it.** In "the animal didn't cross the street because *it* was tired", attention is how the model
links "it" to "animal", not "street". The transformer stacks this looking-around operation many times
to build meaning.

The one-liner: **"A transformer uses self-attention so every token weighs the relevance of all other
tokens in parallel — that's how it captures context."**

---

## 1. Why transformers replaced RNNs

- Old RNNs read text **one token at a time, sequentially** → slow, and they forgot distant context.
- Transformers process the **whole sequence in parallel** and let any token attend directly to any
  other → fast on GPUs, great at long-range links. ("Attention Is All You Need", 2017.)

---

## 2. Attention in plain English

For each token, the model computes three vectors:

- **Query (Q):** what am I looking for?
- **Key (K):** what do I offer?
- **Value (V):** what do I contribute if chosen?

A token's new representation = a **weighted sum of all Values**, where the weights come from how well
its **Query** matches each **Key**. High match → pay more attention.

---

## 3. Multi-head attention

- Run attention **several times in parallel** ("heads"), each learning a different relationship —
  one head tracks syntax, another references, another topic.
- Concatenate the heads → richer representation than a single attention pass.

---

## 4. The full block & depth

A transformer layer = **attention** (mix information across tokens) + a **feed-forward network**
(process each token) + **residual connections** and **layer norm** (stabilize training). Stack dozens
of these layers → each builds on the last, from surface patterns to abstract meaning.

A small detail that matters: since attention has no inherent order, **positional encodings** are
added so the model knows token *order*.

---

## 5. The context window

- The **context window** is how many tokens the model can attend to at once (e.g. 8K, 128K, 1M).
- Attention cost grows **quadratically** with sequence length (every token attends to every token) →
  why long context is expensive and why efficiency tricks exist.
- Anything outside the window is invisible — the model has no memory beyond it (hence RAG/summaries).

---

## 🎤 Say this in the interview

- *"A transformer's core is self-attention: each token forms a query and matches it against every
  other token's key to decide how much of their value to absorb — so it captures context in
  parallel."*
- *"Multi-head attention learns several relationships at once, and stacking many layers builds from
  surface patterns to abstract meaning."*
- *"The context window is the attention span; cost is quadratic in length, which is why long context
  is pricey and we lean on RAG for anything outside it."*

➡️ **Next:** [03 — Prompting & Chain-of-Thought](./03_Prompting_And_Chain_Of_Thought.md)
