---
title: Integer Linear Programming
hide:
  - navigation
  - toc
---

<div class="hero" markdown>

# Integer Linear Programming

<!-- collana-riga -->
<p class="collana-riga" markdown>A course of the <b>Operations Research and Mathematics</b> series · [all the courses](#the-series)</p>
<!-- /collana-riga -->

Teaching material by **[Fabio Furini](https://fabiofurini.github.io/)**, associate professor at [DIAG](https://www.diag.uniroma1.it/), Sapienza University of Rome.

The course covers integer and mixed integer programming models, LP relaxations, ideal formulations, valid inequalities, cutting planes, branch-and-bound, branch-and-cut, MIP solvers and heuristics. It includes definitions, proofs, worked examples and exercises with solutions.

[Start :material-arrow-right:](models-and-bounds/index.md){ .md-button .md-button--primary }
[:material-pencil: Exercises](exercises/index.md){ .md-button }
[:material-gesture-tap: Lab](lab/index.md){ .md-button }
[:material-clipboard-check-outline: Quiz](self-assessment.md){ .md-button }
[:material-download: Downloads](downloads.md){ .md-button }

</div>

<!-- collana-app -->
## Try it now

Branch-and-bound step by step: each node solves an LP relaxation and splits the region on a fractional variable.

<div class="gi" data-grafico="pli-bb"></div>

[:material-gesture-tap: More apps in the lab](lab/index.md){ .md-button }
<!-- /collana-app -->

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

-   :material-clipboard-check-outline: **Self-assessment**

    ---

    *Mock exam*

    A new mock exam at every start: 13 questions, 33 points, with the solutions and links to the notes.

    [:octicons-arrow-right-24: Test yourself](self-assessment.md)

</div>

---

<!-- collana -->
## The series

Ten courses built the same way: lecture notes online and in PDF, exercises with solutions, an interactive lab and self-assessment quizzes.

<div class="grid cards" markdown>

-   :material-function-variant: **[Mathematical Analysis 1](https://fabiofurini.github.io/mathematical-analysis-1/)**

    real numbers, functions, limits, derivatives and series, with interactive graphs

-   :material-matrix: **[Linear Algebra](https://fabiofurini.github.io/linear-algebra/)**

    vectors, matrices, determinants, linear systems and eigenvalues, with the computation lab

-   :material-code-braces: **[Algorithms](https://fabiofurini.github.io/algorithms/)**

    number representation, pseudo-code, sorting and searching, analysis and recursive algorithms

-   :material-vector-triangle: **[Linear Programming](https://fabiofurini.github.io/linear-programming/)**

    polyhedra, the simplex algorithm, duality and the dual simplex, with the interactive lab

-   :material-dots-grid: **Integer Linear Programming** · *this course*

    LP relaxation, valid inequalities, cutting planes, branch-and-bound and branch-and-cut

-   :material-vector-curve: **[Convex Optimization](https://fabiofurini.github.io/convex-optimization/)**

    convex sets and functions, convex optimization problems, with interactive graphs

-   :material-graph-outline: **[Network Optimization](https://fabiofurini.github.io/network-optimization/)**

    graphs, spanning trees, shortest paths, network flows, the network simplex and project management

-   :material-hexagon-multiple-outline: **[Combinatorial Optimization](https://fabiofurini.github.io/combinatorial-optimization/)**

    knapsack, bin packing, coloring, TSP, scheduling and lot sizing, solved in every way

-   :material-vector-polygon: **[MIP Modelling](https://fabiofurini.github.io/mip-modelling/)**

    integer linear programming models, from the formulation to the solution with solvers

-   :material-chart-timeline-variant: **[Operations Research Lab](https://fabiofurini.github.io/operations-research-lab/)**

    modelling and solving optimization problems with Python and solvers

</div>
<!-- /collana -->

Teaching material by **[Fabio Furini](https://fabiofurini.github.io/)** —
[DIAG](https://www.diag.uniroma1.it/), Sapienza University of Rome.

*This website is also available in [Italian](https://fabiofurini.github.io/programmazione-lineare-intera/).*
