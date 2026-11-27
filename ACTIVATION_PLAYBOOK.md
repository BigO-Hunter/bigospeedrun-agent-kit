# How to Activate and Win a Routeless Big O Speedrun Board

A routeless board has a merged statement but no submission route. Getting one opened and won
is a process: ask the gate's semantics before building, build to the answered spec, and deliver
through the maintainer's thread. Two worked cases:

- **snaky** — routeless → spec-compliant package in the maintainer's smoke fixture (#113) in
  under eight hours → the board's first Lean Verified row (#114).
- **directed-SSSP** — a defect report (#134) → the maintainer opened the route with a `(D, T)`
  gate that cites the report in the gate code (`b0c848f5`, `submissions.py:55`). Filing →
  route-opening commit: **43 minutes**.

## 1. What "routeless" means

Snaky had a merged statement dir (`other-snaky/`, `Model.lean` byte-identical to `openai/math`
`SnakyTwentyOne.lean` @ `1581d4cb`) and Claimed rows (19, 20), but its README said **"no verifier
profile, target generator, record gate or direct submission route is enabled."** Five files
lacked the entry — `profiles/direct.json`, the statement file, `scripts/submissions.py`
(`MODEL_IDS`, `OTHER`/`BOARDS`/`CLOSED`, a `challenge_source()` branch), the issue-template
dropdown, and `scripts/smoke.py` (needs a real proof first, #94).

Read `submissions.py` first. Snaky submissions **fell through to the `else` arm and were emitted
as an APSP theorem** (`Race.bound : EndStatement.APSP.SolvedInTime ...`, with `EndStatement` out
of scope): a routeless board is not merely closed — submitting into it silently emits a theorem
nobody intended (#106 follow-up).

## 2. The most expensive lesson: ask the gate's semantics before building

On directed-SSSP the arithmetic foothold `n^2 ; n^3` (`(2,0,3,0,0)`) was **drafted and
filing-ready** before anyone asked what a row means. `parse_sssp`, executed from the fetched
`submissions.py`, returned the corner of the range; four neighbours were verified refused; the
target generated; the staging path (`prepare` → `plan_sources` → `VerifierAudit`) traced; the
checklist written — arithmetic only, from the fork's own `chd_exact_within` over the `Tdisp`
case split, no new program. Recon had already called it a record: *"each IS a record under the
verifier's density-time rule (no listed row denser)"*.

The opened gate refuses **exactly that class**. Maintainer, #134:

> **T must be below n·D.** The second clause of `Bound` already gives C·(n+1)(m+1) for every
> graph, so a row with T ≥ n·D adds nothing to that clause. `n^2 ; n^3` is such a row, and the
> form refuses it.

In code, not merely stated — `submissions.py:99-100` at `b0c848f5`:

```python
if t >= (a + 1, b, Fraction(0)):   # (a+1, b, 0) is n·D
    raise ValueError(SSSP_FREE)     # ":57 the time T ... must be below n·D"
```

`n^2 ; n^3` sits *on* the boundary, not below: T = n³ = n²·n = n·D. The package would have
parsed, passed CI, been filed, and been refused as a form error by the statement's own free
fallback.

**Ask first, then build.** The question that killed the draft was answered in **59 minutes**.
The draft plus the build it implied (~2.5 h per CI iteration; 1–3 days to a filing) is the price
of not asking.

**Corollary — a "record" is defined by the GATE's semantics, not by the absence of denser listed
rows.** "No listed row is denser" is *necessary* under the stated rule, not sufficient: the gate
also includes what the statement gives free (clause 2's `C(n+1)(m+1)`), and below
`n log^{3/4} n` the Vals AI line `Tchd`. Both raise the floor a candidate must beat — read the
gate's comparison, not the table's emptiness.

## 3. Case study 1 — snaky: ask, then build

**The questions (#106).** Don't ask "please open the board." Ask what defines the artifact, show
homework, donate tested work:

1. **Range.** Is `maximum` 21 or wider — would an LV **20** be listed (stepping stone to 19)?
2. **Target shape.** Self-contained `VerifierStatement.lean` (avg-sensitivity layout) or a bound
   pair, and which module should `Submission/` import?
3. **Bridge.** Is proving `Bound n` in the OpenAI representation acceptable — and since it
   quantifies `∀ r M B` including unreachable states, is freshness audited there?

Plus **Homework** (checklist mapped, pins verified under `-DautoImplicit=false`) and a **Plan**
(20 first, then 19). A follow-up carrying a **tested diff patch** for `challenge_source()`
(`19/2` must be rejected — `Bound` is over `ℕ`) drew the maintainer's answers to all three: whole
numbers only, `19/2` a form error, 100 "only stops absurd values", every in-range bound runs
while no LV row exists; one self-contained statement file copied byte for byte, imported by
`Submission/`, proving `Race.bound : BigOSpeedrun.Snaky.Bound 20`; "Prove `Bound n` directly.
That is the only theorem that the verifier checks," freshness on unreachable states **part of
the statement**, axioms `propext`/`Classical.choice`/`Quot.sound`; and "Please do not open a
submission issue before then."

The statement landed with sha256 `49e630b3…780b`; the maintainer wrote the verifier change
themselves — the patch's value was the analysis.

**Doubly validated.** snaky: 50 minutes of asking bought the spec. directed-SSSP: 43 minutes
bought the route. Both answers were things recon could not infer from the tree: a *policy* value
(allowed artifact shape), and a *statement-derived* one (`T < n·D` reads as an ordinary grammar
guard until someone says it is the free bound). Recon reads code; only the maintainer reads
intent.

**Building to the spec.** Byte-level mechanics (pins byte-equal, statement vendored verbatim,
`#print axioms`, zero `sorry`, no `native_decide`, Apache-2.0 only) are the
[AGENT_PROOF_KIT.md](AGENT_PROOF_KIT.md) §4 checklist. Snaky-specific: **the root-module trap** —
`solution_module: Submission` means the **root** `Submission.lean` exports the contract theorem
and `Submission/` is the only source root; nesting it as `Submission/Submission.lean` builds fine
and still fails the profile (cousin: a namespaced subset-sum theorem the audit file could not
resolve). And the **proof arc**: show the *total, fresh-on-every-state-including-unreachable-ones*
policy wins, then discharge the `playState` conjuncts by induction.

**Delivery: the thread, not a submission issue.** Post repo URL + commit SHA in the activation
thread. The maintainer ran the smoke and opened #113. **Once open, the form is byte-strict**:
headings exactly `### Problem`, `### Repository URL`, `### Commit SHA`, `### Bound`, in that
order — #114 bounced in 11 seconds on an extra heading.

## 4. Case study 2 — directed-SSSP: report first, let the GATE define the record

Not a certificate problem: exact **directed SSSP**, nonnegative **real** weights, ONE uniform
deterministic C-HD word-RAM program, under `Bound a b a' b' c'` — one program carrying both a
sparse clause and an unconditional `C·(n+1)(m+1)` fallback (which disqualifies array-Dijkstra
shapes). Rows are `D ; T`; the page scores one α in `O(n log^α n)`; the `(D, T)` table is the
"site half".

The opening arc: statement + `D ; T` grammar → smoke #127 closed → **#134 filed** →
**`b0c848f5`** (route + `(D, T)` gate, `CLOSED` emptied, form and docs updated, #134 cited in the
message *and* at `:55`) → answers → hash-verified follow-up → closed.

Two of three answers were code answers already measured (Claimed does not gate —
`other_record()`; a dense-D row verifies but has no schema to land on). **The real error:**
framing "the site half doesn't exist yet" as *the* blocker, when the deeper one was the
statement's own free bound (§2). **Two defects:**

1. **A dormant crash on the post-change path.** `qualifies_for_run` evaluated
   `Fraction(value['bound'])`; a `D ; T` string raises `ValueError` and only `derivation.Defer`
   was caught — one dense-D submission would abort the whole selection pass. Unreachable *only*
   because `CLOSED = {SSSP}` skipped it first: reachable the instant the route opened, for every
   submitter. The opening commit replaces both and tests that an SSSP issue goes *through*
   selection. **A crash reachable only after another change lands is still a crash: audit the
   post-change path.**
2. **Dense-D scoring had no data model** — single-α only, so "verified but unscored" was real;
   say so, don't file into it.

**The gate as answered:** one section per density; a row is listed in each section at a density
at or below its own D, ranked there by T; the gate for `(D, T)` is the best LV T at density ≥ D,
and at or below `n log^{3/4} n` also Vals AI's line `Tchd(D)`; Claimed, paper and classic rows
never gate — Claimed cannot, *by construction*: `sssp_record()` builds its gate set through
`lean_verified()` (`site_tier == 'verified'` **and** `rerun['state'] == 'passed'` **and** a
non-empty `receipt_path`), so such rows are absent from the data structure, not merely
unconsulted. This is the gate as measured at `b0c848f5`; the description is scoped to that
commit.

**What survives.** With the C-HD row itself LV at `n log^{3/4} ; n log^{11/12}`, a same-bound
proof *ties*, every row at or above `T = n·D` is refused, and rows under the cap must beat
`Tchd`. What is left is new algorithmic content (α < 11/12, or a better T at some D) — a research
result, not a sprint. Directed-SSSP is now a **route to audit**, not a record to take.

## 5. Generalization: five questions every activation must answer

1. **Range and semantics.** What is expressible (snaky rejects `19/2`)? Is `maximum` a gate or an
   absurdity cap (snaky: 100, cap only)? What happens while no LV row exists (snaky: best = range
   end, so every in-range bound runs)?
2. **Statement shape.** Self-contained file vs imported `Model`? Byte-for-byte copy contract?
   Which module does `Submission/` import, and the exact contract theorem?
3. **Proof obligation + totality quirks.** Enumerate the conjuncts (snaky `Bound n`: ∃σ total;
   freshness `∀ r M B` *including unreachable states*, which forced a `freshFin` fallback; card
   counts; disjointness) — and ask whether unreachable-state behavior is audited: it is, "part of
   the statement."
4. **Gate semantics — what is FREE.** What does the gate compare at your own `(D, T)`, and what
   does the statement already hand every program? **Never infer the gate from the board table.**
5. **Process gates.** Smoke run before route opens? Who runs it (#94)? Deliver where? Allowed
   axioms?

All of it is in `submissions.py`, `profiles/*.json`, `docs/targets.md`, the board's `challenges/`
README and `Statement.lean`, the issue template and `smoke.py`. Post the gap map — that is what
lets a maintainer say yes.

**Anti-patterns:**

- **Filing early.** The form lacks the problem; `challenge_source()` emits a wrong theorem.
- **Drafting because the arithmetic closes.** Easy math is not evidence the row is wanted (§2).
  `T ≥ n·D` and "ties the incumbent" look like records from outside the gate and are worth
  nothing inside it.
- **Posting unreviewed packages.** Two concurrent snaky packagers; one canonical package passed
  the checklist first.
- **Believing recon slack figures.** The DFT "2.8e-8 internal slack" was wrong ~2000x (true
  headroom 3.7e-12, exact `Fraction`); contingency-tables' p=17 is integer-locked, so no slack
  exists; integer-mult 1.6415x → 1.6365x.
- **Citing a guard from memory instead of from the line.** #136 reported `parse_sssp` refuses
  `T >= D + 1`; it refuses `T >= n·D` (`submissions.py:100`). Corrected in-thread, retracted.
- **Assuming repo/branch defaults.** integer-mult's default branch is `master`;
  `.../commits/main` returns 422. Verify pins by SHA.
- **Reconning a board before checking the route policy.** A review-repository board is a donation
  lane wearing an activation lane's clothes (§6) — the policy doc predicts the verdict for free.
- **Inferring gate behavior from a close message.** The close message is the map; the gate
  function at the live commit is the measurement (§8).

## 6. Route policy: pre-check the six criteria before spending recon

The verifier publishes its own route policy:
`docs/direct-submissions.md#routes-for-other-bounds-boards` (@ `16cbc5f8`). Read it BEFORE
spending recon on a board — it decides which asks are even admissible, and it predicted three of
four zero-LV outcomes from the text alone.

1. **Ask first.** Unsolicited statement PRs are closed unread. Maintainers decide which boards
   get routes and in what order, one route at a time, and may open their own first to rerun a
   row's proof (as on critical-line-zeros, §7).
2. **A route opens only if ALL six criteria hold:**
   - **No review repository.** The policy names QuasiRiemannTracker as the example: a board whose
     evidence system *is* a review repo is structurally excluded from verifier routes — the
     tracker's CI is its verification. This redirected the quasi-Riemann activation plan: the
     pre-built receipts became a donation into the tracker's own named gap instead (§7).
   - **A faithful pinned upstream statement, byte for byte**, with no submitter hypotheses (the
     #140 ruling is embedded in the policy: a hypothesis can be part of a statement only when the
     trusted statement fixes it; a submission can never add one).
   - **Non-vacuous, with too-strong bounds unprovable** (the controls: baseline proved,
     below-floor not provable).
   - **Pins = an existing profile or an approved new one.**
   - **The proof fits GHA-runner limits.**
   - **The Lean Verified row ADDS INFORMATION.**
3. **The verifier owns `challenges/`; statements give proposers no credit or priority.**
4. **Credit follows the public record:** construction author first, the filer credited for the
   Lean package (canonized from the #142 ruling — AGENT_PROOF_KIT §6); a same-value rerun lists
   the row's authors.

**Rule: evaluate a board against the criteria BEFORE spending recon on it.** The review-repo
criterion predicts a quasi-Riemann refusal for free; the faithful-statement criterion predicted
the #145/#146 shape (both answered NOT_PLANNED — the maintainer writes trusted statements
himself). **Review-repository boards are audit/donation lanes, not activation lanes.**

## 7. Receipts-first activation: never ask a maintainer to imagine feasibility

Three asks, three shapes, measured outcomes:

- **#140 (unit-distance-grid) — asked without receipts, refused.** Filed with three precise
  pre-build questions and a promise of fast assembly ("algebra pre-verified = assembly not
  research") but no built Lean artifact. NOT_PLANNED in ~3h (600+ words): a row's conclusion must
  BE the grid step itself — arithmetic on the witness (`grid_counts` proving `8·4 = 32` by
  `decide`) is not a proof of the row — and "no Lean source exists" for the four upstream
  hypotheses. A pre-build attempt would have surfaced both facts before filing. The refusal was
  still the best possible artifact: an explicit quoted resume path ("If a complete Lean statement
  of the final grid step appears, with the upstream lemmas as explicit hypotheses, please open a
  new issue with it"), and the lane parked against a full spec. Precise questions get maintainer
  answers in hours even when the answer is no.
- **#146 (critical-line-zeros) — receipts posted with the questions, route adopted.** The route
  ask carried the rational-adapter plan; the pre-build finished within the hour and its receipts
  went into the thread before the verdict (native build of anthropics/formal-math @ `16a5ce41`:
  rc=0, 9,015 jobs, 28m43s; all 17 headline theorems on exactly
  `[propext, Classical.choice, Quot.sound]`, zero `sorryAx`; the ~5-line adapter
  `Race.bound (2/3)` PROVED over zeta23's own definitions, nothing redefined). Verdict:
  NOT_PLANNED *for the filer* — the maintainer writes the trusted statement from the zeta23
  Challenge surface, chooses the pins, and reruns Anthropic's own proof first (row credits
  Anthropic). A win dressed as a no: the receipts are the feasibility evidence inside their
  build, the follow-on row (269/400) is named, the audit of the shaky top row explicitly blessed
  ("An audit package can go to auto-research/audits/, as usual").
- **quasi-Riemann — receipts rebuilt BEFORE asking, then donated.** The review-repo exclusion
  (§6) forbids a verifier route; instead the 7/8 baseline was rebuilt locally under the tracker's
  own pins — 2,927/2,927 vendored modules compiled from source in 3h00m51s, rendered
  `Challenge.lean` byte-identical to the tracker's pr69 receipt (`a9d290c7…`), all 2,926 vendored
  OAI modules blob-verified against openai/math @ `fd4aeeb2`, `#print axioms` = std-3 on all
  eight declarations, the unpatched-dependency failure preserved as a negative control — and
  filed GTimG/QuasiRiemannTracker **#124** with the receipts published (bundle sha256
  `b416bbdf…`). It fills the tracker's own uniform "no independent rerun" caveat, on a
  different-hardware basis.

**Rule: never ask a maintainer to imagine feasibility — hand them the build log and the axiom
audit.** A refusal WITH receipts redirects the work (QR → donation; CLZ → maintainer adoption
plus a named follow-on); a refusal without them parks the lane. A negative build is also a
receipt: the QR hosted verify OOMed on three rows (#66/#48/#43), so the local rerun is cost
evidence whichever way it lands.

## 8. The verified-tier-empty pattern

Some boards have rows already machine-checked *elsewhere* — merged tracker PRs, vendor Lean
repos, triple-kernel CI — but unverified *on the board*: the Lean Verified tier is empty. Two
measured cases:

- **quasi-Riemann:** 42 rows; 40 Human-Verified from merged GTimG/QuasiRiemannTracker PRs (full
  Lean proofs, 128–237 modules each; CI = Comparator statement+axioms check plus a
  Lean/NanoDa/con-ron triple kernel), one Claimed-conditional, one the vendored OpenAI 7/8
  baseline — board LV tier empty. The tracker's own uniform caveat: **no independent rerun** (its
  hosted verify OOMed or timed out on three rows: #66/#48/#43).
- **critical-line-zeros:** the Anthropic breakthrough row's page itself says the zeta23
  formalization is sorry-free on standard axioms and "**the verifier has not run this proof**."

**The rebuild-audit is the skeleton key:** rebuild a merged proof locally under the pins and
kernel-check the exact statement, and the first LV row exists — no new mathematics. Empty tier
also means no gate: with no LV rows, every in-range bound runs (snaky's answered semantics; the
directed-SSSP analog is `sssp_qualifies` with an empty record set). Any rebuilt proof becomes the
first LV row — maximum-impact-first picks the top-value rung whose rebuild you can afford (QR
cost ladder: 7/8 baseline < Levy PR#6 < Collet PR#9 < Bebel PR#103 = board top).

**Tie-gate semantics (traced at `b0c848f5d`, the code live at the close).** A run starts only for
a bound **strictly below** the gate. The directed-SSSP gate is density-conditional:
`sssp_best(D)` = lex-smallest T over {LV point rows at density ≥ D} ∪ {LV function lines
evaluated at D, below the cap}, and a candidate qualifies only if its T is strictly below that
value *at its own D* — Pareto-aware by construction. Sibling's #139 row closed as an **exact
tie**: `sssp_time_at(chd-lean-cost, (1,0))` evaluates the verified cost curve's lex-max term to
`(1, 2/3, 0)`, lex-identical to #139's T, and the `sssp_qualifies` docstring is explicit that a
tie starts no run. The close was first misread as a linear-ratchet default on incomparable pairs;
the code trace refuted that before filing — a genuinely incomparable bound whose T strictly beats
the gate at its own D qualifies, so the Pareto semantics is already implemented. **Trace the call
site, not the close message.** (The fairness method around this self-refutation: AGENT_PROOF_KIT
§5.)

## 9. Landing work under load

Under sustained host load, prefer a persistent serialized queue over one-shot polling watchers:
when a bounded retry mechanism exhausts, enqueue its staged work rather than respawning it —
patience is the landing strategy.
