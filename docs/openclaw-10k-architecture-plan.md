# OpenClaw $10,000/month Architecture Plan

**Prepared by:** Claude (outside architect role), 2026-06-09
**Scope:** Local / report-only. No posting, buying, trading, outreach, ads, lead capture, account changes, deployments, or secrets handling is recommended anywhere in this plan.

**Access disclaimer (read first):** This plan was produced from a cloud session that has access only to the `imocoservices.github.io` repository. The path `C:\OpenClaw` and all twelve referenced memory/control files (FACTS.md, RULES.md, NEXT.md, LEARNINGS.md, SUGGESTIONS.md, MISTAKES.md, the goal ladder JSON, research-autopilot config and charter, revenue-supervisor charter, agent-lanes JSON, projects index) were **not accessible and were not read**. Nothing in Section 1 is a verified observation about OpenClaw's actual state. Where the plan refers to OpenClaw's current behavior, it is marked **UNKNOWN** or **HYPOTHESIS**. The lower-agent scan jobs in Section 2 are designed to be run later, on the machine where `C:\OpenClaw` lives, to replace these hypotheses with evidence.

---

## 1. Current System Diagnosis

### What was verifiable in this session
- None of OpenClaw's memory or control files were reachable. **Current state: UNKNOWN.**
- Joey's actual revenue, time availability, skills inventory, and project statuses: **UNKNOWN.**
- Whether lower-cost agents are currently runnable on Joey's machine: **UNKNOWN.**

### What the file inventory implies about the architecture (inferred from file names only, unverified)
The directory structure suggests OpenClaw already has the right *shape*: persistent memory split by type (facts, rules, learnings, mistakes, suggestions, next actions), a goal ladder tied to the $10k/month target, a research autopilot with an autonomy charter, a revenue-supervisor charter for the main agent, defined agent lanes, and a projects index. That is the skeleton of a learning organization.

### Likely failure modes to test for (HYPOTHESES — each maps to a scan job in Section 2)
These are the standard ways systems with this shape stall. None is asserted as true of OpenClaw; each must be confirmed or refuted by a scanner before being acted on.

1. **Stale memory treated as current.** Facts and learnings written months ago keep steering plans even after circumstances change, because nothing expires or re-verifies them.
2. **Repeated advice loops.** SUGGESTIONS.md and daily reports re-propose the same handful of moves in new wording, because nothing deduplicates advice against what was already proposed and rejected.
3. **Learnings without evidence.** LEARNINGS.md entries that state conclusions ("X doesn't work") without recording what was tried, for how long, and what was measured — making them impossible to trust or overturn.
4. **Joey as the bottleneck.** Plans that quietly assume Joey will do research, judgment calls, and execution steps that agents could do locally, so throughput is capped by Joey's hours.
5. **Delegation that doesn't close the loop.** Tasks get assigned to lanes but results never flow back into memory as structured outcomes, so the system delegates without learning from delegation.
6. **Narrow opportunity research.** The research autopilot keeps mining the same sources and categories, producing variations on ideas already considered instead of genuinely new option space.
7. **Goal ladder without rung-level evidence.** The 10k ladder defines stages, but rungs lack pass/fail criteria and dated evidence, so "progress" is a feeling rather than a measurement.

### What blocks progress (structural, regardless of file contents)
- A goal ("$10k/month") without a falsifiable pipeline (N candidates → gated validation → kill/scale decisions) degenerates into idea churn.
- A memory system without timestamps, evidence links, and deduplication degenerates into a diary that repeats itself.
- A delegation system without a verifier role degenerates into either rubber-stamping or Joey re-checking everything.

---

## 2. Lower-Agent Scan Plan

These are exact job specs OpenClaw should assign to its cheapest available agents (local models or low-cost API tier). All jobs are read-only against local files. Each produces a structured report file under `C:\OpenClaw\data\scans\<job-name>\<date>.md` so results are diffable over time. Main/Opus never accepts a scanner's conclusion without checking its cited line references.

### Job 1 — Stale Memory Auditor
- **Agent name:** `scan-stale-memory`
- **Files to scan:** `memory/FACTS.md`, `memory/LEARNINGS.md`, `memory/RULES.md`
- **Question to answer:** Which entries have no date, are older than 60 days without re-verification, or contradict a newer entry?
- **Output expected:** Table: entry excerpt (≤120 chars), file + line, last-touched date (or "undated"), status (`fresh` / `stale` / `contradicted` / `unverifiable`), and the contradicting entry's location if any.
- **Verification by Main/Opus:** Spot-check 5 random rows against the source lines; reject the report if any cited line doesn't contain the quoted text. Confirm "contradicted" pairs actually conflict rather than covering different scopes.

### Job 2 — Repeated Advice Detector
- **Agent name:** `scan-advice-dedup`
- **Files to scan:** `memory/SUGGESTIONS.md`, `memory/NEXT.md`, plus any daily-report archive directory if one exists
- **Question to answer:** Which suggestions are semantic duplicates (same action, reworded), how many times has each cluster been proposed, and was it ever acted on or explicitly rejected?
- **Output expected:** Clusters of duplicate advice with counts, first-seen and last-seen dates, and disposition (`acted` / `rejected` / `never addressed` / `unknown`). Flag the top 5 most-repeated never-addressed items.
- **Verification by Main/Opus:** Re-read the top 5 clusters in full; confirm they are genuinely the same action and not distinct ideas that share keywords.

### Job 3 — Evidence Gap Finder
- **Agent name:** `scan-evidence-gaps`
- **Files to scan:** `memory/LEARNINGS.md`, `memory/MISTAKES.md`, `data/goal-incentives/10000-month-goal-ladder.json`
- **Question to answer:** Which learnings, mistakes, and goal-ladder rungs state a conclusion without recording what was observed, when, and how it was measured?
- **Output expected:** List of entries classified `evidenced` (cites a concrete event/measurement/date) vs `asserted` (conclusion only), with the missing element named per asserted entry (no date / no measurement / no described trial).
- **Verification by Main/Opus:** Check that nothing labeled `evidenced` merely cites another memory entry as its evidence (circular citation = `asserted`).

### Job 4 — Joey-Dependency Mapper
- **Agent name:** `scan-joey-bottlenecks`
- **Files to scan:** `memory/NEXT.md`, `data/main-agent/revenue-supervisor-charter.md`, `data/research-autopilot/autonomy-charter.md`, `data/agent-lanes/agent-lanes.json`
- **Question to answer:** Which pending or recurring tasks require Joey personally, and for each: is that because of (a) a real approval boundary, (b) a missing agent capability, or (c) habit/default?
- **Output expected:** Table of Joey-dependent tasks with classification a/b/c and, for every (c), the specific agent lane that could absorb it under existing charter rules without expanding permissions.
- **Verification by Main/Opus:** For each proposed reassignment, confirm against RULES.md and the charters that no approval boundary is crossed. Anything ambiguous stays with Joey.

### Job 5 — Delegation Loop Tracer
- **Agent name:** `scan-delegation-loops`
- **Files to scan:** `data/agent-lanes/agent-lanes.json`, `data/projects/projects-index.json`, `memory/NEXT.md`
- **Question to answer:** For tasks delegated to lanes, does a completed result ever come back and get written into memory? Where does the loop break — assignment, execution, reporting, or memory capture?
- **Output expected:** Per-lane summary: tasks assigned (count), results recorded (count), memory entries traceable to lane output (count), and the named break-point for each lane with a ratio below 1:1.
- **Verification by Main/Opus:** Trace one full task per lane end-to-end manually before accepting the break-point diagnosis.

### Job 6 — Research Diversity Auditor
- **Agent name:** `scan-research-diversity`
- **Files to scan:** `data/research-autopilot/config.json`, any research output archive under `data/research-autopilot/`, `memory/SUGGESTIONS.md`
- **Question to answer:** Across past research outputs, how concentrated are the categories, sources, and business models explored? What fraction of outputs are near-duplicates of earlier outputs?
- **Output expected:** Category/source frequency table, a duplication estimate (% of outputs substantially overlapping a prior one), and a list of structurally unexplored axes (customer type, pricing model, delivery mechanism) — *axes only, no specific business ideas*, so the constraint against app suggestions is preserved.
- **Verification by Main/Opus:** Check the frequency table sums against the raw file count; spot-check 3 claimed duplicates.

### Job 7 — Goal Ladder Integrity Checker
- **Agent name:** `scan-goal-ladder`
- **Files to scan:** `data/goal-incentives/10000-month-goal-ladder.json`
- **Question to answer:** Does every rung have (a) a numeric target, (b) a pass/fail criterion checkable from local data, (c) a current status with a date, and (d) a defined next action? Which rungs are missing which?
- **Output expected:** Rung-by-rung compliance matrix plus a list of rungs whose status cannot be determined from any local file (**mark UNKNOWN, do not guess**).
- **Verification by Main/Opus:** Validate the JSON parses and the matrix covers every rung; confirm no scanner-invented statuses appear.

### Job 8 — Rules/Charter Conflict Scanner
- **Agent name:** `scan-rule-conflicts`
- **Files to scan:** `memory/RULES.md`, `data/research-autopilot/autonomy-charter.md`, `data/main-agent/revenue-supervisor-charter.md`
- **Question to answer:** Are there rules that contradict each other or the charters? Are there charter permissions with no corresponding rule, or rules so vague an agent could not decide compliance?
- **Output expected:** List of conflicts (rule A vs rule B, quoted, with locations), orphan permissions, and vague rules with a one-line proposed clarification each (proposal only — Joey approves wording).
- **Verification by Main/Opus:** Read every flagged pair in full context; vagueness flags are accepted only if the scanner supplies a concrete scenario the rule fails to decide.

---

## 3. Learning Loop Design

The goal: every report, answer, capture, and failure measurably changes future behavior, and nothing is repeated without acknowledging it was said before.

**1. One evidence ledger, append-only.** A single local file (e.g., `data/ledger/evidence.jsonl`) where every entry has: date, source (which agent/report/event), claim, evidence type (`measured` / `observed` / `inferred` / `external-unverified`), and expiry/review date. Memory files stop being where conclusions are born; they become summaries that must cite ledger IDs. A learning without a ledger ID is an opinion.

**2. Advice fingerprinting.** Before any suggestion is written to SUGGESTIONS.md or a daily report, a cheap agent normalizes it (verb + object + mechanism) and checks it against prior fingerprints. Duplicates aren't silently dropped — they're written as `REPEAT (3rd time, first proposed 2026-04-02, disposition: never addressed)`. Repetition becomes a visible signal that something is blocked, rather than noise.

**3. Dispositions are mandatory.** Every suggestion eventually gets exactly one of: `adopted`, `rejected (reason)`, `expired (superseded by ledger #N)`, or `blocked on Joey`. A weekly cheap-agent job lists everything still undispositioned after 14 days. This is the single mechanism that kills stale-advice loops.

**4. Failure autopsy format.** When an idea or experiment dies, MISTAKES.md gets a structured entry: what was the hypothesis, what gate did it fail (see Section 5), what evidence killed it, and — crucially — the *reusable rule* extracted (e.g., "ideas requiring asset class X failed gate G1 twice; require pre-evidence before next G1 attempt"). Cause-of-death codes let the research autopilot avoid generating new candidates that would die identically.

**5. Weekly distillation, monthly pruning.** Weekly: a mid-tier agent compresses the week's ledger entries into ≤10 durable learnings, each citing ledger IDs, and updates NEXT.md from dispositions rather than from fresh brainstorming. Monthly: stale entries (past expiry, never re-verified) are moved to an `memory/archive/` folder — never deleted, but no longer fed into planning context. Planning prompts only ever include non-archived memory.

**6. Claude/Opus answers are inputs, not oracles.** External-model answers enter the ledger as `inferred` or `external-unverified` until something local confirms them. This prevents the failure mode where a confident LLM answer gets promoted to FACTS.md and steers months of work.

---

## 4. Delegation Model

| Role | Who | Responsibilities | Explicitly NOT allowed |
|---|---|---|---|
| **Main / Supervisor (Opus-tier)** | Highest-capability model, invoked sparingly | Owns the goal ladder; sets weekly priorities; designs experiments and gates; resolves conflicts between reports; writes the weekly distillation | Doing scans itself; generating bulk research; bypassing the verifier |
| **Verifier** | Mid-tier model, separate context from Main | Checks every scanner report against cited sources before Main consumes it; checks Main's plans against RULES.md and charters; flags evidence-free claims | Originating plans or suggestions (keeps it adversarial) |
| **Cheap/local workers** | Lowest-cost agents | Run Section 2 scan jobs; fingerprint advice; maintain dispositions; file-format chores; produce diff reports | Drawing strategic conclusions; writing to FACTS/LEARNINGS directly (they propose, verifier admits) |
| **Research workers** | Cheap-to-mid tier | Generate opportunity candidates inside the Section 5 framework; fill in evaluation matrices from locally available material; explicitly label every unknown as UNKNOWN | Any external action (posting, signups, outreach, purchases); inventing market data |
| **Memory workers** | Cheap tier, scheduled | Append ledger entries; apply expiry dates; run weekly dedup; execute monthly archive moves (move, never delete) | Editing entry content; deleting anything |
| **Joey** | Human | Approves: rule/charter changes, anything crossing the local/report-only boundary, money, accounts, deployments. Reviews: one weekly digest. Supplies: real-world facts no agent can know | Being the default executor of tasks classified (c) in Job 4; reading raw agent output (digests only) |

**Flow:** scanners → verifier → Main → plan → dispositions → memory workers → next cycle. Joey touches the loop at exactly two points: the weekly digest and explicit approval requests. Anything an agent wants from Joey must arrive as a yes/no question with the evidence attached, never as "look into this."

---

## 5. $10,000/month Strategy Without Existing App Examples

No specific business ideas are proposed here (per constraint). This is the machine that finds and tests them.

### 5.1 Target arithmetic (structure, not prediction)
$10k/month must come from some combination of: price × customers, or rate × hours, or margin × volume. Before any candidate enters the pipeline, OpenClaw must be able to write its arithmetic in one line (e.g., "200 buyers × $50/mo" or "4 clients × $2,500/mo") and state which factor is the hard one. Candidates whose arithmetic requires numbers with no precedent anywhere are deprioritized automatically.

### 5.2 Candidate criteria (scored 0–2 each, locally, before any validation effort)
1. **Reachable demand evidence exists** — there is publicly observable proof that someone pays for this category today (not that they *might*). If unknown → score 0, not a guess.
2. **Joey-independence** — ≥70% of build/operate steps can be done by agents locally; Joey's role fits inside his stated available hours (**currently UNKNOWN — must be captured as a fact with a date before scoring begins**).
3. **Time-to-first-evidence ≤ 14 days** — a kill/continue signal is obtainable within two weeks of starting validation.
4. **Capital at risk ≤ a Joey-set cap** (UNKNOWN until Joey sets it; default assumption: near-zero).
5. **Compounding** — month 6 is structurally easier than month 1 (assets, reputation, or data accumulate).
6. **Boundary-compatible** — validation gates can be passed without violating the local/report-only charter, or with only narrow, specific Joey approvals.

Candidates scoring <8/12 are archived with their scores (so research workers stop regenerating them).

### 5.3 Discovery process (diverse by construction)
Research workers generate candidates by systematically crossing axes rather than free-associating: **(who pays)** individuals / small businesses / mid-size firms / other builders × **(what's sold)** time / product / access / data / outcomes × **(why now)** new capability / new regulation / new platform / cost collapse × **(moat)** speed / niche depth / accumulated data / relationship. Each research run must fill cells *not* covered in prior runs (Job 6's diversity report enforces this). Every candidate ships with its evidence list and an explicit UNKNOWNS section.

### 5.4 Validation gates (each gate is a kill checkpoint with a written verdict)
- **G0 — Desk check (agents only, ≤1 day):** arithmetic line + criteria score + prior-art check against the idea graveyard. Kill silently if <8/12 or graveyard-matched.
- **G1 — Evidence dossier (agents only, ≤3 days):** compile publicly observable proof of paid demand, named competitor pricing, and the smallest sellable unit. Every claim labeled `measured/observed/inferred/unknown`. Kill if the dossier's load-bearing claims are all `inferred`/`unknown`.
- **G2 — Joey review (≤30 min of Joey's time):** Joey reads the dossier and answers three fixed questions: Would I do this for a year? Is the Joey-role realistic for me? What's my risk cap here? Kill or proceed.
- **G3 — Buildable-locally proof (agents, ≤7 days):** produce the smallest internal artifact demonstrating the capability exists (prototype, sample deliverable, dry-run output) without any external action. Kill if the artifact can't be produced in 7 agent-days.
- **G4 — External validation design (report only):** agents write the exact, minimal external test (what would be shown to whom, expected signal, abort criteria) and stop. **Executing G4 always requires Joey's explicit approval — it crosses the report-only boundary.**

### 5.5 Decision rules
- Pipeline WIP limits: ≤10 candidates past G0, ≤3 past G2, ≤1 past G3 at a time. Focus is enforced by the limits, not willpower.
- A kill at any gate writes a graveyard entry with cause-of-death code; research workers must check the graveyard during G0.
- No candidate may re-enter the pipeline unless a *new, dated* piece of evidence addresses its cause of death.
- Revenue claims only ever come from Joey-confirmed numbers. Agents never estimate "current revenue"; it is a fact Joey supplies or it is UNKNOWN.
- The portfolio target is one validated path scaled deliberately — not five half-validated paths. The ladder rungs (first dollar → first $100 month → first $1k month → $10k month) each require dated, Joey-confirmed evidence to mark passed.

---

## 6. First 7 Days (all local / report-only)

- **Day 1:** Run Job 1 (Stale Memory Auditor) and Job 7 (Goal Ladder Integrity Checker). Verifier checks both. Output: stale-memory report + ladder compliance matrix.
- **Day 2:** Run Job 2 (Repeated Advice Detector) and Job 3 (Evidence Gap Finder). Output: duplicate-advice clusters with dispositions; evidenced-vs-asserted classification.
- **Day 3:** Run Job 4 (Joey-Dependency Mapper) and Job 5 (Delegation Loop Tracer). Output: Joey-task classification table; per-lane loop-break report.
- **Day 4:** Run Job 6 (Research Diversity Auditor) and Job 8 (Rules/Charter Conflict Scanner). Output: diversity/duplication report; conflict list with proposed clarifications (proposals only).
- **Day 5:** Main/Opus synthesis day. Consume all eight verified reports; replace every Section 1 HYPOTHESIS with `confirmed` / `refuted` / `still unknown`; draft the evidence-ledger format and advice-fingerprint convention as files for Joey's approval.
- **Day 6:** Build the pipeline scaffolding as local files only: `data/pipeline/candidates/`, `data/pipeline/graveyard/`, gate templates G0–G4, criteria scorecard template. No candidates yet.
- **Day 7:** Produce the first Weekly Digest for Joey (≤1 page): what the scans found, the 3 highest-impact confirmed problems, the proposed rule clarifications, and the open questions only Joey can answer (his hours, risk cap, hard exclusions). **Joey's first review happens here.**

---

## 7. First 30 Days

**Phase 1 — Audit & repair (Days 1–7).** As above.
*Milestones:* 8/8 scan reports verified; every Section 1 hypothesis dispositioned; weekly digest #1 delivered; Joey's hours/risk-cap/exclusions captured as dated facts (or explicitly recorded as UNKNOWN-pending-Joey).

**Phase 2 — Loop installation (Days 8–14).** Stand up the evidence ledger, advice fingerprinting, disposition tracking, and the weekly distillation job. Backfill: migrate the top 20 still-relevant learnings into ledger format; archive what Job 1 marked stale (move, don't delete). Re-run Job 2 at day 14 to confirm duplicate-advice rate is falling.
*Milestones:* ledger live with ≥20 backfilled entries; 100% of new suggestions fingerprinted; undispositioned-suggestion count trending down; weekly digest #2.

**Phase 3 — Discovery at full diversity (Days 15–21).** Research workers run the Section 5.3 axis-crossing process. Target: 20–30 G0 candidates spanning ≥8 distinct axis-cells, each with arithmetic line, scorecard, and UNKNOWNS section. Graveyard seeded from past dead ideas (via MISTAKES.md, with cause-of-death codes) so nothing is regenerated.
*Milestones:* ≥20 scored candidates; ≥8 axis-cells covered; 100% of candidates carry explicit UNKNOWN labels where evidence is missing; weekly digest #3 presents the top 10.

**Phase 4 — First gated validations (Days 22–30).** Top candidates (by score) proceed: G1 dossiers for up to 10; Joey runs G2 on the survivors (≤30 min each); at most 3 proceed; at most 1 enters G3 by day 30. All G4 designs are written but **none executed** — they queue for Joey's explicit approval.
*Milestones:* ≥5 completed G1 dossiers with verifier sign-off; ≥1 candidate through G2; G3 artifact started; weekly digest #4 includes the first kill-rate and cycle-time numbers (the system's own performance metrics, which become the baseline for month 2).

Note: 30 days produces a *working selection machine and its first validated-locally candidate*, not $10k/month. Any plan promising revenue inside 30 days from a standing start would be invented confidence.

---

## 8. What OpenClaw Should Stop Doing

1. Writing suggestions without checking whether the same suggestion was already made (pending Job 2 confirming this happens — **HYPOTHESIS**).
2. Recording conclusions in LEARNINGS.md without evidence, dates, or what-was-tried (pending Job 3).
3. Treating undated facts as current indefinitely (pending Job 1).
4. Defaulting tasks to Joey when no rule requires it (pending Job 4).
5. Generating opportunity research as free association instead of against a coverage map (pending Job 6).
6. Letting daily reports restate state without proposing a disposition for anything.
7. Marking goal-ladder progress without dated, checkable evidence per rung (pending Job 7).
8. Estimating or implying revenue/market numbers that no local file or Joey statement supports — UNKNOWN must stay UNKNOWN.
9. Keeping dead ideas only as prose in MISTAKES.md instead of as structured graveyard entries that block regeneration.
10. Invoking the most expensive model for work a scanner-tier agent can do (pending confirmation of current usage patterns — **UNKNOWN**).

## 9. What OpenClaw Should Start Doing

1. **Weekly Digest to Joey** (≤1 page, fixed format: findings / decisions needed / pipeline state / system health).
2. **The eight recurring scans** (Section 2): Jobs 1–3 weekly; Jobs 4–8 monthly.
3. **Evidence ledger discipline:** every claim entering memory carries source, type, and expiry.
4. **Advice fingerprinting + mandatory dispositions** with a 14-day undispositioned alert.
5. **Failure autopsies with cause-of-death codes** feeding a graveyard the research workers must check.
6. **Axis-coverage research:** every research run reports which cells of the who/what/why-now/moat grid it covered and which remain empty.
7. **Verifier-before-Main:** no scanner report or research dossier reaches planning context unverified.
8. **Joey-as-approver, not executor:** every request to Joey is a yes/no question with evidence attached.
9. **Pipeline WIP limits** (10 / 3 / 1) enforced in the pipeline files themselves.
10. **Monthly self-metrics:** duplicate-advice rate, undispositioned count, candidate kill-rate per gate, G1 cycle time, Joey-hours consumed — the system measures its own learning loop.

## 10. Risks And Guardrails

| Risk | Failure mode | Guardrail |
|---|---|---|
| **Process theater** | The system perfects scans and ledgers while no candidate ever reaches G3 | Phase deadlines in Section 7; weekly digest must report pipeline counts; an empty pipeline at day 21 is itself a red flag escalated to Joey |
| **Hallucinated evidence** | A research worker invents demand "proof" to pass G1 | Verifier checks every load-bearing claim's source; claims typed `inferred`/`unknown` cannot carry a gate pass |
| **Boundary creep** | An agent "helpfully" performs an external action (posting, signup, outreach) during validation | Hard rule: G4 execution requires Joey's explicit, per-candidate approval; charters list prohibited verbs; Job 8 re-scans rules monthly |
| **Memory corruption** | Aggressive cleanup deletes context that was actually load-bearing | Memory workers may only *move* to archive, never delete; archives reviewed before Phase 2 ends |
| **Joey overload returns** | Digests and approval requests balloon until Joey is the bottleneck again | Digest hard-capped at 1 page; approval requests must be yes/no with evidence; Job 4 re-run monthly to catch regression |
| **Goalpost drift** | Activity metrics (scans run, candidates generated) get treated as progress toward $10k | Only the goal ladder's dated, Joey-confirmed rungs count as progress; everything else is system health |
| **Stale plan, fresh repetition** | This very document becomes next quarter's repeated advice | This plan gets a ledger entry with a 30-day review date; at day 30 it is re-verified or revised, not re-recommended |
| **Single-candidate tunnel vision** | The first G2 survivor absorbs all attention regardless of evidence quality | Kill criteria written at G2 time, before attachment forms; verifier owns the kill checklist |

---

## Final Answers

- **The single highest-leverage change:** Install the evidence ledger + advice fingerprinting + mandatory dispositions (Section 3, items 1–3). Every other improvement — better delegation, better research, better gating — silently fails if the system cannot tell what it already knows, what it already said, and what was already decided.
- **The first lower-agent task to run:** Job 1, `scan-stale-memory`, against FACTS.md, LEARNINGS.md, and RULES.md — it is cheap, read-only, and its output calibrates how much of current memory every other job can trust.
- **The first thing Joey should review:** The Day 7 Weekly Digest — specifically the three open questions only he can answer (available hours per week, capital risk cap, hard exclusions), because candidate scoring is blocked until those become dated facts instead of UNKNOWNs.
- **Confidence:** **Medium** on the architecture (the loop, gate, and delegation designs are standard and robust to whatever the scans find) — but **Low** on every statement about OpenClaw's current state, because `C:\OpenClaw` was not accessible from this session and no memory file was actually read. The Section 2 scans exist precisely to convert that Low to High.
