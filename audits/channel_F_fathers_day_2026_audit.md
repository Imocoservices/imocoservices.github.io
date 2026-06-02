# Channel F — Father's Day 2026 Audit
**Date:** June 2, 2026
**Auditor:** Claude Code
**Branch:** claude/channel-f-fathers-day-audit-YWxan

---

## Verdict: NEEDS FIX

13 findings across configs and scripts. No uploads may proceed until operational blockers (items 1–3) are explicitly resolved by Joey. The Father's Day batch is nearly ready — 7 of 20 scripts need targeted edits before render.

---

## Findings — Ordered by Severity

---

### SEVERITY 1 — Operational Blockers (resolve before any upload attempt)

---

#### Finding 1 — OAuth Expired, Channel Disabled
**File:** `config/youtube.json`
**Risk:** Any attempt to re-enable upload will fail silently or throw at auth step.

`youtube.json` correctly shows:
- `"status": "expired"` for channel F's OAuth token
- `"upload_enabled": false` on the channel
- `"global_upload_lock": true`

These locks are correctly in place. **Do not remove them** until full re-auth is completed by Joey. The `global_upload_lock` should be the last thing lifted, after channel-level re-auth is confirmed.

**Action required:** Joey must complete OAuth re-authorization before any upload flag is flipped to `true`. This audit does not touch those flags.

---

#### Finding 2 — Stale Ready Item Will Post Before Father's Day Batch
**File:** `script_queue/F/ready/F_20260502_01.txt`
**Risk:** When uploads resume, the system drains `ready/` before rendering from `pending/`. `F_20260502_01` will post first — out of season, out of theme, and with a generic closer ("Have a great day, everyone.") instead of a Father's Day sign-off.

Additionally, `channel_F_template.json` sets `"sort_order": "filename_ascending"` but **does not specify whether the scope is per-folder or across both `ready/` and `pending/` combined**. This ambiguity is a secondary risk. At ASCII sort order, `F_2` < `F_f`, so even if folders were merged, `F_20260502_01` would still lead the queue.

**Recommended action:** Move `F_20260502_01.txt` from `script_queue/F/ready/` to `script_queue/F/processed/` before enabling uploads. Do not delete — keep for audit trail.

**No change applied here.** Awaiting Joey's explicit approval to relocate the file.

---

#### Finding 3 — `fathers_day_2026_plan.md` Acknowledges Stale Item but Provides No Resolution Step
**File:** `channels/channel_F_dad_jokes/fathers_day_2026_plan.md`

The plan notes: *"Stale ready item F_20260502_01 exists — must be processed or moved before new batch uploads"* but gives no assigned owner, no target date, and no explicit action. This leaves the risk open indefinitely.

**Recommended edit to `fathers_day_2026_plan.md` — Queue Notes section:**

Replace:
```
- Stale ready item F_20260502_01 exists — must be processed or moved before new batch uploads
```
With:
```
- Stale ready item F_20260502_01 exists in script_queue/F/ready/ and WILL post before the
  Father's Day batch when uploads resume. Action: move to script_queue/F/processed/ before
  flipping upload_enabled to true. Owner: Joey. Required before: June 14, 2026.
```

---

### SEVERITY 2 — Config Inconsistencies

---

#### Finding 4 — Rating Field Conflicts with Channel Identity
**File:** `config/channel_F_template.json`, line: `"rating": "G"`

The channel is named **"PG-13 Dad Jokes"** in `config/channels.json`. The template sets `"rating": "G"`. These conflict. A generation pipeline reading the template for filtering or guardrail enforcement will apply the wrong rating tier. G-rated filtering is more restrictive than PG-13 and could reject acceptable material; a PG-13 label on a G-config may cause content confusion.

**Recommended fix:**
```json
"rating": "PG-13",
"rating_notes": "Clean, warm humor. No crude language, bathroom humor, or adult themes. PG-13 refers to mild groan-worthy wordplay suitable for all ages in a family room."
```

---

#### Finding 5 — Prompt Template Missing Critical Guardrails
**File:** `config/prompt_templates.json`

The `style_notes` field reads: *"Keep it clean. Use relatable dad props when possible."*

This is far too vague to reliably constrain a generator. Missing:

| Missing rule | Risk if absent |
|---|---|
| No engagement bait ("comment below", "like and subscribe", "follow for more") | Scripts like 014 already exhibit this failure |
| TTS-friendly text only — spell out numbers, no symbols (&, *, %, #, @) | Scripts like 016 already exhibit this failure |
| Max ~35–40 words per line; total script target ~50 words | Scripts like 008's SETUP already exceed safe timing |
| GROAN must be a spoken phrase, not a stage direction or symbol | Script 002 already exhibits asterisk-groan failure |
| Preferred prop list | Grill, lawn, remote, socks, coffee mug, tie, toolbox, thermostat, nap |
| Forbidden topic list | Bathroom humor, mean spouse jokes, lazy stereotypes, politics |

**Recommended replacement for `style_notes` and adjacent fields:**
```json
"style_notes": "Keep it clean and warm. Preferred dad-life props: grill, lawn, remote, socks, coffee mug, tie, toolbox, thermostat, nap. Avoid bathroom humor, mean spouse jokes, lazy stereotypes, and politics.",
"tts_rules": "All text must be TTS-friendly. Spell out numbers (three, not 3). Spell out 'and' (not &). No asterisks, percent signs, or other symbols. The GROAN field must be a short spoken phrase — never a stage direction like *groan* or *sighs*.",
"format_rules": "Each line must be speakable in under 5 seconds. Target 35–50 total words per script. SETUP: 1–2 sentences. PUNCHLINE: 1 sentence. GROAN: 2–6 words. CLOSER: 1 warm sentence. No engagement bait in any field ('comment below', 'like and subscribe', 'follow for more')."
```

---

#### Finding 6 — `sort_order` Scope Is Undefined in Template
**File:** `config/channel_F_template.json`

The `"sort_order": "filename_ascending"` key is inside the `queue` block alongside both `pending_folder` and `ready_folder`. It is unclear whether sort order applies:
- Per-folder independently, or
- Across all folders in processing priority order.

If the runner sorts items globally across folders, and `ready/` is not given explicit priority, the alphabetical-first item in either folder wins — which could interleave stale and new content.

**Recommended fix:** Add explicit folder priority:
```json
"queue": {
  "pending_folder": "script_queue/F/pending",
  "ready_folder": "script_queue/F/ready",
  "processed_folder": "script_queue/F/processed",
  "sort_order": "filename_ascending",
  "folder_priority": ["ready", "pending"]
}
```
This makes the execution contract explicit: drain `ready/` first in ascending filename order, then pull from `pending/`.

---

### SEVERITY 3 — Script Violations (must fix before render)

---

#### Finding 7 — Engagement Bait in CLOSER
**File:** `script_queue/F/pending/F_fathers_day_014.txt`
**Line:** `[CLOSER]`

Current:
```
Comment below your dad's most repeated joke. Happy Father's Day!
```

"Comment below" is explicit engagement bait. Violates style guardrails. YouTube Shorts policies also discourage overt call-to-action comment prompts in automated content.

**Replacement:**
```
[CLOSER]
Happy Father's Day to every dad with a signature joke and a captive audience.
```

---

#### Finding 8 — SETUP Exceeds Safe TTS Timing for a 30-Second Short
**File:** `script_queue/F/pending/F_fathers_day_008.txt`
**Line:** `[SETUP]`

Current SETUP word count: **37 words**. At standard TTS pace (~140–160 wpm) that's approximately 14 seconds for setup alone, leaving only ~16 seconds for the remaining three lines plus any music/caption time. With `scripts_per_video: 2`, two of these back-to-back would run ~28+ seconds for setup lines alone — well over the 30-second target total.

**Full rewrite:**
```
[SETUP]
Dad watched one plumbing video online and declared himself fully certified.
[PUNCHLINE]
We now own three buckets, have no water pressure, and a deep respect for licensed plumbers.
[GROAN]
Close enough, Dad.
[CLOSER]
Happy Father's Day to every dad who Googles first and calls a plumber second.
```

---

#### Finding 9 — TTS Will Read `*groan*` Literally
**File:** `script_queue/F/pending/F_fathers_day_002.txt`
**Line:** `[GROAN]`

Current:
```
*groan*
```

TTS will vocalize this as "asterisk groan asterisk" — a hard break in the joke's rhythm.

**Fix:**
```
[GROAN]
Ohhh, Dad.
```

---

#### Finding 10 — TTS Hazard: Symbols and Raw Digits in SETUP
**File:** `script_queue/F/pending/F_fathers_day_016.txt`
**Line:** `[SETUP]`

Current:
```
Dad's BBQ sauce has 3 secret ingredients & 2 proprietary techniques.
```

The `&` symbol and raw digits `3`, `2` will cause inconsistent TTS rendering depending on engine. Standard TTS may read `&` as "ampersand" or skip it; digits may be read as "three" or silently skipped.

**Fix:**
```
[SETUP]
Dad's BBQ sauce has three secret ingredients and two proprietary techniques.
```

---

### SEVERITY 4 — Quality Issues (recommend fix before render)

---

#### Finding 11 — Duplicate Joke Mechanic: Temperature / Dad Won't Pay for Comfort
**Files:** `F_fathers_day_003.txt` and `F_fathers_day_004.txt`

Both scripts run the same mechanic: *dad refuses to spend money on household temperature comfort and justifies it with a pithy dismissal.*

- 003: Thermostat — "I'm not heating the whole neighborhood."
- 004: Air conditioning — "Electricity costs money, and sweating builds character."

These are funny individually but weaken each other as paired content in the same batch. Videos 002 and 003 (scripts 003+004) would render back-to-back and the audience would notice the repeat theme.

**Recommendation:** Keep 003 (stronger, better punchline). Rewrite 004 with a different mechanic.

**Replacement for F_fathers_day_004.txt:**
```
[SETUP]
Dad insists on arriving forty-five minutes early to everything.
[PUNCHLINE]
He calls it "being on time." Everyone else calls it standing in a parking lot.
[GROAN]
Punctuality is his love language.
[CLOSER]
Happy Father's Day to every dad who invented early as a lifestyle.
```

---

#### Finding 12 — Duplicate Joke Mechanic: Dad Is Baffled by Technology
**Files:** `F_fathers_day_012.txt` and `F_fathers_day_013.txt`

Both scripts run the same mechanic: *dad is comically incompetent with everyday technology, misunderstands something basic, and the narrator is gently amused.*

- 012: TV was unplugged; calls tech support for 20 minutes.
- 013: Asks to "do the Google"; mistakes voicemail setup for satellite hacking.

013 is the stronger and more original of the two. 012 reads closer to a standard "boomer tech fail" format.

**Recommendation:** Keep 013. Rewrite 012 with a different mechanic.

**Replacement for F_fathers_day_012.txt:**
```
[SETUP]
Dad asked me to wrap his Father's Day gift so he "wouldn't feel awkward" receiving it.
[PUNCHLINE]
I wrapped a spatula. He acted genuinely surprised and said it was perfect.
[GROAN]
Method acting.
[CLOSER]
Happy Father's Day to every dad who makes any gift feel like the best one.
```

---

#### Finding 13 — Flat Punchline with No Subversion or Wordplay
**File:** `script_queue/F/pending/F_fathers_day_018.txt`

Current:
```
[SETUP]
What is every dad's favorite type of music?
[PUNCHLINE]
Classic rock, obviously.
[GROAN]
Of course.
[CLOSER]
Happy Father's Day to all the classic rock dads out there.
```

The punchline is the only plausible answer to the setup. There is no surprise, wordplay, or subversion. The audience will have the answer before the PUNCHLINE card appears. "Obviously" as a tag word confirms the joke isn't trying to land — it's just stating a stereotype. This script would earn silence, not a groan.

**Full rewrite:**
```
[SETUP]
Why does every dad insist on giving directions instead of using GPS?
[PUNCHLINE]
Because he knows a shortcut that saves six minutes and adds thirty-five.
[GROAN]
We will get there eventually.
[CLOSER]
Happy Father's Day to every dad with a scenic alternate route.
```

---

## Scripts Requiring Rewrites — Summary

| Script | Issue | Action |
|---|---|---|
| F_fathers_day_002.txt | `*groan*` TTS hazard | Fix GROAN line |
| F_fathers_day_004.txt | Duplicate mechanic (thermostat) | Full rewrite |
| F_fathers_day_008.txt | SETUP too long (37 words) | Tighten SETUP and PUNCHLINE |
| F_fathers_day_012.txt | Duplicate mechanic (dad/tech) | Full rewrite |
| F_fathers_day_013.txt | Keep as-is (stronger of the pair) | No change |
| F_fathers_day_014.txt | Engagement bait in CLOSER | Fix CLOSER line |
| F_fathers_day_016.txt | TTS symbols and digits in SETUP | Fix SETUP line |
| F_fathers_day_018.txt | Flat punchline, no surprise | Full rewrite |

---

## Scripts That Pass — No Changes Needed

| Script | Theme | Notes |
|---|---|---|
| F_fathers_day_001.txt | Grill | Strong, prop-grounded, good timing |
| F_fathers_day_003.txt | Thermostat | Sharp punchline, keep |
| F_fathers_day_005.txt | Nap/recliner | Warm, relatable, good groan |
| F_fathers_day_006.txt | Socks and sandals | Quotable punchline |
| F_fathers_day_007.txt | Coffee mug | Self-aware, funny, tight |
| F_fathers_day_009.txt | Tie vault | Good Father's Day meta-joke |
| F_fathers_day_010.txt | Toolbox/duct tape | Strong closer |
| F_fathers_day_011.txt | Stakes/steaks pun | Classic dad pun, earned groan |
| F_fathers_day_013.txt | "Do the Google" | Strongest tech joke, keep |
| F_fathers_day_015.txt | Vintage humor | Good self-referential bit |
| F_fathers_day_017.txt | Garden cardio | Warm, visual, good rhythm |
| F_fathers_day_019.txt | Nap-cinema | Strong callback structure |
| F_fathers_day_020.txt | "We'll see" | Groan with genuine charm |

---

## Stale Item Review: F_20260502_01

**File:** `script_queue/F/ready/F_20260502_01.txt`

The joke itself (hardware store / fastener aisle) is clean and functional but the CLOSER — "Have a great day, everyone." — has no seasonal tie and no Channel F voice. It reads like a placeholder. If this item is ever re-queued for posting, the CLOSER should be updated to match current channel style before it renders.

**Stale item disposition recommendation:** Move to `processed/`. If Joey wants to repurpose it later, update the CLOSER before re-queuing.

---

## Config Fixes Required Before Next Generation Run

1. `config/channel_F_template.json` — change `"rating": "G"` to `"rating": "PG-13"`, add `rating_notes`
2. `config/prompt_templates.json` — expand `style_notes`; add `tts_rules` and `format_rules` fields
3. `config/channel_F_template.json` — add `"folder_priority": ["ready", "pending"]` to queue block
4. `channels/channel_F_dad_jokes/fathers_day_2026_plan.md` — add explicit resolution action and owner for stale item

---

## Operational Checklist Before Upload May Resume

- [ ] Joey completes OAuth re-auth for Channel F
- [ ] `youtube.json` — `oauth.channel_F.status` updated from `"expired"` to `"valid"` by Joey only
- [ ] `F_20260502_01.txt` moved from `ready/` to `processed/` — confirmed by Joey
- [ ] All 8 script fixes applied and reviewed
- [ ] `channel_F_template.json` rating corrected to `"PG-13"`
- [ ] `prompt_templates.json` guardrails expanded
- [ ] Joey explicitly approves enabling `upload_enabled: true` on Channel F
- [ ] `global_upload_lock` lifted by Joey only

**Upload enable is NOT approved by this audit. All upload-related flags remain as-is until Joey explicitly approves.**

---

*Audit complete — 13 findings, 8 scripts need edits, 4 config fixes recommended, 3 operational blockers require Joey's action.*
