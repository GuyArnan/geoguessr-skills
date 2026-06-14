# GeoGuessr Skills Project — Session Restoration Document

**Captured:** 15/06/2026  
**Session:** Laptop session following mobile session on 14/06/2026  
**Purpose:** Full state snapshot for restoring context in a future Claude session with zero loss of fidelity. Import this document at session start and you are fully up to speed.

---

## What This Project Is

Guy uses Claude as a real-time GeoGuessr plonk assistant. Over many sessions (May-June 2026) a skill file accumulated rules, corrections, and reference tables. The problem: container filesystem resets between sessions meant rules written in one session were gone the next. This project establishes a Git-backed, version-controlled skill ecosystem that persists properly.

This session was dedicated to building that ecosystem methodically — not patching reactively.

---

## Repo State

**Repository:** `https://github.com/GuyArnan/geoguessr-skills` (private)  
**Local path:** `C:\Users\guyar\Documents\Programming\Geoguessr\geoguessr-skills`

### Current file structure (as of session end):

```
geoguessr-skills/
  SKILL.md                          ← main GeoGuessr plonk skill (v1.1)
  geoguessr-postmortem/
    SKILL.md                        ← post-mortem analysis skill (v1.0)
    references/
      log.md                        ← longitudinal post-mortem log (empty, ready for entries)
  SESSION_RESTORE.md                ← this file
```

### Commits made this session:
- `feat: add geoguessr-postmortem skill v1.0`
- `feat: add geoguessr skill v1.0` (initial, pre-recovery)
- `feat: geoguessr skill v1.1 - 12 missing rules restored + hybrid architecture` (download pending as of session end — Guy needs to drop the updated SKILL.md and push)

---

## The 12 Recovered Rules

These rules were written into the skill in prior sessions but lost to container resets. All 12 are now in `SKILL.md` v1.1. This is the audit trail:

| # | Rule | Source session | Root miss |
|---|------|---------------|-----------|
| 1 | Result screen convention: black dot = plonk, avatar = actual | 11/06 + 13/06 (written twice, reset twice) | Guy called it out with profanity on 13/06 after repeated failures |
| 2 | Verification gate: name 2-3 alternatives, eliminate each with a specific clue | 29/05 | Almería/Sevilla miss — committed to Sevilla without floating Almería |
| 3 | Reverse deduction pass: walk Tier 3→2→1 after conclusion, check for contradictions | 29/05 | Same Almería session |
| 4 | Business name search rule: if legible business name visible, search it | 29/05 | "Efectos Navales del Sur S.A." would have returned Almería in seconds |
| 5 | Two-stage fetch protocol: continent fetch → country fetch on confirmation | 14/06 (proposed, never committed) | Fetch was continent-only, missing sub-regional depth |
| 6 | Active fetch fallback: web_search immediately on any failed geotips fetch | 14/06 | Flaky geotips fetches were silently failing with no fallback |
| 7 | Poland sandy pine forest bias: central Poland not German border | 11/06 | 468km miss — plonked Brandenburg, actual was central Poland |
| 8 | Featureless park road default: Paris on capitals map | 12/06 | Anchored on Berlin Tiergarten, actual was Paris Bois de Boulogne/Vincennes |
| 9 | Environmental read before sign drill | 25/05 | Suggested Lebanon (not on GeoGuessr) then Albania; actual was Andorra. Yellow chevrons + Pyrenean terrain should have locked region before any sign was read |
| 10 | Borassus palms = broadly Sub-Saharan, not West Africa specifically | 04/06 | Defaulted to West Africa, actual was Uganda |
| 11 | Cuba not on GeoGuessr (explicit callout) | 27/05 | Cuba suggested, caused bad plonk |
| 12 | Kalimantan/Java disambiguation: Indomaret alone doesn't lock to Java | 30/05 | Transmigration means Kalimantan can look identical to Java streetscapes |

---

## Architectural Decisions Made This Session

### Hybrid architecture (fetch + local reference)
**Decision:** Not pure dynamic fetch, not pure local storage. Hybrid.  
**Reasoning:** Guy plays primarily on mobile. Geotips fetches are demonstrably flaky. A skill that depends on a live fetch to function is fragile on mobile. Local tables that load instantly are the resilient baseline; dynamic fetch adds depth when connectivity is good.

### What stays local vs. what gets fetched
**Always local (load at session start, no trigger needed):**
- Deduction tier methodology (Tier 1/2/3) — this is process, not data
- Verification gate — this is process
- Result screen convention — critical, must never be fetch-dependent
- Bollards — appear in almost every Street View frame, high-frequency tiebreaker
- Utility poles — same reasoning as bollards
- Trucks and buses — when visible, often the highest-confidence clue in the frame
- Baltic quick reference — kept as methodology aid (side-by-side diff format is the value, not just the facts)

**Fetched on country confirmation:**
- geotips country-level page for sub-regional placement
- web_search fallback on any fetch failure

**Situational pull (reference files):**
- `references/baltics.md` — when Baltic region confirmed (currently folded into SKILL.md, candidate for extraction later)
- `references/special-cases.md` — Qatar, featureless park road, Kalimantan/Java

**NOT stored locally (fetch-only):**
- Eastern Europe encyclopedic data beyond the quick reference
- Country-specific architectural details
- Anything that benefits from freshness over speed

### File structure direction (v2.0 target)
The current SKILL.md is still monolithic. The agreed v2.0 direction:

```
geoguessr-skills/
  SKILL.md                          ← protocol, deduction tiers, verification gate, plonk rules, fetch protocol, pointers
  references/
    always.md                       ← bollards, utility poles, trucks/buses (loaded every session)
    baltics.md                      ← Baltic disambiguation table (loaded when Baltic confirmed)
    special-cases.md                ← Qatar, park road, Kalimantan, etc. (loaded when pattern detected)
  geoguessr-postmortem/
    SKILL.md
    references/
      log.md
```

**This restructure has not been done yet.** Current SKILL.md is v1.1 monolithic. v2.0 split is the next major task.

### Post-mortem skill design principles
- Fixed typed output format every time — same fields, same order, feeds the log
- Five root cause codes: MC (misread clue), MG (missing clue), OC (overconfidence), NS (no signal), KG (knowledge gap)
- NS is not a face-save code — only use it when the round genuinely gave nothing actionable
- Pattern check is mandatory: read log.md before making any skill update recommendation
- Band-aid gate: a proposed update must address a recurring pattern OR clearly generalize beyond the triggering round. Single-miss reactive rules are flagged as band-aid risk
- Post-mortem skill NEVER writes to GeoGuessr SKILL.md directly. All proposed changes go through improve-system Skill Review after Guy's explicit confirmation
- Log entry appended after every post-mortem, including NS rounds — complete records are what make the log useful

---

## Open Threads (Where We Stopped)

1. **SKILL.md v1.1 not yet pushed.** Guy needs to download the updated `geoguessr-SKILL.md` from the outputs and replace `SKILL.md` in the repo root, then `git add . && git commit -m "feat: geoguessr skill v1.1" && git push origin main`.

2. **Git tag for v1.0 snapshot not yet created.** After pushing v1.1, tag the prior commit:
   ```powershell
   git tag -a v1.0 -m "Initial release snapshot - pre-restructure"
   git push origin v1.0
   ```

3. **SKILL.md v2.0 restructure not started.** The monolithic SKILL.md needs to be split into core + `references/always.md` + `references/baltics.md` + `references/special-cases.md`. Bollards, poles, and trucks/buses move to `always.md`. Baltic table moves to `baltics.md`. Special cases move to `special-cases.md`. Protocol, verification gate, deduction tiers, plonk rules stay in SKILL.md core.

4. **Memory update pending.** Memory currently points to `/mnt/skills/user/geoguessr/SKILL.md` as the skill location. This needs updating to reference the GitHub raw URL once the repo structure is stable: `https://raw.githubusercontent.com/GuyArnan/geoguessr-skills/main/SKILL.md`.

5. **Post-mortem skill not yet installed to `/mnt/skills/user/`.** Currently lives in `/home/claude/geoguessr-postmortem/` (container, resets between sessions) and in the repo. Needs to be installed as a proper skill so it triggers automatically.

6. **geoguessr-postmortem skill description not yet in available_skills.** The triggering description needs to be added to the Claude skills system so it fires on result screenshots without Guy invoking it manually.

---

## Design Philosophy (Do Not Drift From This)

**1000x not 10x.** The difference: 10x is a better-formatted version of what we were already doing. 1000x is a system that compounds — every post-mortem feeds a log, the log enables pattern analysis, pattern analysis distinguishes real gaps from noise, real gaps produce clean skill additions, clean additions improve future sessions. Each individual piece is modest; the compounding is the value.

**No band-aids.** A band-aid is: one miss triggers one rule addition without checking whether it's a pattern. The pattern check step in the post-mortem skill is specifically designed to prevent this. Before any skill update is proposed, the log is read. If this is the first occurrence, the recommendation is "band-aid risk, confirm before writing." If it's the third occurrence, it's a clean addition.

**Honest self-assessment.** The NS (no signal) root cause code is the face-save escape hatch. The post-mortem rules explicitly call this out: NS is only correct when the round genuinely gave nothing actionable. If I had the clues and failed, that's MC, MG, or OC and must be labeled as such.

**Mobile-first resilience.** Guy plays on mobile 100% of the time. Every architectural decision should be evaluated against: does this degrade gracefully on a flaky mobile connection? Local tables that load instantly beat dynamic fetches that might fail. Fetch is additive depth, not the load-bearing structure.

**No direct writes from post-mortem to GeoGuessr skill.** Ever. The post-mortem skill proposes, improve-system Skill Review confirms and writes. This keeps the GeoGuessr skill's edit history clean and intentional.

---

## How to Resume This Session

1. Read this document in full.
2. Check repo state: `Get-ChildItem -Recurse` in `geoguessr-skills\`.
3. Pick up at the first open thread above.
4. If Guy shares a GeoGuessr screenshot before the restructure is complete, use the current SKILL.md from the repo and apply the 12 recovered rules manually — they're in this document if needed.
