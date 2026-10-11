---
title: Integer Linear Programming
hide:
  - navigation
  - toc
---

<div class="hero" markdown>

![Under construction: the website and the course material are still being written](img/under-construction.svg){ width="560" }

**Website under construction.** The material is still being written: slides, interactive graphs and
notebooks will follow later.

# Integer Linear Programming

Teaching material conceived and developed by **[Fabio Furini](https://fabiofurini.github.io/)**, associate
professor at [DIAG](https://www.diag.uniroma1.it/), Sapienza University of Rome.

**The course lecture notes, online**: integer and mixed integer programming, LP relaxation and bounds, valid inequalities, cutting planes, branch-and-bound and branch-and-cut, MIP solvers and heuristics, with definitions, worked examples step by step and exercises with solutions.

[Start with the chapters :material-arrow-right:](models-and-bounds/index.md){ .md-button .md-button--primary }
[The exercises](exercises/index.md){ .md-button }
[:material-download: Download the lecture notes (PDF)](downloads.md){ .md-button }

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

    1\. Integer linear programming · 2\. LP relaxation and dual bounds

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

- **[Linear Programming](https://fabiofurini.github.io/linear-programming/)** — the prerequisite of this course: LP problems, the simplex algorithm, duality and the dual simplex algorithm, with interactive graphs;
- **[Combinatorial Optimization](https://fabiofurini.github.io/combinatorial-optimization/)** — knapsack, bin packing, coloring, TSP, scheduling and lot sizing solved in every way (under construction);
- **[Operations Research Lab](https://fabiofurini.github.io/operations-research-lab/)** — the lab on modelling and solving optimization problems, with Python and solvers;
- **[MIP Modelling](https://fabiofurini.github.io/mip-modelling/)** — the module on integer-variable models, with the same tools and the same style;
- **[Mathematical Analysis 1](https://fabiofurini.github.io/mathematical-analysis-1/)** — the analysis lecture notes, with interactive graphs;
- **[Linear Algebra](https://fabiofurini.github.io/linear-algebra/)** — the preparatory course, with the step-by-step computation lab;
- **[Convex Optimization](https://fabiofurini.github.io/convex-optimization/)** — convex sets and functions, optimization problems, with interactive graphs;
- **[Algorithms](https://fabiofurini.github.io/algorithms/)** — number representation, pseudo-code, C++, Python and Java, sorting and searching, asymptotic notation, analysis and recursive algorithms, with interactive graphs.

Teaching material by **[Fabio Furini](https://fabiofurini.github.io/)** —
[DIAG](https://www.diag.uniroma1.it/), Sapienza University of Rome.

*Questo sito è disponibile anche in [italiano](https://fabiofurini.github.io/programmazione-lineare-intera/).*
