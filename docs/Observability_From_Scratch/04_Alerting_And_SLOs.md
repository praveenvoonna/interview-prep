# 04 — Alerting & SLOs

## 🧠 The One Idea

**An SLO is a promise about reliability ("99.9% of requests succeed"), and good alerts fire when
you're about to *break that promise* — not on every blip.** The shift is from alerting on causes
("CPU high") to alerting on **symptoms users feel** (errors, latency), measured against a target.

The one-liner: **"Define SLIs, set SLOs, spend an error budget, and alert on burn rate — symptom-based
alerts on what users experience, not noisy cause-based ones."**

---

## 1. SLI / SLO / SLA

- **SLI (Indicator):** a measured number — e.g. % of successful requests, p99 latency.
- **SLO (Objective):** your **target** for an SLI — e.g. 99.9% success over 30 days.
- **SLA (Agreement):** a **contract** with consequences (refunds) if the SLO is missed — the legal
  cousin.

You design around **SLIs/SLOs**; the SLA is the business wrapper.

---

## 2. Error budgets

- 99.9% success = **0.1% allowed to fail** = your **error budget**.
- Budget remaining → ship features fast. Budget burning → freeze and stabilize.
- This turns reliability into a **shared, quantified trade-off** between dev velocity and stability —
  no more arguing by gut feel.

---

## 3. Alert on burn rate, not blips

- **Burn rate** = how fast you're consuming the error budget.
- Alert when burn rate is high enough to **exhaust the budget** before the window ends (e.g. fast-
  and slow-burn multi-window alerts).
- This avoids both **alert fatigue** (paging on every spike) and **missed slow leaks**.

---

## 4. Alert fatigue & good-alert hygiene

- **Every alert must be actionable** — if there's nothing to do, it shouldn't page.
- **Symptom over cause:** page on "users seeing errors", not "one pod restarted".
- Severity tiers: **page** (wake someone) vs **ticket** (handle in hours).
- Prune noisy alerts ruthlessly — a noisy pager gets ignored, and *that* causes outages.

---

## 5. RED & USE (what to measure)

- **RED** (request-driven services): **R**ate, **E**rrors, **D**uration — the golden signals for an
  API.
- **USE** (resources): **U**tilization, **S**aturation, **E**rrors — for CPUs, disks, queues.
- Google's **four golden signals:** latency, traffic, errors, saturation.
- Start every service's dashboard/alerts from these — they cover most incidents.

---

## 🎤 Say this in the interview

- *"I define SLIs like success rate and p99, set SLOs as targets, and the gap is an error budget that
  balances feature velocity against stability."*
- *"I alert on **burn rate** against the budget and on **symptoms** users feel — rate, errors,
  duration — not on every CPU spike, to avoid alert fatigue."*
- *"Every alert must be actionable with a clear severity; I build dashboards from RED for services and
  USE for resources."*

➡️ **Next:** [05 — Dashboards & debugging a real incident](./05_Dashboards_And_Debugging.md)
