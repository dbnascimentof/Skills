---
name: sprint-review
description: Generate a biweekly BAR stakeholder sprint-review draft from Jira ENG (Done, 14d), merged PRs on squad owned/shared repos, and pm-brain context. Short leadership tone, ~400 words. Writes knowledge/comms/sprint-review-YYYY-MM-DD.md.
---

# BAR biweekly sprint review

Produce a **draft** stakeholder readout for sprint review. Audience: senior leadership, stakeholders, support — outcomes first, not a Jira export.

## Required context (read before generating)

1. [`knowledge/org/squad-bar.md`](../../knowledge/org/squad-bar.md) — members, GitHub logins, Jira `ENG`, repos (`owned` + `shared` only for PR scan).
2. [`knowledge/product/bar-product-strategy.md`](../../knowledge/product/bar-product-strategy.md) — KPI language for “Product alignment” (only when obvious).
3. [`knowledge/comms/templates/sprint-review-template.md`](../../knowledge/comms/templates/sprint-review-template.md) — output shape.

## When to run

- Biweekly Cursor Automation, or
- Manual: user says “generate sprint review” / “sprint review draft”.

## Time window

- **Rolling 14 days** ending today (UTC or user timezone if stated).
- Record `period_start` and `period_end` in the **Period** line under the title (no YAML frontmatter).

## Step 1 — Jira (Atlassian MCP)

**Cloud:** `779001ad-b70d-41c7-9e8a-f7f87292e66e` (sumupteam) unless session already has `cloudId`.

**Query completed work:**

```jql
project = ENG AND status = Done AND resolved >= -14d ORDER BY resolved DESC
```

Request fields: `summary`, `issuetype`, `status`, `parent`, `labels`, `components`, `resolutiondate`, `description` (for epics/stories only).

**Roll-up rules:**

- Do **not** list raw sub-tasks in headline or theme narrative.
- Group by parent **Epic** (or Story if no epic): walk `parent` until Epic or top-level Story/Task.
- Count completed children only to inform **What we've done so far** bullets — not to quote sub-task titles.
- For each initiative theme, fetch parent **Story/Epic description** (`getJiraIssue`) for acceptance criteria language.
- Demote pure KTLO / Automation Testing **unless** leadership would care (reliability, merchant-visible fix).

**If empty Done set:** widen narrative to material **In Review** parent stories only (max 2 themes) and say “no items closed this period; momentum on …”.

## Step 2 — GitHub (`gh`)

**Repos:** From `squad-bar.md`, repositories with ownership `owned` or `shared` (currently `bank-home-service`, `banking-unified-mobile-bff`, `reward-engine`, `bank-promotions-service`).

**Per repo**, merged PRs in period:

```bash
gh pr list --repo sumup/<repo> --state merged --limit 100 \
  --json number,title,url,author,closedAt,body
```

Filter to `closedAt` within `[period_start, period_end]`.

**Include PR if any:**

1. Author login is in **Members → GitHub** table in `squad-bar.md`, or
2. Title/body/branch references `ENG-\d+` AND that issue belongs to project ENG (verify via Jira if unsure), or
3. PR author’s SumUp email matches roster (when inferable from `gh api`).

**Shared repo (uBFF):** Prefer rules 1–2; do not list tribe-wide PRs without ENG link or squad author.

**Synthesis:** Merge PR themes with Jira themes; one bullet cluster per initiative.

**PR detail for Jira tickets (markets, config):** When a Done ticket claims regional/config work but Jira text is thin, find linked PRs (`gh search prs`, ticket key in title/body/branch). Use `gh pr view <n> --json title,body,files` and scan the diff for country codes or market names.

## Step 3 — Write stakeholder copy

Follow [`templates/sprint-review-template.md`](../../knowledge/comms/templates/sprint-review-template.md): **one `## {Initiative}` per theme** (with `---` after the title line and after each initiative heading), each with exactly these four blocks:

1. **Why it matters** — problem / opportunity for merchants or the business (plain language, no Jira jargon).
2. **What we've done so far** — 2–4 bullets; only **Done** Jira or **merged** squad PRs in period; achievement pattern *When [trigger], we can [capability]*.
3. **So what** — stakeholder impact; honest **not live yet** and rollout/partner notes belong here (not a separate Watchouts section unless cross-cutting).
4. **If we had a demo** — one sentence, merchant/support-visible behavior.

Constraints:

| Rule | Detail |
| ---- | ------ |
| Length | ~400–600 words total (excluding appendix) |
| Ban | “Backbone”, “foundation”, “progress on”, “closer to” without naming a concrete capability |
| Ban | Leading with service/repo names; Kafka/inbox/outbox without gloss |
| Done vs not | Achievements only under *What we've done so far*; gaps under *So what* |

### Geographic / market claims

- Do **not** use “new countries”, “new markets”, or “additional regions” in headlines, themes, or demo lines unless you can **name** them from Jira, Jira comments, or linked PR title/body/diff.
- **Source order:** Jira fields → Jira comments → merged PRs for that ticket on the owning repo → PR file changes.
- If markets are **still unknown** after PR fallback: state the achievement **without** naming markets; add a **PM attention point** (Step 3b)—never guess.

### Step 3b — PM attention points

When stakeholders would ask a follow-up question (“which countries?”) and sources do not answer it, add **`## PM attention points`** after the last initiative (before Appendix).

Each bullet:

- **What’s missing** — fact not in Jira/PR
- **Why it matters** — e.g. sprint review question
- **Suggested action** — ticket, assignee, where to look (Honeycomb, assignee)

Omit the section only if nothing is flagged. Clear attention points before sharing with stakeholders.

**Example:**

- **Markets for ENG-3157:** Jira and in-period PRs do not list which countries were added to the Bank Tile SLO. Name them before sprint review (assignee: see Jira; inspect SLO/Honeycomb change).

**Good vs bad (Bank Plus example):**

- **Bad:** “Completed the backbone for enroll when Bank Plus turns on; verify with Identity.”
- **Good:** “When a merchant subscribes to Bank Plus, we can confirm their subscription from Identity and prepare the data needed for automatic cashback enrollment—without them submitting an enrollment form.”

**Quality gate (must pass before writing file):**

- Can **support** explain what **changed** (not what “moved forward”) in **30 seconds**?
- Does every bullet under *What we've done so far* answer “what can we do now that we could not do 14 days ago?”
- Does every initiative have all **four blocks** (Why / What / So what / Demo)?
- Is it **shorter** than a Jira filter export?
- Any regional claim either **names markets** or has a matching **PM attention point**?

## Step 4 — Output file

Path: `knowledge/comms/sprint-review-YYYY-MM-DD.md` where `YYYY-MM-DD` is **period_end** (today).

- **No YAML frontmatter.** Match template dividers: `# title` → `---` → **Period:**; each `## initiative` → `---` before **Why it matters**; same for **PM attention** and **Appendix**.
- Do **not** publish to Notion, Slack, or Confluence unless user explicitly asks after approval.

## Step 5 — Report back

Tell the user:

- File path
- 2–3 bullets from *What we've done so far* as preview
- Anything ambiguous (ownership, missing GitHub login, empty PR set)

## Maintenance

Update `squad-bar.md` GitHub column when hires leave or usernames change.