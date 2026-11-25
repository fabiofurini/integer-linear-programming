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

Teaching material conceived and developed by **[Fabio Furini](https://fabiofurini.github.io/)**, associate
professor at [DIAG](https://www.diag.uniroma1.it/), Sapienza University of Rome.

The course presents integer and mixed integer linear programming: the LP relaxation and bounds, valid
inequalities and cutting planes, the branch-and-bound and branch-and-cut algorithms, MIP solvers and
heuristics.

Prerequisites: [Linear Programming](https://fabiofurini.github.io/linear-programming/).

[Start :material-arrow-right:](models-and-bounds/index.md){ .md-button .md-button--primary }
[:material-pencil: Exercises](exercises/index.md){ .md-button }
[:material-gesture-tap: Lab](lab/index.md){ .md-button }
[:material-clipboard-check-outline: Quiz](self-assessment.md){ .md-button }
[:material-download: Downloads](downloads.md){ .md-button }

</div>

<!-- collana-app -->
## Try it now

Branch-and-bound at the end: the complete tree, with the regions of the nodes, the pruned nodes and the incumbent. Each node solves an LP relaxation and splits the region on a fractional variable; press «Run» to replay it step by step.

<div class="gi" data-grafico="pli-bb" data-vetrina="A | auto"></div>

[:material-gesture-tap: More apps in the lab](lab/index.md){ .md-button }
<!-- /collana-app -->

## The parts of the course

<div class="grid cards" markdown>

-   :material-sigma: **I · Models and bounds**

    ---

    What an integer linear programming problem is (MIP, ILP, BIP), its solutions and outcomes; primal and dual bounds, the gap and the LP relaxation.

    [:octicons-arrow-right-24: The chapters](models-and-bounds/index.md)

-   :material-vector-polygon: **II · Strengthening the formulation**

    ---

    Ideal formulations, valid inequalities and cover inequalities: how to strengthen a formulation.

    [:octicons-arrow-right-24: The chapters](formulations/index.md)

-   :material-file-tree: **III · Exact methods**

    ---

    The cutting plane algorithm with Gomory fractional cuts, LP-based branch-and-bound and branch-and-cut. Examples worked out step by step and exercises with complete solutions.

    [:octicons-arrow-right-24: The chapters](exact-methods/index.md)

-   :material-cog-outline: **IV · MIP solvers**

    ---

    What a MIP solver does (presolve, cuts, primal heuristics, tree strategies, tolerances) and how to read its log, with real examples.

    [:octicons-arrow-right-24: The chapters](solvers/index.md)

-   :material-lightning-bolt: **V · MIP heuristics**

    ---

    Rounding, diving, feasibility pump, RINS, local branching, relax-and-fix and fix-and-optimize. Examples and exercises with complete solutions.

    [:octicons-arrow-right-24: The chapters](mip-heuristics/index.md)

-   :material-vector-polygon: **MIP Modelling**

    ---

    From the description of a problem to the integer linear programming model, solved with Python and Gurobi: modelling techniques and assignment, scheduling, location and production problems. It is a separate module of the series.

    [:octicons-arrow-right-24: The MIP Modelling module](https://fabiofurini.github.io/mip-modelling/)

-   :material-clipboard-check-outline: **Self-assessment**

    ---

    *Mock exam*

    A new mock exam at every start: 13 questions, 33 points, with the solutions and links to the notes.

    [:octicons-arrow-right-24: Test yourself](self-assessment.md)

</div>

<!-- collana-da-non-perdere -->
## Don't miss

<div class="grid cards" markdown>

-   :material-pencil-box-multiple: **Exercises with solutions**

    ---

    Exercises for every chapter, each with its worked solution that opens with a click.

    [:octicons-arrow-right-24: The exercises](exercises/index.md)

-   :material-clipboard-check-outline: **Self-assessment quiz**

    ---

    A new mock exam at every start: 13 questions, 33 points, with the solution and the link to the notes. Downloads also has the full question bank.

    [:octicons-arrow-right-24: Test yourself](self-assessment.md)

-   :material-gesture-tap: **Interactive lab**

    ---

    Apps running in the browser: move the parameters and watch the ideas of the course at work, one step at a time.

    [:octicons-arrow-right-24: The lab](lab/index.md)

-   :material-download: **Lecture notes in PDF**

    ---

    Lecture notes, exercise books and slides in PDF, with the same numbering as the website.

    [:octicons-arrow-right-24: Downloads](downloads.md)

-   :material-format-list-numbered: **Full index**

    ---

    All the chapters with their sections on one page, and how each chapter is organized.

    [:octicons-arrow-right-24: The index](indice.md)

</div>
<!-- /collana-da-non-perdere -->

---

<!-- collana -->
## The series

All the courses of the series are built the same way: lecture notes online and in PDF, exercises with solutions, an interactive lab and self-assessment quizzes.

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

-   :material-hexagon-multiple-outline: **[Combinatorial Optimization Problems](https://fabiofurini.github.io/combinatorial-optimization/)**

    knapsack, bin packing, coloring, TSP, scheduling and lot sizing: the problems, their formulations and ad hoc algorithms

-   :material-table-large: **[Dynamic Programming](https://fabiofurini.github.io/dynamic-programming/)**

    principles and DP algorithms for knapsack, lot sizing, coin change and rod cutting

-   :material-map-marker-path: **[Heuristic Algorithms](https://fabiofurini.github.io/heuristic-algorithms/)**

    constructive heuristics, local search, metaheuristics and MIP heuristics for combinatorial optimization problems

-   :material-approximately-equal: **[Approximation Algorithms](https://fabiofurini.github.io/approximation-algorithms/)**

    performance guarantees, approximation schemes and limits of approximation for combinatorial problems

-   :material-chart-line: **[LP Modelling](https://fabiofurini.github.io/lp-modelling/)**

    linear programming models of managerial problems, solved with Python and Gurobi

-   :material-chart-bell-curve-cumulative: **[NLP Modelling](https://fabiofurini.github.io/nlp-modelling/)**

    nonlinear programming models, from portfolios to machine learning, with Python and Gurobi

-   :material-vector-polygon: **[MIP Modelling](https://fabiofurini.github.io/mip-modelling/)**

    integer linear programming models, from the formulation to the solution with solvers

</div>
<!-- /collana -->

---

Teaching material by **[Fabio Furini](https://fabiofurini.github.io/)** —
[DIAG](https://www.diag.uniroma1.it/), Sapienza University of Rome.

*This website is also available in [Italian](https://fabiofurini.github.io/programmazione-lineare-intera/).*
