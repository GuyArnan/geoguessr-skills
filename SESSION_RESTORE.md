# GeoGuessr Skills — Operating Manual & Session Restore

**Version:** 1.1  
**Captured:** 15/06/2026  
**Purpose:** This is not a summary. It is a complete operating manual for the GeoGuessr skills system. A future Claude instance reading this document should be able to run the entire system cold — no chat history mining, no reconstruction, no guessing.

---

## What This System Is

Guy uses Claude as a real-time GeoGuessr plonk assistant. Over many sessions (May–June 2026) a skill file accumulated rules, corrections, and reference tables, but container resets between sessions wiped improvements. This system solves that with a Git-backed skill ecosystem and a structured post-mortem loop that compounds improvements over time rather than losing them.

**The core loop:**
1. Play a round → Claude analyzes using the GeoGuessr skill
2. Share result screenshot → post-mortem skill produces a typed record
3. Post-mortem identifies a gap → pattern check against the log
4. If it's a real pattern → clean addition proposed → confirmed → written to repo
5. Repo updated → ZIP packaged → uploaded via Settings → skill updated
6. Next session starts with the improved skill

Every step feeds the next. The compounding is the value.

---

## Repo

**URL:** `https://github.com/GuyArnan/geoguessr-skills` (public)  
**Local path:** `C:\Users\guyar\Documents\Programming\Geoguessr\geoguessr-skills`  
**Pages URL:** `https://guyarnan.github.io/geoguessr-skills/` (active, .nojekyll committed)

### Structure

```
geoguessr-skills/
  SKILL.md                          ← GeoGuessr plonk skill (v1.1) — SOURCE OF TRUTH
  SESSION_RESTORE.md                ← this file
  _config.yml                       ← empty, disables Jekyll
  .nojekyll                         ← forces GitHub Pages to serve files as-is
  geoguessr-postmortem/
    SKILL.md                        ← post-mortem skill (v1.0)
    references/
      log.md                        ← longitudinal post-mortem log
```

---

## Skill Installation — How It Actually Works

Skills in Claude.ai are installed as ZIP files via **Settings → Customize → Skills**. The installed skills live at `/mnt/skills/user/` which is read-only during sessions — Claude can read them but nothing can write to them mid-session.

**This means:** every skill update requires a re-upload cycle. The repo is the development environment. The ZIP upload is the deployment.

### Update cycle (run from laptop)

**To update the GeoGuessr skill:**
```powershell
cd "C:\Users\guyar\Documents\Programming\Geoguessr\geoguessr-skills"
# Make changes to SKILL.md
git add .
git commit -m "feat/fix: [description]"
git push origin main
# Package just the skill file
Compress-Archive -Path SKILL.md -DestinationPath geoguessr-skill.zip
```
Then: **Settings → Customize → Skills → GeoGuessr skill → update → upload geoguessr-skill.zip**

**To update the post-mortem skill:**
```powershell
Compress-Archive -Path geoguessr-postmortem\* -DestinationPath geoguessr-postmortem.zip
```
Then: **Settings → Customize → Skills → GeoGuessr Post-Mortem → update → upload geoguessr-postmortem.zip**

**Important:** The log.md inside geoguessr-postmortem/references/ accumulates post-mortem records. When repackaging the post-mortem skill, always include the current log.md so records aren't lost.

### Fetch fallback (why GitHub fetch doesn't auto-work)

`raw.githubusercontent.com` is blocked at the network level. `github.io` is reachable but `web_fetch` policy blocks autonomous fetches from memory instructions — only user-provided URLs in-conversation can be fetched. The installed skill at `/mnt/skills/user/` is the only reliable auto-load path. Keep it up to date via the ZIP upload cycle above.

---

## GeoGuessr Skill — Design Principles

**Architecture: hybrid (local core + dynamic fetch)**  
Guy plays on mobile 100% of the time. Fetch reliability is variable. The skill must work perfectly with no network calls. Fetch is additive depth, not load-bearing structure.

**What stays local (always available, no trigger needed):**
- Session protocol and result screen convention
- Deduction tier methodology (Tier 1/2/3) — this is process, not data
- Verification gate — process, must never be fetch-dependent
- Bollards — appear in almost every Street View frame
- Utility poles — same reasoning
- Trucks and buses — when visible, often highest-confidence clue
- Baltic quick reference — methodology aid, side-by-side diff format is the value

**What gets fetched on country confirmation:**
- geotips.net country-level page for sub-regional depth
- web_search fallback on any failed fetch — never silently proceed

**What is NOT stored locally:**
- Encyclopedic country data beyond the quick references
- Anything that benefits from freshness over speed

**v2.0 target structure (not yet implemented):**
```
SKILL.md                ← protocol, tiers, verification gate, plonk rules, fetch protocol
references/
  always.md             ← bollards, utility poles, trucks/buses
  baltics.md            ← Baltic disambiguation table
  special-cases.md      ← Qatar, park road, Kalimantan, etc.
```
Current SKILL.md is v1.1 monolithic. The split is the next major task.

---

## The 12 Recovered Rules (now in SKILL.md v1.1)

These were written into the skill in prior sessions but lost to container resets. All 12 are now in the installed skill. This is the audit trail so they are never lost again.

| # | Rule | Source miss |
|---|------|-------------|
| 1 | Result screen: black dot = plonk, avatar = actual | Called out with profanity 13/06 after being wrong repeatedly |
| 2 | Verification gate: name 2-3 alternatives, eliminate each with a specific clue | Almería/Sevilla — committed to Sevilla without floating Almería |
| 3 | Reverse deduction pass: walk Tier 3→2→1 after conclusion | Same Almería session |
| 4 | Business name search: if legible name visible, search it | "Efectos Navales del Sur S.A." would have returned Almería instantly |
| 5 | Two-stage fetch: continent → country on confirmation | Fetch was continent-only, missing sub-regional depth |
| 6 | Active fetch fallback: web_search immediately on failed geotips fetch | Silent failures with no fallback |
| 7 | Poland sandy pine forest: bias central Poland not German border | 468km miss — plonked Brandenburg, actual was central Poland |
| 8 | Featureless park road: default Paris on capitals map | Anchored on Berlin Tiergarten, actual was Paris Bois de Boulogne |
| 9 | Environmental read before sign drill | Suggested Lebanon (not on GeoGuessr) then Albania; actual was Andorra |
| 10 | Borassus palms = broadly Sub-Saharan, not West Africa | Defaulted to West Africa, actual was Uganda |
| 11 | Cuba not on GeoGuessr | Cuba suggested, caused bad plonk |
| 12 | Kalimantan/Java: Indomaret alone doesn't lock to Java | Transmigration makes Kalimantan look identical to Java |

---

## Post-Mortem Skill — How It Works

Triggered automatically when Guy shares a result screenshot or says "post mortem", "how did I do", "debrief", etc.

**Output is a typed record every time. Fixed fields, fixed order:**
- DATE, MAP TYPE, SCORE, DISTANCE, PLONKED, ACTUAL
- Clues Available (exhaustive flat list)
- Deduction Chain Review (correct chain vs. where I diverged)
- Root Cause (exactly one code)
- Honest Self-Assessment (no hedging)
- Severity (1-5)
- Pattern Check (reads log.md, checks last 20 entries)
- Skill Update Recommendation
- Log Entry (always appended, even NS rounds)

**Root cause taxonomy:**
- `MC` — Misread clue: clue visible, wrong conclusion drawn
- `MG` — Missing clue: clue present, not identified or weighted
- `OC` — Overconfidence: anchored early, stopped looking for contradictions
- `NS` — No signal: genuinely insufficient information (not a face-save code)
- `KG` — Knowledge gap: correct deduction required knowledge not in the skill

**NS is not a face-save.** If I had the clues and failed, it's MC, MG, or OC.

**Band-aid gate criteria:**
- Band-aid risk: proposed rule addresses fewer than two prior misses, no structural relationship to deduction order, would add a one-country exception to a general table
- Clean addition: addresses a recurring pattern in the log, fits into existing framework structure, generalizes beyond the triggering round

**Hard rule:** Post-mortem skill NEVER writes to the GeoGuessr SKILL.md directly. All proposed changes go through improve-system Skill Review after Guy's explicit confirmation, then through the repo → ZIP → upload cycle.

---

## Open Threads

1. **SKILL.md v2.0 restructure** — split monolithic file into core + references/always.md + references/baltics.md + references/special-cases.md. Bollards, poles, trucks/buses move to always.md. Baltic table to baltics.md. Special cases to special-cases.md. Core keeps protocol, tiers, verification gate, plonk rules.

2. **Post-mortem skill ZIP not yet uploaded** — geoguessr-postmortem skill needs to be packaged and installed via Settings → Skills so it triggers automatically on result screenshots.

3. **GeoGuessr skill ZIP needs re-upload** — v1.1 is in the repo but the installed skill at /mnt/skills/user/ may still be the old version. Package and re-upload to sync.

4. **Git tag v1.0** — tag the initial release commit if not already done:
   ```powershell
   git tag -a v1.0 -m "Initial release snapshot"
   git push origin v1.0
   ```

---

## Design Philosophy

**1000x not 10x.** The difference: 10x is a better-formatted version of what we were doing. 1000x is a system that compounds — every post-mortem feeds the log, the log enables pattern analysis, pattern analysis distinguishes real gaps from noise, real gaps produce clean additions, clean additions improve future sessions.

**No band-aids.** The pattern check step exists specifically to prevent reactive one-miss rule additions. Before any skill update is proposed, the log is read. First occurrence = band-aid risk. Third occurrence = clean addition.

**Mobile-first resilience.** Every architectural decision evaluated against: does this degrade gracefully on a flaky mobile connection? Local tables beat dynamic fetches that might fail.

**No direct writes from post-mortem to GeoGuessr skill.** Ever. Post-mortem proposes, improve-system confirms and writes, repo is updated, ZIP is uploaded. That's the full cycle.

**Honest self-assessment is non-negotiable.** NS is only correct when the round genuinely gave nothing actionable.

---

## How to Resume

1. Read this document in full.
2. Check repo: `Get-ChildItem -Recurse` in `geoguessr-skills\`.
3. Check installed skills: Settings → Customize → Skills — verify both GeoGuessr and GeoGuessr Post-Mortem are installed and toggled on.
4. Pick up at open thread #1 (v2.0 restructure) or #3 (ZIP re-upload) depending on what Guy wants to tackle.
5. If Guy shares a GeoGuessr screenshot before the re-upload is done, note that the installed skill may be the old version and apply the 12 recovered rules from this document manually.
