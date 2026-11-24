---
name: draft-experiment
description: >-
  Designs a rigorously specified in-product A/B (holdout) experiment after a
  grill-me interview. Produces build spec, XYZ hypothesis, primary experiment,
  secondary metrics, and guardrails. Use when drafting experiment specs,
  holdout tests, growth A/B tests, or when the user invokes /draft-experiment.
  Does not use landing-page pretotypes or lean pretotype methods.
---

# Draft experiment

Design an **in-product A/B or holdout** experiment for **$ARGUMENTS**. No pretotypes (no fake doors, landing pages, concierge MVPs, or waitlists unless the user explicitly asks for something outside this skill).

## Prerequisites (mandatory order)

1. **Grill-me first.** Read and follow the **grill-me** skill (`Productivity/grill-me/SKILL.md` under the user Skills folder) before any experiment draft. Do not output a “quick draft” before the interview.
2. **Build spec + approval.** After grill-me, present the build spec format from grill-me and get an explicit **yes** (or edits) from the user.
3. **Then draft** the experiment sections below.

If the user says `/draft-experiment` with `/grill-me`, run grill-me in the same session; still no experiment content until the spec is approved.

If the user attaches product or strategy files, read them before grill-me so you do not re-ask settled facts.

## Experiment quality rules

Apply these instead of lean-startup pretotype framing:

- **Test and control:** Every experiment needs a clear **control** (holdout, no change) and **test** (the change being evaluated); state split (e.g. 50/50) and assignment unit (user, merchant, account).
- **Intent-to-treat (ITT):** Primary analysis uses randomized assignment, not only users assigned to test who saw the experience.
- **One primary metric:** A single decision metric with a pre-stated success rule (e.g. statistically significant lift, or MDE agreed with data).
- **Guardrails:** Metrics that must not regress (volume, revenue, complaints, funnel steps).
- **Your own data:** Baseline and power from recent cohorts in the product, not borrowed industry benchmarks alone.
- **Behavior over surveys:** Prefer shipped-exposure outcomes; qualitative research is optional and never replaces the A/B readout.

Supported methods: **A/B and holdout tests only** (no switchback, multivariate, or rollout sections unless the user asks outside this skill).

## Deliverable structure

Produce markdown in chat with these sections (tables encouraged).

### 1. Context (short)

One paragraph: problem, lever, and link to any strategy doc sections the user cares about.

### 2. XYZ hypothesis

Use **control** and **test** (not population / treatment). Two linked statements:

**Control (benchmark):**  
**"At least X% of Y will do Z"**

- **X%:** Set from **control** baseline when known; if unknown, state that X will be set in a pre-period (control cohort, no test experience).
- **Y:** Specific cohort (market, platform, eligibility, timing).
- **Z:** Observable behavior within a defined window.

**Test (what the A/B evaluates):**  
One line stating what the **test** arm does and the expected change vs **control** (e.g. higher 30d activation rate in test than control, measured ITT).

### 3. Primary experiment

| Dimension | Content |
|-----------|---------|
| Hypothesis under test | Causal claim the A/B answers |
| Method | Product surface, trigger, persistence; **control** vs **test** arms |
| Audience | Inclusion / exclusion |
| Primary metric | Definition, window, analysis (ITT) |
| Success threshold | How you call win / loss / inconclusive |
| Runtime / sample | High-level unless data team supplied power calc |

### 4. Secondary metrics

Short table or bullets: diagnostic metrics (CTR, time-to-event, funnel steps). Label each as **diagnostic** so they do not hijack the primary decision.

### 5. Guardrails

Metrics and thresholds that trigger pause or rollback.

### 6. Launch prerequisites (optional, brief)

Only non-pretotype gates: metric definition sign-off, targeting/eligibility verification, legal/comms alignment. Keep to a short checklist.

## Output

- Default: deliver in **chat**.
- If the artifact is substantial, **offer** to save a markdown file; save only when the user accepts (path: user’s choice or repo `experiments/` if they name one).

## Out of scope for this skill

- Pretotype experiment libraries (landing page, video, pre-order, concierge).
- Non–A/B designs (switchback, bandits, qualitative-only plans as the main deliverable).

## Related skills

- **grill-me** — required before drafting.
- **brainstorm-experiments-new** — use when the user wants pretotypes or net-new product validation outside the product.