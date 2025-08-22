# Day 3 — Promptfoo: Assertion Sets, Weights, Metrics

In this exercise you’ll run **four focused tests** that combine:
- deterministic checks (`contains`, `equals`)
- model-graded checks (`llm-rubric`, `factuality`, `answer-relevance`)
- **assertion sets** (for grouping + “OR” behavior)
- **weights** (relative importance)
- **test-level thresholds** (minimum weighted score to pass)
- **named metrics** (Tone, Accuracy, Performance, etc.)

**Files provided:** `promptfooconfig.yaml` (already set up with the 4 tests below).  
Keep the official docs handy: https://www.promptfoo.dev/docs/intro/

---


## 1) Run

```bash
promptfoo eval
promptfoo view
```
This opens the local results UI.

---

## 2) What to look for in the UI

- The **Metrics** column shows named groups (**Tone**, **Accuracy**, **Performance**, etc.).
- **Tests 1 & 3** include an **assert-set**. In **Test 3**, the set uses `threshold: 0.5` so **either** cost **or** latency can pass the set.
- Tests use **weights** and a **test-level threshold**. The final **pass/fail** is the **weighted score** compared against each test’s `threshold`.

---

## 3) The Four Tests (what each is asserting)

### Test 1 — *quality + performance budget*
- Goal: Tweet is **enthusiastic**, mentions **“AI”**, stays **under 50 words**, and is **cheap & fast**.
- Mixes a model-graded style rule (`llm-rubric`) + a deterministic `contains "AI"` + a Performance set (cost + latency).

### Test 2 — *correctness + brevity*
- Goal: Capital of California is **correct** (**Sacramento**) and the model answers **briefly** (just the city).
- Uses `factuality` + `icontains "Sacramento"` + a brevity rubric to fail correct-but-verbose outputs.

### Test 3 — *correctness + tone + cheap OR fast*
- Goal: A **friendly French** greeting that **includes “Bonjour”**, stays on topic, and meets **either** a **cost** **or** a **latency** target.
- Demonstrates an **assert-set with `threshold: 0.5`** for **OR** behavior (cost **or** latency).

### Test 4 — *exactness + naturalness + performance*
- Goal: EN→FR translation prioritizes **exact match**, allows a **close variant**, reads **naturally**, and respects **performance**.
- Uses **weighted** `equals` (primary) + `similar` (fallback) + a fluency rubric + performance checks.

---

## 4) Interpreting Results

- **Weights**: Heavier weights (e.g., `equals` in Test 4) influence the final score more than lighter checks (e.g., `similar`).
- **Test threshold**: If the **weighted average** of all assertions ≥ test `threshold`, the test **passes**.
- **Assert-set threshold**: Inside a set, `threshold: 0.5` means “at least half the assertions in this set must pass” (i.e., logical **OR** when there are two).

---

## 5) Troubleshooting & Tuning

- Hitting cost/latency failures? Relax targets slightly (e.g., cost `0.0015`, latency `300`) or reduce `max_tokens`.
- Too wordy? Lower `max_tokens` and/or temperature; strengthen brevity rubric text.
- Over-strict translation? Shift weight from `equals` to `similar` or lower the test’s `threshold`.

---

## 6) Stretch Ideas

- Add a **Safety** metric with an `llm-rubric` (e.g., “No PII; no medical/legal advice.”).
- Duplicate the suite with a second provider (e.g., a local endpoint) and compare **Performance** side-by-side.
