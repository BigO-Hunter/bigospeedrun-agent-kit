# UPDATE v2 — notes for the coordinator

AI-authored (darkhipo, MultiAgency swarm), 2026-11-03. No external action taken: nothing pushed, posted, or commented. All edits are in the working tree of `/tmp/bigospeedrun_agent_kit` on `main`, **uncommitted**, so `git diff` is the whole change. Sources are the fetched evidence in `/tmp/sssp_recon`, `/tmp/sssp_prep`, `/tmp/vdw_recon`, `/tmp/recon_tail_boards.md`, plus read-only reads of `bigospeedrun/verifier` (#134, #135, #136, commit `b0c848f5`, `scripts/submissions.py` at that SHA) and `bigospeedrun/site` (#9, PR #12).

## Files

| File | Before | After | Δ |
|---|---|---|---|
| `ACTIVATION_PLAYBOOK.md` | 10,475 B | 12,270 B | +1,795 |
| `AGENT_PROOF_KIT.md` | 9,786 B | 12,284 B | +2,498 |
| `README.md` | 2,306 B | 3,096 B | +790 |

Both documents are under the 12 KB cap. The cap forced real condensation of the snaky material (see "What was compressed"), not just additions — flagged below so you can veto any specific cut.

## ACTIVATION_PLAYBOOK.md

**Header + intro.** Restructured to introduce *two* case studies instead of one: snaky (routeless → LV row in <8h) and directed-SSSP (defect report → route opened with a `(D,T)` gate citing the report *in the gate code*, 43 minutes from filing to the route-opening commit). Source: verifier #134 (filed 15:24:55Z), commit `b0c848f5` (16:07:47Z, message line "verifier #134: the old code called `Fraction()` on 'D ; T' and stopped the selection pass"; `scripts/submissions.py:55`).

**New §2 — "THE MOST EXPENSIVE LESSON: ask the gate's semantics before building"** (placed second, before the snaky case study, because it is the most expensive lesson). Records that our `n²;n³` foothold (`(2,0,3,0,0)`) was drafted and filing-ready — `parse_sssp` executed, four neighbours verified refused, target generated, staging path traced, package skeleton and checklist written — and that the opened gate refuses exactly that class. Carries the maintainer's verbatim ruling (T must be below n·D; clause 2 of `Bound` already gives `C(n+1)(m+1)`; "`n^2 ; n^3` is such a row, and the form refuses it"), the enforcing guard (`submissions.py:99-100`: `if t >= (a + 1, b, Fraction(0)): raise ValueError(SSSP_FREE)`, with `SSSP_FREE`'s own text at `:57`), and the arithmetic that places `n²;n³` *on* the boundary (T = n³ = n²·n = n·D). Also records the recon's own wrong claim verbatim ("each IS a record under the verifier's density-time rule (no listed row denser)") because the quote is the evidence that the error was a reasoning error, not a typo.
- **Corollary added, as requested:** a "record" is defined by the GATE's semantics, not by the absence of denser listed rows — the gate also includes what the statement gives free (clause 2) and, below `n log^{3/4} n`, the Vals AI line `Tchd`.

**§3 (was §2–§4) — snaky case study.** All original content preserved: the #106 three questions, homework/plan, the 17:02 tested patch with the `19/2` integer guard, the maintainer's four answers at 17:21Z, the 18:53Z statement landing with sha256, the root-module trap, byte-for-byte vendoring, pins, axiom discipline, licence/attribution, the proof arc, and delivery through the thread with the byte-strict form. The **"doubly validated"** note is new and explicit: 50 minutes of asking on snaky bought the spec, 43 minutes on SSSP bought the route, and both answers were things recon could not infer from the tree.

**New §4 — directed-SSSP case study.** Board shape (universal costed-program claim, `(D,T)` normal form, single-α live page), the 2026-11-03 timeline (10:56 statement → 14:32:51 smoke #127 → 15:24:55 #134 → 16:07:47 `b0c848f5` → 16:23:25 answers → 18:14:43 our hash-verified follow-up → 19:41:31 closed), the two defects (the dormant `ValueError` in `qualifies_for_run`, aborted selection pass once `CLOSED` emptied; dense-D scoring with no data model), the gate as answered (one section per density, listed at every density ≤ own D, ranked by T, gate = best LV T at D' ≥ D plus `Tchd` below the cap; Claimed cannot gate *by construction* via `sssp_record()` → `lean_verified()`, all three of `site_tier == 'verified'`, `rerun['state'] == 'passed'`, non-empty `receipt_path`), and what survives (α < 11/12 or a better T at some D — a research result, so the board is now a route to audit, not a record to take).

**§5 — generalization.** Now **five** questions; the fifth is new: **gate semantics — what is FREE** ("never infer the gate from the board table"). Anti-patterns gained two: "drafting a package because the arithmetic closes" and "citing a guard from memory instead of from the line" (verifier #136: we wrote `T >= D + 1`, the guard is `T >= n·D` at `:100`; we retracted in-thread).

## AGENT_PROOF_KIT.md

**Header.** v2 disclosure added; sources extended to verifier/site #9, #97, #102, #124, #134–#136, site PR #12.

**§4 checklist.** New final item: **ranking/ordering key verified against the board's stated direction semantics, not just against the repro**.

**New §5 — "Ranking keys and row audits."**
- *Ranking-key trap* (site #9 / PR #12): our LGTM verified the null parse and the newest-first fallback but not the ordering semantics; `A·10^p` is the value at `d = 10` only, so `2d^{18}` (2e18) outranks `80,000d^{17}` (8e21) while the board ranks the second first; the maintainer declined the merge and ordered on `p` first, then `A`, exactly, because the row's statement says "lower `p` is better, with `A` also tracked". Rule stated as you specified.
- *Three audit shapes:* local replay audits (#97, #102 merged 2026-11-02; **#124 filed and still open** — so described as open); pinned-SHA provenance vs CI state (#135 — Finding 1 withdrawn in-thread, Finding 2 confirmed); independent replication of a paper's proofs (#136 — Lemma 3.7 false as printed, 23.59375 vs 23.546875, 366/988 violations, `cargo test --release` 14/14). Each with what to leave behind.
- *How to file* so maintainers can act: one claim per bullet with line/commit, what you verified vs not, named artifacts, ruling not demand, and retract your own wrong half loudly.

**§6** — the negatives bank, renumbered only; content unchanged apart from two micro-condensations.

## README.md

One v2 line in the intro (two case studies, the ~6h arc, the vacuous-row lesson) and the two document bullets updated to name both case studies, the headline lesson, the ranking-key check, and the audit shapes.

## What was compressed (please sanity-check these)

The 12 KB cap required cutting ~4.7 KB from the playbook draft. Nothing below removes a lesson; each is a fact moved to a pointer or a count:
- The snaky enablement table (5 files × "who adds it") became a prose list; the "who adds it" column is dropped (all but one were maintainer).
- The snaky timeline one-liner and several date-stamped restatements folded into the surrounding sentences (all timestamps that remain are sourced; 16:31/17:02/17:21/18:53/20:05/23:46:58 all still present).
- The directed-SSSP `18:14:43` follow-up keeps its role but drops the two SHA strings (they are in the intro row for `b0c848f5` anyway, and the `CHDRow.lean` sha256 is in #134).
- The 11-board name census in "Where this applies next" became a count plus the verifier-tree/`chat-knowledge.json` citation. **This is the one cut that loses bytes of information rather than words** — restore it if you want the tail named explicitly.
- I dropped the van-der-Waerden NO-GO paragraph I had drafted for §5 (it was my own addition, not a requested item).

## Not sourced / deliberately not claimed

- No claim is made that the density-time gate behaves correctly for *all* SSSP submissions — only that it exists at `b0c848f5`, refuses `T ≥ n·D` at `:99-100`, and builds its record set from `lean_verified` rows.
- The "~6h" in the README is the board's whole opening arc (10:56 statement → 19:41 issue close, ~8h45m to close, 5h11m to the gate commit); the precise sourced figure used everywhere else is 43 minutes filing → route-opening commit. If you prefer one number, say which.
- `#124` is open at the time of writing; if it merged since, the "open" label needs a one-word update.
