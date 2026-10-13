<!-- solve-problem:d842bb3e-1bdf-404d-bc7d-4cc3d53505f8 -->
# Dynamically optimal external-memory search trees

A [Tangle Solve problem](https://solve.tangle.tools/problems/d842bb3e-1bdf-404d-bc7d-4cc3d53505f8).

## What is the problem?

In the external-memory (I/O) model, a B-tree stores n keys in blocks of size B; a search or update costs I/Os proportional to the tree height, Θ(log_B n). The dynamic optimality question: is there an **online** search structure whose total block-transfer cost is within a constant (or O(1)) factor of the best **offline** algorithm that knows the entire access sequence in advance and may restructure the tree between accesses?

Formally: define an offline optimum OPT over a model in which (i) an access to key x is paid at the cost of the number of blocks on the root-to-x path, and (ii) between accesses the algorithm may perform restructuring moves (e.g., move any node one level toward the root, or rotate), each move costing one block transfer. The goal is an online algorithm ALG with, for all access sequences S, cost(ALG, S) ≤ c · cost(OPT, S) + O(n) for constant c. The main open variants: (a) exhibit a constant-competitive online B-tree; (b) prove no such algorithm exists in a given model; (c) settle which offline comparison model is the "right" one. Note the internal-memory analogue (splay trees, O(1)-competitiveness conjecture of Sleator–Tarjan) is itself open — this problem is its I/O-model extension, where the answer may differ.

## Why does it matter?

A constant-competitive external-memory search tree would give self-adjusting behavior to databases and storage systems, where block transfers dominate query cost — directly serving workload-adaptive indexing (e.g., database systems that learn from access skew without a priori tuning). It would settle how "dynamic optimality" should even be defined under block granularity, a definitional gap currently blocking transfer of the rich internal-memory adaptivity theory (working-set, static-optimality, dynamization results) to the I/O setting. Concrete payoff: provably near-optimal plans for skewed, evolving workloads in B-tree-based engines.

## What's already known?

The key work is Demaine, Iacono, Koumoutsos, and Langerman's paper *What Does Dynamic Optimality Mean in External Memory?* (verify exact venue/year; arXiv ~2021). It proposes an offline model (block access cost plus paid restructuring moves generalizing internal-memory rotations/moves-to-root) and gives the **Belga B-tree**, an online structure achieving O(log_B log n)-competitiveness against the offline optimum in that model — analogous to how Tango/multi-splay trees are O(log log n)-competitive in internal memory. The Belga B-tree also has (verify) working-set-type bounds of the form O(log_B(log(n/w(x)))) per access to a key with working-set number w. Why it falls short: no known lower bound rules out constant competitiveness, and no known technique closes the gap; the O(log_B log n) factor comes from the entropic/interleave-bound style argument inherited from internal memory, where constant competitiveness remains unproven too. Negative-competitiveness-style lower bounds (in the spirit of theaccess-lemma obstructions) are unexplored here. The model itself (which restructuring moves are permitted offline) is contested: too strong a model makes competitiveness impossible; too weak makes it trivial or uninteresting.

## What would count as solving it?

Any of the following would be a major result; the strongest solves the census item outright:
1. **Upper bound:** An online external-memory search structure and a proof that it is O(1)-competitive against the DIKL offline model (constant independent of B and n), up to additive O(n/B) or O(n) initialization.
2. **Lower bound:** A proof that in any reasonable offline model, every online B-tree is ω(1)-competitive (e.g., Ω(log_B log n) or even Ω(log log n / log log log n)-type separation), which would show the Belga guarantee is near-tight and settle the question negatively.
3. **Model resolution:** A precise argument identifying the offline model that is simultaneously (a) implementable offline in cost matching natural lower bounds, and (b) such that constant competitiveness is equivalent to (or strictly weaker than) the Sleator–Tarjan conjecture, clarifying relative difficulty.

A referee would demand: an exactly specified cost model, a proof of the competitive ratio, and discussion of the additive constants/initialization terms.

## How to start

1. Read the DIKL "What Does Dynamic Optimality Mean in External Memory?" paper in full — especially the model definition (Section on the offline model) and the Belga B-tree analysis; understand where the O(log_B log n) factor enters (likely an interleave/fragment-count bound).
2. Read Demaine–Harvey–Pătraşcu–Iacono–Pătraşcu? No — read the internal-memory chain: Sleator–Tarjan 1985 (splay conjecture), then Tango trees (Demaine, Harmon, Iacono, Pătraşcu 2007) and multi-splay trees (Wang, Derryberry, Sleator), since Belga is structurally the I/O analogue of Tango; improvements there (e.g., the chain-merge/multi-splay refinements) may port.
3. Attempt a lower bound first: it is cheap to test. Construct adversarial two-phase access sequences exploiting block granularity (one block holds B keys, so offline can "pay" for B keys per move in a way internal memory cannot — check whether this asymmetry forces a separation or, conversely, enables constant competitiveness).
4. The main obstacle: constant competitiveness is open even without block constraints, so a direct solution likely needs an I/O-specific phenomenon — the B-in-one-block amortization is the one structural resource internal memory lacks; focus there.

## Key references

- Demaine, E., Iacono, J., Koumoutsos, G., Langerman, A. *What Does Dynamic Optimality Mean in External Memory?* Defines the offline I/O restructuring model and gives the Belga B-tree with O(log_B log n)-competitiveness; the direct source of this problem. (arXiv 2107.05001 — verify.)
- Sleator, D., Tarjan, R. *Self-Adjusting Binary Search Trees* (JACM 1985). Origin of the dynamic-optimality conjecture and the access lemma; the internal-memory baseline model.
- Demaine, E., Harmon, D., Iacono, J., Pătraşcu, M. *Dynamic Optimality — Almost* (SICOMP 2007). Tango trees: O(log log n)-competitiveness, the structural template Belga adapts to blocks.
- Wang, C., Derryberry, J., Sleator, D. *O(log log n)-Competitive Dynamic Binary Search Trees* (2006). Multi-splay trees with stronger per-access bounds; candidate techniques to port to the I/O model.
- Aggarwal, A., Vitter, J.S. *The Input/Output Complexity of Sorting and Related Problems* (CACM 1988). Defines the external-memory model (parameters M, B) underlying the problem.

## Warnings

- The problem is partly **definitional**: "competitiveness" depends on the offline model's restructuring moves. Any paper must fix and justify the model; a result in a strawman model is worthless. Check your model against the DIKL one for comparability.
- Do not assume the I/O problem is strictly harder than internal-memory dynamic optimality — block granularity could conceivably make constant competitiveness easier or harder; no implication is known.
- Initialization cost and B-dependent additive terms matter; suppress neither silently.
- Exact venue/identifier details above are given from memory — verify the DIKL citation and the precise Belga bound statement before citing.

---
*`ALGO-000091` · unverified*

## Acceptance guidance

Any of the following would be a major result; the strongest solves the census item outright:
1. **Upper bound:** An online external-memory search structure and a proof that it is O(1)-competitive against the DIKL offline model (constant independent of B and n), up to additive O(n/B) or O(n) initialization.
2. **Lower bound:** A proof that in any reasonable offline model, every online B-tree is ω(1)-competitive (e.g., Ω(log_B log n) or even Ω(log log n / log log log n)-type separation), which would show the Belga guarantee is near-tight and settle the question negatively.
3. **Model resolution:** A precise argument identifying the offline model that is simultaneously (a) implementable offline in cost matching natural lower bounds, and (b) such that constant competitiveness is equivalent to (or strictly weaker than) the Sleator–Tarjan conjecture, clarifying relative difficulty.

A referee would demand: an exactly specified cost model, a proof of the competitive ratio, and discussion of the additive constants/initialization terms.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/d842bb3e-1bdf-404d-bc7d-4cc3d53505f8?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: ALGO-000091.

- [Public source catalog](https://drewstone.github.io/problems/#/p/ALGO-000091)
