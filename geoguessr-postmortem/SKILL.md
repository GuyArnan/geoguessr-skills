---
name: geoguessr-postmortem
description: Post-mortem analysis skill for Guy's GeoGuessr rounds. Trigger immediately whenever Guy shares a result screenshot showing a score and map, or uses phrases like "post mortem", "postmortem", "debrief", "how did I do", "why was I wrong", "what did we miss", or shares a GeoGuessr result image after completing a round. Also triggers when Guy references a prior round and asks what went wrong. Always run this skill for result screenshots — do not just describe the image without running the full structured analysis. This skill produces a typed analytical record and feeds a longitudinal performance log.
---

# GeoGuessr Post-Mortem Skill

Produces a structured post-mortem for a completed GeoGuessr round. Each output is a typed record in a consistent format. Records accumulate in `references/log.md` and power pattern analysis over time.

---

## Inputs

- A GeoGuessr result screenshot (required): shows score, distance off, map with actual location dot and Guy's plonk pin
- Street View screenshots from the round (optional): may appear earlier in conversation context
- Any discussion of the round that happened before the result was shared

If no Street View is in context, note it explicitly in the analysis and work from what's available.

---

## Step 1 — Round Analysis

Produce the post-mortem record in this exact structure. Do not skip fields. Do not reorder fields.

```
DATE: [DD/MM/YY]
MAP TYPE: [World / Capitals / Country-specific / Unknown]
SCORE: [pts]
DISTANCE: [km] off
PLONKED: [country + region description]
ACTUAL: [country + region description]
```

### Clues Available

List every identifiable clue from the Street View(s). Be exhaustive. Include clues that were correctly read, incorrectly read, and missed entirely. Format as a flat list:

- [clue]: [what it shows]

### Deduction Chain Review

Reconstruct what the correct deduction chain should have been, step by step, using the GeoGuessr skill's Tier 1 → Tier 2 → Tier 3 order. Show where my actual chain diverged from the correct one.

**Correct chain:** [step by step]
**Where I diverged:** [specific point of failure]

### Root Cause

Assign exactly one primary root cause from this taxonomy:

| Code | Name | Definition |
|------|------|------------|
| `MC` | Misread clue | Clue was visible and identifiable; I drew the wrong conclusion from it |
| `MG` | Missing clue | Clue was present but I failed to identify or weight it |
| `OC` | Overconfidence | I anchored on an early read and stopped looking for contradicting evidence |
| `NS` | No signal | Round genuinely gave insufficient information; miss was close to unavoidable |
| `KG` | Knowledge gap | Correct deduction required knowledge not in the GeoGuessr skill |

**ROOT CAUSE: [code] — [name]**
[One crisp sentence explaining why this code applies.]

### Honest Self-Assessment

This field requires genuine self-attribution. No hedging, no blaming the round unless it actually deserves it.

- Was the information available to make the correct call? [Yes / No / Partially]
- If yes: what specifically failed in my reasoning?
- If no: what would have been needed?
- Would a stronger model likely have caught this? [Yes / No / Uncertain]

### Severity

```
SEVERITY: [1-5]
```

Scale:
- 1: Minor miss, within same region, correct country or adjacent
- 2: Wrong sub-region, correct broad area (e.g., right part of Europe)
- 3: Wrong country, correct continent, clues were ambiguous
- 4: Wrong country, correct continent, clues were available
- 5: Wrong continent, or large miss with clear available clues

---

## Step 2 — Pattern Check

Read `references/log.md`.

Check the last 20 entries (or all entries if fewer than 20 exist) for:

1. **Recurrence**: Has this root cause code appeared 3+ times in the last 20 rounds?
2. **Regional pattern**: Has this country or region appeared in prior misses?
3. **Clue pattern**: Has this specific clue type (e.g., utility poles, bollards, road lines) been the point of failure before?

Report findings concisely:

**Pattern found:** [Yes / No]
[If yes: describe the pattern in one sentence. How many prior instances? What country/clue/root cause?]

---

## Step 3 — Skill Update Gate

Based on the round analysis and pattern check, make a recommendation. Use exactly one of these:

### Option A: No action
Root cause was `NS` (no signal), or the miss was a one-off with no recurrence, or the correct rule already exists in the GeoGuessr skill and I simply failed to apply it.

```
RECOMMENDATION: NO ACTION
REASON: [one sentence]
```

### Option B: Band-aid risk
A rule could be added but it would be narrow, reactive, and address only this specific miss without evidence of a pattern. Risk of cluttering the skill with one-country exceptions.

```
RECOMMENDATION: BAND-AID RISK
PROPOSED TEXT: [exact proposed addition to GeoGuessr skill]
CONCERN: [one sentence on why this may be premature]
DECISION: Confirm before writing.
```

### Option C: Clean addition
Root cause is `KG` or `MG`, and either: (a) a pattern exists showing this gap has caused multiple misses, or (b) the missing knowledge clearly belongs in the skill's structure and will generalize beyond this round.

```
RECOMMENDATION: CLEAN ADDITION
PROPOSED TEXT: [exact proposed addition — section, table row, or rule — formatted for direct insertion into GeoGuessr skill]
FITS INTO: [which section of the GeoGuessr skill this belongs in]
PATTERN BASIS: [how many prior misses this addresses, or why it generalizes]
```

**If Option B or C:** End with the handoff line:

> **Handoff to improve-system Skill Review:** [one sentence describing the change.]
> Confirm and I'll write it in.

Do not write the change to the GeoGuessr skill directly. Ever.

---

## Step 4 — Log Entry

After completing the analysis, append a new entry to `references/log.md` in this exact format:

```
| [DD/MM/YY] | [map type] | [score] | [distance]km | [PLONKED] | [ACTUAL] | [ROOT CAUSE CODE] | [SEVERITY] | [update recommendation: NONE / BAND-AID / CLEAN] | [one-line summary] |
```

If `references/log.md` does not exist yet, create it with this header first:

```
# GeoGuessr Post-Mortem Log

| Date | Map | Score | Distance | Plonked | Actual | Root Cause | Severity | Update | Summary |
|------|-----|-------|----------|---------|--------|------------|----------|--------|---------|
```

---

## Rules

- Never auto-write to the GeoGuessr skill. All proposed changes go through improve-system Skill Review after Guy's explicit confirmation.
- Honest self-assessment is not optional. If I had the clues and blew it, say so clearly.
- `NS` (no signal) is the correct call only when the round genuinely gave nothing actionable. It is not a face-saving code.
- The log entry must always be appended, even for `NS` rounds with no skill update. The log's value depends on complete records.
- If no Street View is in context, the Clues Available and Deduction Chain Review sections should reflect only what can be inferred from the result map and any conversation. Note the limitation.
- Band-aid risk is not a rejection. It's a flag. Guy may choose to add it anyway -- the call is his.
- Each post-mortem is fully independent. Do not carry assumptions from the prior round.
