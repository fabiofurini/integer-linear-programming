---
title: Integer Linear Programming
hide:
  - navigation
  - toc
---

<div class="hero" markdown>

# Integer Linear Programming

Teaching material by **[Fabio Furini](https://fabiofurini.github.io/)**, associate professor at [DIAG](https://www.diag.uniroma1.it/), Sapienza University of Rome.

The course covers integer and mixed integer programming models, LP relaxations, ideal formulations, valid inequalities, cutting planes, branch-and-bound, branch-and-cut, MIP solvers and heuristics. It includes definitions, proofs, worked examples and exercises with solutions.

[Start with the chapters :material-arrow-right:](models-and-bounds/index.md){ .md-button .md-button--primary }
[Go to the exercises](exercises/index.md){ .md-button }
[Open the interactive lab](lab/index.md){ .md-button }
[Download materials](downloads.md){ .md-button }

</div>

## How each chapter is organized

Each chapter is one of the course notes, with the same numbers and the same colors as the
lecture notes: on the website and in the PDF you find everything in the same place.

<div class="grid" markdown>

!!! definizione "Definition"
    The precise meaning of a new concept.

!!! teorema "Theorem, proposition, observation"
    A result to remember, with its hypotheses.

!!! esempio "Example"
    A computation worked out in full, one step at a time.

!!! chiave ""
    **In green** the key point to take away.

!!! algoritmo "Algorithm"
    The pseudocode of a method, in red.

</div>

??? dimostrazione "Proof — opens with a click"
    All proofs are included, but folded: first read the statement, then open
    the proof when you want to study it. The same holds for the solutions of the exercises.

## The parts of the course

<div class="grid cards" markdown>

-   :material-sigma: **I · Models and bounds**

    ---

    What an integer linear programming problem is (MIP, ILP, BIP), its solutions and outcomes; primal and dual bounds, the gap and the LP relaxation.

    1\. Integer linear programming · 2\. Primal and dual bounds

    [:octicons-arrow-right-24: The chapters](models-and-bounds/index.md)

-   :material-vector-polygon: **II · Strengthening the formulation**

    ---

    Ideal formulations, valid inequalities and cover inequalities: how to strengthen a formulation.

    3\. Ideal formulations · 4\. Valid inequalities · 5\. Cover inequalities

    [:octicons-arrow-right-24: The chapters](formulations/index.md)

-   :material-file-tree: **III · Exact methods**

    ---

    The cutting plane algorithm with Gomory fractional cuts, LP-based branch-and-bound and branch-and-cut. Examples worked out step by step and exercises with complete solutions.

    6\. The cutting plane algorithm with Gomory fractional cuts · 7\. LP-based branch-and-bound · 8\. Branch-and-cut

    [:octicons-arrow-right-24: The chapters](exact-methods/index.md)

-   :material-cog-outline: **IV · MIP solvers**

    ---

    What a MIP solver does (presolve, cuts, primal heuristics, tree strategies, tolerances) and how to read its log, with real examples.

    9\. MIP solvers: what they do

    [:octicons-arrow-right-24: The chapters](solvers/index.md)

-   :material-lightning-bolt: **V · MIP heuristics**

    ---

    Rounding, diving, feasibility pump, RINS, local branching, relax-and-fix and fix-and-optimize. Examples and exercises with complete solutions.

    10\. MIP heuristics

    [:octicons-arrow-right-24: The chapters](mip-heuristics/index.md)

</div>

---

By the same author:

- **[Linear Programming](https://fabiofurini.github.io/linear-programming/)** — polyhedra, the simplex algorithm, duality and the dual simplex, with the interactive lab;
- **[Convex Optimization](https://fabiofurini.github.io/convex-optimization/)** — convex sets and functions, convex optimization problems, with interactive graphs;
- **[Algorithms](https://fabiofurini.github.io/algorithms/)** — number representation, pseudo-code, sorting and searching, analysis and recursive algorithms;
- **[Combinatorial Optimization](https://fabiofurini.github.io/combinatorial-optimization/)** — knapsack, bin packing, coloring, TSP, scheduling and lot sizing, solved in every way;
- **[Network Optimization](https://fabiofurini.github.io/network-optimization/)** — graphs, spanning trees, shortest paths, network flows, the network simplex and project management;
- **[MIP Modelling](https://fabiofurini.github.io/mip-modelling/)** — integer linear programming models, from the formulation to the solution with solvers;
- **[Operations Research Lab](https://fabiofurini.github.io/operations-research-lab/)** — modelling and solving optimization problems with Python and solvers;
- **[Mathematical Analysis 1](https://fabiofurini.github.io/mathematical-analysis-1/)** — real numbers, functions, limits, derivatives and series, with interactive graphs;
- **[Linear Algebra](https://fabiofurini.github.io/linear-algebra/)** — vectors, matrices, determinants, linear systems and eigenvalues, with the computation lab.

Teaching material by **[Fabio Furini](https://fabiofurini.github.io/)** —
[DIAG](https://www.diag.uniroma1.it/), Sapienza University of Rome.

*Questo sito è disponibile anche in [italiano](https://fabiofurini.github.io/programmazione-lineare-intera/).*
