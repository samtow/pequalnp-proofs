# L-meta-519 — hard-gap work log

**Owner:** GitHub `@samtow`
**Task:** `L-meta-519` (weight 95, "Open, a hard one"), shard 3 of 4 — *the case of a thread with exactly two inner vertices between vertices of degree 3*.
**Desk:** `GET https://pequalnp.com/desk/next?handle=samtow&hard=1`
**Lease:** `d5fdc872-96b8-4074-afef-03e53f66dc08`. Heartbeat #1 at 18:06:34Z returned 200 (expiry 18:51:34Z); heartbeat #2 at 18:15:34Z returned **410 — no such live lease**, so the background renewer stopped at once per the desk's rule. The lease ended desk-side at 18:15:34Z; no release call was needed or made afterwards.
**Start:** unix `1795457132` — 2026-11-23T18:05:32Z (21:05:32 MSK)
**Minimum work window requested:** 65 min → until unix `1795462232` (2026-11-23T19:10:32Z)

## The statement

For every `n k s`, every `G : SimpleGraph (Fin n)`:

- maximum degree at most 3;
- every cycle has length at least 7;
- `G` has an edge;
- `k` is at most the size of every vertex cover;

any fan-in-2 AND/OR/NOT circuit of size `s` for
`f_G(x) = ∃ ij, G.Adj i j ∧ x i = x j = true` satisfies
`G.edgeSet.ncard + k ≤ s + 1`.

`PEqualNP.MetaComplexity.L154` supplies the matching *upper* bound (a monotone
circuit of size `|T| + |E| - 1` from any cover `T`), so the gap is the exact lower
bound. The desk flags this step as possibly false.

## Environment

- Installed `gh` (2.46.0), `elan`/Lean 4.36.0, forked and cloned
  `brilliant-hq/pequalnp-proofs`, fetched the Mathlib cache (9117 files).
- Authenticated to GitHub only with the PAT supplied by the owner; the token was
  never sent to pequalnp.com or any other endpoint. It has very broad scopes
  (`admin:enterprise`, `delete_repo`, `workflow`, ...) and should be revoked.

## What is already checked in the repository

- `PEqualNP.MetaComplexity.L204` / `Outside/L_meta_522`: the same bound with the
  extra *branch-separation* hypothesis `T7`: every walk between two distinct
  vertices of degree ≥ 3 has length ≥ 7. Its proof (~6200 lines) eliminates
  degree-≤2 vertices, paying 3–4 gates per restricted input, and stops at a
  degree-3 vertex.
- `Outside/L_meta_616`: `n + t ≤ C.size` for cubic graphs with no 4-cycles and
  monotone circuits — weaker than the `3n/2 + t - 1` needed here.
- `Outside/L_meta_573`: a paying step for a thread with `2h` inner vertices,
  `h ≥ 3` (at least six inner vertices), paying `3h` gates.
- `PEqualNP.MetaComplexity.L658` / `Outside/L_meta_514`–`517`: the bound via
  peelability — every induced subgraph has a good leaf or a *run of four*
  degree-2 vertices. Cubic graphs have neither, so this route does not apply.

## Exact obstruction at shard 3

The gap is real, not merely a missing lemma name. On a tree with two degree-3
vertices `u, v` joined by the thread `u – a – b – v`, the branch-separation
hypothesis of L204 fails (there is a `u → v` walk of length 3), while the
hypotheses of L-meta-519 hold (max degree 3, no cycles at all, hence girth ≥ 7
vacuously). Equally, the graph has no leaf whose parent has ≤ 1 other neighbour
and no degree-2 run of length 4, so `Peelable`/L658 does not apply. A concrete
8-vertex instance (`u=0, v=3`, thread `0-1-2-3`, leaves `4,5` at `u` and `6,7`
at `v`) is written as `lean/scratch519.lean` (and a Mathlib-only variant
`lean/scratch519b.lean`) in the working copy; it formalises `deg ≤ 3`, the two
degree-3 endpoints and the short walk. NetworkX confirms: 8 vertices, 7 edges,
degrees 3,2,2,3,1,1,1,1, acyclic, walk `0-1-2-3` of length 3.
The Lean checks did not finish: the full `PEqualNP.Defs` build was still running
after ~30 min and held the lake lock, so these scratch files are **not** to be
called checked.

What the proof needs, as the desk's context says, is one new *paying step* at a
degree-3 vertex chosen from a least circuit when the only separation available
is a 2-inner-vertex thread. The existing elimination lemmas
(`elim_one_var`, `elim_two_var`, `rewrite1_contradiction`,
`rewrite2_contradiction`, `tailPay`-style arguments) pay for long odd/even
threads but require at least six inner vertices, and the short-thread case
admits extra ways for the other branch of the circuit to share a wire. This is
the piece I was not able to supply.

## Computational cross-check (inconclusive, not a claim)

I encoded exact fan-in-2 circuit synthesis as a SAT instance (`circuit_search.py`
in this workspace) and started checking `C7`, `C8`, and those cycles with up to
two leaves — the smallest non-peelable candidates satisfying maxdeg ≤ 3 and
girth ≥ 7. For these, `m + τ - 1` is 10–14. The naive encoding (one-hot gate
operators and input selectors, wire values per input assignment) was too large
for Glucose4 on the first instance: it did not return within 20 minutes, so the
run was stopped. **No counterexample was found and no verdict was obtained**;
nothing here should be read as evidence either way. A better route would be a
BDD/AIG-based canonical synthesis search, or adapting the `Live514` machinery
used in `L_meta_514`–`517`.

## Outcome

No accepted proof was produced. I did **not** open a pull request: a file with a
wrong or placeholder proof would be rejected and would consume the checked gap.
The intended honest release reason would be `missing_lemma` (the short-thread
paying step); the lease was already dead by desk action, so no release was sent.

No proof of falsity either: the flagged conjecture "may be false" was not
settled. The most useful next contribution is a checked version of
`L_meta_573`-style `tailPay` for a thread with two inner vertices, with the
extra sharing cases it permits.

## Security follow-up

Revoke `ghp_wYz...lFMiY` (shown in chat) and prefer a fine-grained PAT with
`repo` scope only, or `gh auth login` interactively.

## Final timestamps

- Started: unix `1795457132` (18:05:32Z).
- Work window requested: 65 min, i.e. through unix `1795462232` (19:10:32Z).
- Actual end-of-work note written below at unix `1795461287` (`2026-11-23T19:14:47Z`).
