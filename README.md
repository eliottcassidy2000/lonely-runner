# The Lonely Runner Conjecture in Lean 4

A self-contained [Lean 4](https://leanprover.github.io/) / [Mathlib](https://github.com/leanprover-community/mathlib4)
formalization of the **Lonely Runner Conjecture (LRC)**: its definitions together with a suite of
**unconditionally proved** elementary results. Mathlib has no lonely-runner content as of `v4.30.0`,
so the definitions here are themselves a new contribution.

Every theorem in this repository is **fully proved** — no `sorry`, no `admit`, no `native_decide`,
and no project-specific axioms. Each headline result reduces to Lean's three standard axioms
`[propext, Classical.choice, Quot.sound]`, and the file [`LonelyRunner/Basic.lean`](LonelyRunner/Basic.lean)
prints this audit at build time via `#print axioms`.

## The conjecture

Model the track as the unit circle `UnitAddCircle = ℝ ⧸ ℤ`. A runner of integer speed `v`, at time
`t`, sits at `(v · t : UnitAddCircle)`; its distance to the stationary runner at the origin is the
quotient norm `‖(v·t : ℝ)‖ = |v·t − round(v·t)|`. Write `IsLonelyAt S r t` when, at time `t`, every
speed in `S` is at circle-distance at least `r` from the origin.

> **Lonely Runner Conjecture (`Conjecture k`).** For every set `S` of `k` distinct positive integer
> speeds, there is a time `t` at which all runners are simultaneously at distance at least `1/(k+1)`
> from the origin: `∃ t, IsLonelyAt S (1/(k+1)) t`.

The general conjecture is a well-known **open problem**; it is established in the literature only for
small numbers of runners. **This repository does not claim to resolve it.** What it provides is a
rigorous, machine-checked foundation: the exact statement, the base cases, the standard reductions,
and the sharpness of the constant — all unconditional.

## What is proved here

All results live in [`LonelyRunner/Basic.lean`](LonelyRunner/Basic.lean) unless noted.

| Result | Statement | Location |
|--------|-----------|----------|
| `IsLonelyAt`, `Conjecture` | Definitions of loneliness and the conjecture | [Basic.lean:51](LonelyRunner/Basic.lean#L51), [:57](LonelyRunner/Basic.lean#L57) |
| `norm_natCast_div` | Circle-distance of `m/n` to the integers | [Basic.lean:61](LonelyRunner/Basic.lean#L61) |
| `isLonelyAt_of_forall_not_dvd` | **q-witness lemma**: if no speed is divisible by `q`, then time `1/q` is `1/q`-lonely | [Basic.lean:81](LonelyRunner/Basic.lean#L81) |
| `exists_isLonelyAt_of_forall_not_dvd` | **Covering reduction**: any counterexample to `Conjecture k` must contain a multiple of `k+1` | [Basic.lean:91](LonelyRunner/Basic.lean#L91) |
| `isLonelyAt_image_mul` | **Dilation invariance** of loneliness | [Basic.lean:102](LonelyRunner/Basic.lean#L102) |
| `norm_le_of_abs_le` | The origin window `|t| ≤ r/v` is `r`-dangerous for speed `v` | [Basic.lean:117](LonelyRunner/Basic.lean#L117) |
| `conjecture_one` | **The conjecture for `k = 1`** | [Basic.lean:130](LonelyRunner/Basic.lean#L130) |
| `exists_norm_le_of_mem_Icc`, `not_isLonelyAt_Icc_of_lt` | **Tightness** (via Dirichlet): the constant `1/(k+1)` is optimal for `{1,…,k}` | [Basic.lean:151](LonelyRunner/Basic.lean#L151), [:167](LonelyRunner/Basic.lean#L167) |
| `exists_isLonelyAt_pair_coprime`, `conjecture_two` | **The conjecture for `k = 2`** (coprime middle-third construction + gcd/dilation reduction) | [Basic.lean:182](LonelyRunner/Basic.lean#L182), [:251](LonelyRunner/Basic.lean#L251) |
| `isLonelyAt_of_unit_residue_missed`, `exists_residue_one_of_tight` | **Unit-residue improvement lemma**: constructive strict improvement over `1/q`; structural constraint on `q`-tight sets | [Basic.lean:308](LonelyRunner/Basic.lean#L308), [:377](LonelyRunner/Basic.lean#L377) |
| Three-gap sampling | Per-interval lattice-count discrepancy `≤ 1` and the `m`-arc `±m` band (an elementary equidistribution lemma) | [ThreeGapSampling.lean](LonelyRunner/ThreeGapSampling.lean) |

Note: [`ThreeGapSampling.lean`](LonelyRunner/ThreeGapSampling.lean) is a general-purpose supporting
lemma (equally-spaced sampling of a union of arcs); it is **not** on its own a case of the conjecture.

## Scope and honesty note

This repository is deliberately limited to results that are **proved outright**. The conjecture is
verified here for `k = 1` and `k = 2`; the constant is shown to be sharp for all `k`; and the standard
reductions (covering, dilation) and a strict-improvement lemma for tight sets are formalized. Cases
`k ≥ 3` and the full conjecture are **not** proved here. Any future work aiming higher should keep this
same standard: a claim counts only when the top-level theorem carries no undischarged hypotheses and its
`#print axioms` shows the standard axioms alone.

## Building

Requires [`elan`](https://github.com/leanprover/elan) (the Lean toolchain manager). The toolchain
(`leanprover/lean4:v4.30.0`) and the exact Mathlib revision are pinned in
[`lean-toolchain`](lean-toolchain) and [`lake-manifest.json`](lake-manifest.json).

```bash
lake exe cache get   # download Mathlib's prebuilt binaries (avoids a multi-hour compile)
lake build           # compile the library and run the #print axioms audit
```

## License

Released into the **public domain** under the Unlicense — see [LICENSE](LICENSE). The `.lean` file
headers match. Note that a direct submission of these definitions to Mathlib would first require
relicensing to Apache 2.0 (and signing the Mathlib CLA); the public-domain dedication here does not
prevent that but is a separate, more permissive choice.
