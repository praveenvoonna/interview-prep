# 00 — What is an LLM

## 🧠 The One Idea

**An LLM is a gigantic autocomplete: given the text so far, it predicts the most likely next word
(token), over and over.** That's it. Everything impressive — answering questions, writing code,
reasoning — emerges from doing next-token prediction extremely well, trained on a huge slice of human
text.

The one-liner: **"An LLM is a next-token predictor: it models P(next token | context) and generates
by sampling that distribution one token at a time."**

---

## 1. Next-token prediction

- Input: a sequence of tokens (your prompt).
- Output: a **probability distribution** over the entire vocabulary for the *next* token.
- Pick one (greedy or sampled), append it, feed it back in, repeat → text is generated
  **autoregressively**, one token at a time.

---

## 2. Training vs inference

- **Training:** show the model billions of text examples; it predicts the next token, compares to
  the real one, and adjusts its **weights** to reduce the error (gradient descent). Expensive, done
  once.
- **Inference:** the trained weights are frozen; you feed a prompt and it generates. Cheap-ish, done
  constantly.

Two phases people conflate — be precise about which you mean.

---

## 3. What "parameters" are

- **Parameters = the learned weights** (the numbers in the network). A "7B model" has 7 billion of
  them.
- They encode everything the model "knows" — grammar, facts, patterns — as adjustable connection
  strengths.
- More parameters = more capacity (and more compute/memory), but data quality and training matter as
  much as raw count.

*(This is the classic "what are parameters / how is it trained" interview question.)*

---

## 4. The training stages (modern LLMs)

1. **Pre-training:** learn language from massive unlabeled text (next-token prediction). Produces a
   raw "base" model.
2. **Fine-tuning / instruction-tuning:** teach it to *follow instructions* on curated examples.
3. **RLHF / preference tuning:** align outputs to human preferences (helpful, harmless).

Base model = knowledge; the later stages = behaviour. (Lesson 05 covers fine-tuning/LoRA.)

---

## 5. Key limits to name

- **Hallucination:** it predicts plausible text, not verified truth → can be confidently wrong.
- **Knowledge cutoff:** it only knows its training data → **RAG** (Lesson 04) injects fresh facts.
- **Context window:** a finite amount of text it can attend to at once (Lesson 02).
- **Stochastic:** sampling means the same prompt can give different answers.

---

## 🎤 Say this in the interview

- *"An LLM is fundamentally a next-token predictor — it models the probability of the next token
  given the context and generates autoregressively."*
- *"Parameters are the learned weights; a 7B model has 7 billion of them, tuned during pre-training
  to minimize next-token error, then instruction- and preference-tuned for behaviour."*
- *"Its limits — hallucination, knowledge cutoff, finite context — are why we add RAG and careful
  prompting around it."*

➡️ **Next:** [01 — Tokens & embeddings](./01_Tokens_And_Embeddings.md)
