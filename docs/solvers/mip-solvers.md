---
title: "MIP solvers: what they do"
---

# MIP solvers: what they do

<div class="info-capitolo" markdown>

**Part 4 · MIP solvers · Chapter 9** · [:material-file-pdf-box: Lecture notes, volume 3 (PDF)](../pdf/lecture-notes-3-mip-solvers.pdf)

</div>

!!! nota "Prerequisites"

    - From this module, the chapters «LP-based branch-and-bound algorithms» and «Branch-and-cut algorithms»: the branching, bounding and pruning operations, the cuts, and the incumbent solution.

    - From this module, the chapter «LP relaxation and dual bounds»: the lower and upper bounds and the optimality gap.

    - From the module <em>Algorithms</em>: the knapsack problem (KP) and the bin packing problem (BPP) are used as examples.

!!! chiave "Objectives"

    At the end of this chapter you will be able to:

    - name the components of a MIP solver and say on which side of the bounds each of them acts;

    - explain presolve, cuts, primal heuristics, branching rules, tolerances and the gap;

    - read the log of Gurobi and follow the two bounds, the incumbent solution and the tree;

    - explain why a solver can stop with a solution that is not optimal.

## 9.1 From the branch-and-cut algorithm to a MIP solver

- A <strong>MIP solver</strong> (Gurobi, CPLEX, Xpress, HiGHS, SCIP) is a computer program that computes optimal solutions of MIP problems. Its core is the branch-and-cut algorithm of the previous chapter.

- Around the core, the solver adds several components. Each of them acts on one side of the bounds on $z(\ILP)$, here for a maximization problem:

    $$
    \underbrace{\LB}_{\text{primal side}} ~~\le~~ z(\ILP) ~~\le~~ \underbrace{\UB}_{\text{dual side}}
    $$

- The components are the same in all the solvers. The details (which cuts, which heuristics, which rules) differ from solver to solver and from version to version, and are only partially documented.

!!! chiave ""

    A MIP solver executes presolve, then the root node (LP relaxation, cuts, primal heuristics), then the branch-and-cut tree, and it stops when the gap between the bounds is below a tolerance or a limit is reached.

![Figure 1](../img/09-mip-solvers/fig01.svg){ .fig loading=lazy  }

### 9.1.1 Dictionary between the notes and the solver

<div class="tabella" markdown="0"><table>
<tr>
<th>In the notes (B&amp;B, B&amp;C)</th>
<th>In the Gurobi log</th>
</tr>
<tr>
<td>lower bound <span class="arithmatex">\(\LB\)</span> (max), incumbent solution <span class="arithmatex">\(\bar{\vet x}\)</span></td>
<td><code>Incumbent</code></td>
</tr>
<tr>
<td>upper bound <span class="arithmatex">\(\UB\)</span> (max)</td>
<td><code>BestBd</code> (best bound)</td>
</tr>
<tr>
<td>optimality gap</td>
<td><code>Gap</code></td>
</tr>
<tr>
<td>list <span class="arithmatex">\(L\)</span> of LP problems for branching</td>
<td><code>Unexpl</code> (unexplored nodes)</td>
</tr>
<tr>
<td>LP problems already processed</td>
<td><code>Expl</code> (explored nodes)</td>
</tr>
<tr>
<td><span class="arithmatex">\(z(\LP_k)\)</span> of the current branching node</td>
<td><code>Obj</code></td>
</tr>
<tr>
<td>level of the node in the tree</td>
<td><code>Depth</code></td>
</tr>
<tr>
<td>fractional integer variables in <span class="arithmatex">\(\tilde{\vet x}(\LP_k)\)</span></td>
<td><code>IntInf</code></td>
</tr>
</table></div>

- In a <u>minimization</u> problem, such as the BPP, the roles are exchanged: <code>Incumbent</code> is an upper bound and <code>BestBd</code> is a lower bound.

## 9.2 The components of a MIP solver

### 9.2.1 Presolve

- The <strong>presolve</strong> transforms the problem into an equivalent one: same optimal value, and an optimal solution of the original problem can be recovered from one of the new problem.

- The new problem is smaller and, possibly, has a stronger LP relaxation. The typical operations are three:

    1. removing <strong>redundant constraints</strong> and <strong>fixing variables</strong> whose value is implied;

    2. <strong>bound tightening</strong>: deriving smaller upper bounds and larger lower bounds on the variables;

    3. <strong>coefficient tightening</strong>: changing the coefficients of a constraint so that the integer solutions stay the same, while the LP relaxation becomes smaller.

<a id="box-1-ex-solver-presolve"></a>

!!! esempio "Example 9.1: coefficient tightening in the BPP model"

    Consider the BPP instance with bin capacity $c = 10$ and $n = 9$ items of weights

    $$
    w_1 = \dots = w_7 = 6, \qquad w_8 = w_9 = 3,
    $$

    and the ILP model of the BPP with $9$ bins ($9 \cdot 9 + 9 = 90$ binary variables, $18$ constraints). The capacity constraint of bin $b$ is

    $$
    6\,(x_{1b} + \dots + x_{7b}) + 3\,(x_{8b} + x_{9b}) \le 10\,y_b .
    $$

    - Two items of weight $6$ do not fit in the same bin ($6 + 6 > 10$), so $z(\ILP) = 7$. The LP relaxation has optimal value $\frac{7 \cdot 6 + 2 \cdot 3}{10} = \frac{24}{5}$.

    - The presolve of Gurobi replaces the capacity constraint of every bin with

        $$
        7\,(x_{1b} + \dots + x_{7b}) + 3\,(x_{8b} + x_{9b}) \le 10\,y_b .
        $$

    - The integer solutions are the same. If $y_b = 0$, all the $x_{jb}$ are $0$ in both constraints. If $y_b = 1$, a bin contains at most one item of weight $6$ and at most one item of weight $3$ ($7 + 3 = 10$), or no item of weight $6$ and at most two items of weight $3$ ($6 \le 10$), in both constraints.

    - The LP relaxation of the new model has optimal value $\frac{7 \cdot 7 + 2 \cdot 3}{10} = \frac{11}{2}$: the lower bound grows from $\frac{24}{5}$ to $\frac{11}{2}$ without changing the size of the model.

- The same reasoning can be applied at every branching node (<strong>node presolve</strong>, or <strong>domain propagation</strong>): a branching constraint $x_j \le 0$ may imply that other variables can be fixed.

### 9.2.2 Cutting planes

- At the root node the solver executes several <strong>rounds of cuts</strong>. At the end, the log reports how many cuts of each family have been added.

- The families that appear in our logs are <code>Gomory</code> (Gomory cuts), <code>Cover</code> (cover inequalities) and <code>Zero half</code> (cuts with multipliers $0$ and $\frac12$, a family of Chvátal–Gomory cuts).

- Cuts are generated also in the tree, less often than at the root (branch-and-cut).

### 9.2.3 Primal heuristics

- <strong>Primal heuristics</strong> search for good integer feasible solutions: they give the incumbent solution and the bound $\LB$ (maximization). Examples are rounding the LP solution, <strong>diving</strong>, and local search around the incumbent solution (chapter «MIP heuristics»).

- In the log, a line starting with <code>H</code> reports a new incumbent solution found by a heuristic. A line starting with <code>\*</code> reports one found as the integer optimal solution of a node LP problem (pruning by integrality).

- The line <code>Found heuristic solution</code> reports a solution found before the root LP.

### 9.2.4 Branching-variable and branching-node selection

- The branching-variable strategies of the B&amp;B chapter (maximum infeasibility, strong branching, pseudocosts) are all available in a solver. Strong branching gives small trees but is expensive; pseudocosts are cheap but unreliable at the beginning.

- Solvers combine them in <strong>reliability branching</strong>: strong branching on a variable until its pseudocosts have been observed enough times.

- In Gurobi the parameter <code>VarBranch</code> selects the strategy: $-1$ automatic (default), $0$ pseudo reduced-cost, $1$ pseudo shadow-price, $2$ maximum infeasibility, $3$ strong branching.

- The node selection combines <strong>best bound</strong> (select the node with the largest $z(\LP_k)$, which decreases $\UB$) and <strong>depth first</strong> (which finds integer solutions quickly).

### 9.2.5 Tolerances and gap

- A MIP solver works with <strong>floating-point arithmetic</strong>: a value is integer, a constraint is satisfied, a reduced cost is non-positive, up to a <strong>tolerance</strong>. The default values in Gurobi 13 are:

<div class="tabella" markdown="0"><table>
<tr>
<th>Parameter</th>
<th>Default</th>
<th>Meaning</th>
</tr>
<tr>
<td><code>IntFeasTol</code></td>
<td><span class="arithmatex">\(10^{-5}\)</span></td>
<td>a variable is integer if it is within <span class="arithmatex">\(10^{-5}\)</span> of an integer</td>
</tr>
<tr>
<td><code>FeasibilityTol</code></td>
<td><span class="arithmatex">\(10^{-6}\)</span></td>
<td>maximum violation of a constraint</td>
</tr>
<tr>
<td><code>OptimalityTol</code></td>
<td><span class="arithmatex">\(10^{-6}\)</span></td>
<td>tolerance on the reduced costs (LP optimality)</td>
</tr>
<tr>
<td><code>MIPGap</code></td>
<td><span class="arithmatex">\(10^{-4}\)</span></td>
<td>relative gap at which the solver stops</td>
</tr>
<tr>
<td><code>MIPGapAbs</code></td>
<td><span class="arithmatex">\(10^{-10}\)</span></td>
<td>absolute gap at which the solver stops</td>
</tr>
</table></div>

- Gurobi computes the <strong>relative gap</strong> as

    $$
    \text{Gap} = \frac{|\,\texttt{BestBd} - \texttt{Incumbent}\,|}{|\,\texttt{Incumbent}\,|},
    $$

    which, for a maximization problem, is the optimality gap $\frac{\UB - \LB}{|\LB|}$ of the notes, with $\UB$ not rounded down.

!!! chiave ""

    “<code>Optimal solution found (tolerance 1.00e-04)</code>” means that the incumbent solution is optimal <strong>up to a relative gap of $10^{-4}$</strong>. With a larger <code>MIPGap</code> the solver stops with a solution that may <strong>not</strong> be optimal, and it writes the same sentence.

## 9.3 How to read the log of Gurobi

- All the logs are produced by Gurobi 13.0.3 (<code>gurobipy</code>) with <code>Threads</code> $=1$ and <code>Seed</code> $=0$, so that the execution is deterministic. With other versions or computers the numbers of nodes and iterations can differ.

- The numbers on the left are line numbers added for the comments; the lines <code>Set parameter ...</code> are omitted.

### 9.3.1 A knapsack instance solved by the presolve

<a id="box-2-ex-solver-kp"></a>

!!! esempio "Example 9.2: a KP instance"

    Capacity $c = 9$ and $n = 6$ items:

    <div class="tabella" markdown="0"><table>
    <tr>
    <th></th>
    <th><span class="arithmatex">\(j = 1\)</span></th>
    <th><span class="arithmatex">\(j = 2\)</span></th>
    <th><span class="arithmatex">\(j = 3\)</span></th>
    <th><span class="arithmatex">\(j = 4\)</span></th>
    <th><span class="arithmatex">\(j = 5\)</span></th>
    <th><span class="arithmatex">\(j = 6\)</span></th>
    </tr>
    <tr>
    <td><span class="arithmatex">\(p_j\)</span></td>
    <td><span class="arithmatex">\(6\)</span></td>
    <td><span class="arithmatex">\(5\)</span></td>
    <td><span class="arithmatex">\(8\)</span></td>
    <td><span class="arithmatex">\(9\)</span></td>
    <td><span class="arithmatex">\(6\)</span></td>
    <td><span class="arithmatex">\(7\)</span></td>
    </tr>
    <tr>
    <td><span class="arithmatex">\(w_j\)</span></td>
    <td><span class="arithmatex">\(2\)</span></td>
    <td><span class="arithmatex">\(3\)</span></td>
    <td><span class="arithmatex">\(6\)</span></td>
    <td><span class="arithmatex">\(7\)</span></td>
    <td><span class="arithmatex">\(5\)</span></td>
    <td><span class="arithmatex">\(9\)</span></td>
    </tr>
    </table></div>

    The model has $6$ binary variables and $1$ constraint. The LP relaxation has optimal value $z(\LP) = 6 + 5 + \frac{4}{6} \cdot 8 = \frac{49}{3}$; the optimal solution is $\{1, 4\}$, of value $15$.

??? codice "Gurobi log: KP instance, default parameters"

    ```python
    01| Gurobi Optimizer version 13.0.3 build v13.0.3rc0 (linux64 - "Ubuntu 26.04 LTS")
    02| 
    03| CPU model: Intel(R) Core(TM) i5-14500T, instruction set [SSE2|AVX|AVX2]
    04| Thread count: 20 physical cores, 20 logical processors, using up to 1 threads
    05| 
    06| Non-default parameters:
    07| LogToConsole  0
    08| Threads  1
    09| 
    10| Optimize a model with 1 rows, 6 columns and 6 nonzeros (Max)
    11| Model fingerprint: 0xb4d2031c
    12| Model has 6 linear objective coefficients
    13| Variable types: 0 continuous, 6 integer (6 binary)
    14| Coefficient statistics:
    15|   Matrix range     [2e+00, 9e+00]
    16|   Objective range  [5e+00, 9e+00]
    17|   Bounds range     [1e+00, 1e+00]
    18|   RHS range        [9e+00, 9e+00]
    19| 
    20| Found heuristic solution: objective 11.0000000
    21| Presolve removed 1 rows and 6 columns
    22| Presolve time: 0.00s
    23| Presolve: All rows and columns removed
    24| 
    25| Explored 0 nodes (0 simplex iterations) in 0.00 seconds (0.00 work units)
    26| Thread count was 1 (of 20 available processors)
    27| 
    28| Solution count 2: 15 11
    29| 
    30| Optimal solution found (tolerance 1.00e-04)
    31| Best objective 1.500000000000e+01, best bound 1.500000000000e+01, gap 0.0000
    ```

- **01–04** Version of Gurobi, computer, threads used ($1$, since <code>Threads</code> $=1$).

- **06–08** Parameters with non-default values; <code>Seed</code> $=0$ is not listed, since $0$ is its default.

- **10–13** Size of the model: $1$ row, $6$ columns, $6$ non-zeros, <code>(Max)</code>; the variable types ($6$ binary).

- **14–18** <strong>Coefficient statistics</strong>: the smallest and the largest absolute values of the coefficients. Ranges spanning many orders of magnitude signal numerical difficulties; here all values are between $2$ and $9$.

- **20** A heuristic finds a solution of value $11$ before the presolve, so $\LB = 11$.

- **21–23** The presolve removes all rows and columns: the problem is solved without LP relaxation and without branching.

- **25** $0$ nodes, $0$ simplex iterations.

- **28–31** Two solutions found, of values $15$ and $11$; the best has value $15$, the best bound is $15$ and the gap is $0$: the solution is optimal.

### 9.3.2 A bin packing instance solved at the root node

??? codice "Gurobi log: BPP instance of the presolve example, default parameters"

    ```python
    01| Gurobi Optimizer version 13.0.3 build v13.0.3rc0 (linux64 - "Ubuntu 26.04 LTS")
    02| 
    03| CPU model: Intel(R) Core(TM) i5-14500T, instruction set [SSE2|AVX|AVX2]
    04| Thread count: 20 physical cores, 20 logical processors, using up to 1 threads
    05| 
    06| Non-default parameters:
    07| LogToConsole  0
    08| Threads  1
    09| 
    10| Optimize a model with 18 rows, 90 columns and 171 nonzeros (Min)
    11| Model fingerprint: 0x05ef7999
    12| Model has 9 linear objective coefficients
    13| Variable types: 0 continuous, 90 integer (90 binary)
    14| Coefficient statistics:
    15|   Matrix range     [1e+00, 1e+01]
    16|   Objective range  [1e+00, 1e+00]
    17|   Bounds range     [1e+00, 1e+00]
    18|   RHS range        [1e+00, 1e+00]
    19| 
    20| Found heuristic solution: objective 7.0000000
    21| Presolve time: 0.00s
    22| Presolved: 18 rows, 90 columns, 171 nonzeros
    23| Variable types: 0 continuous, 90 integer (90 binary)
    24| 
    25| Root relaxation: objective 5.500000e+00, 28 iterations, 0.00 seconds (0.00 work units)
    26| 
    27|     Nodes    |    Current Node    |     Objective Bounds      |     Work
    28|  Expl Unexpl |  Obj  Depth IntInf | Incumbent    BestBd   Gap | It/Node Time
    29| 
    30|      0     0    5.50000    0    7    7.00000    5.50000  21.4
    31|      0     0    5.50000    0    9    7.00000    5.50000  21.4
    32|      0     0 infeasible    0         7.00000    7.00000  0.00
    33| 
    34| Cutting planes:
    35|   Gomory: 2
    36|   Cover: 4
    37|   Zero half: 1
    38| 
    39| Explored 1 nodes (56 simplex iterations) in 0.00 seconds (0.00 work units)
    40| Thread count was 1 (of 20 available processors)
    41| 
    42| Solution count 1: 7
    43| 
    44| Optimal solution found (tolerance 1.00e-04)
    45| Best objective 7.000000000000e+00, best bound 7.000000000000e+00, gap 0.0000
    ```

- **10–13** $18$ rows, $90$ columns, $171$ non-zeros; <code>(Min)</code>: minimization.

- **20** A heuristic finds a solution with $7$ bins. In a minimization problem $7$ is an <strong>upper bound</strong>.

- **21–23** The presolved model has the same size, but the coefficients $6$ have become $7$ ([Example 9.1](#box-1-ex-solver-presolve)).

- **25** <strong>Root relaxation</strong>: the value is $\frac{11}{2}$ (lower bound), after $28$ simplex iterations.

- **27–28** Header of the <strong>node log</strong>: <code>Expl</code> and <code>Unexpl</code> are the explored and unexplored nodes; <code>Obj</code>, <code>Depth</code>, <code>IntInf</code> describe the current node; <code>Incumbent</code>, <code>BestBd</code>, <code>Gap</code> are the two bounds and the gap.

- **30** Root node: $7$ integer variables are fractional, and the gap is $\frac{7 - 5.5}{7} \approx 21.4\%$.

- **31** After a round of cuts the LP value is still $\frac{11}{2}$, with $9$ fractional variables.

- **32** After more cuts the LP becomes <code>infeasible</code>: no solution with fewer than $7$ bins exists. The best bound jumps to $7$, the gap to $0$, and the root is pruned.

- **34–37** Cuts added: $2$ Gomory, $4$ cover, $1$ zero-half.

- **39–45** $1$ explored node, $56$ simplex iterations; one solution, with $7$ bins, optimal.

!!! chiave ""

    Here the heuristic finds the optimal solution at once. All the remaining work, presolve and cuts, serves to <strong>prove</strong> optimality, by raising the lower bound from $\frac{24}{5}$ to $7$.

### 9.3.3 The same instance without presolve, cuts and heuristics

- With <code>Presolve</code> $=0$, <code>Cuts</code> $=0$ and <code>Heuristics</code> $=0$ the solver is close to the LP-based B&amp;B algorithm of the notes, and we see the tree.

??? codice "Gurobi log: BPP instance, <code>Presolve=0</code>, <code>Cuts=0</code>, <code>Heuristics=0</code>"

    ```python
    01| Gurobi Optimizer version 13.0.3 build v13.0.3rc0 (linux64 - "Ubuntu 26.04 LTS")
    02| 
    03| CPU model: Intel(R) Core(TM) i5-14500T, instruction set [SSE2|AVX|AVX2]
    04| Thread count: 20 physical cores, 20 logical processors, using up to 1 threads
    05| 
    06| Non-default parameters:
    07| Heuristics  0
    08| Cuts  0
    09| LogToConsole  0
    10| Presolve  0
    11| Threads  1
    12| 
    13| Optimize a model with 18 rows, 90 columns and 171 nonzeros (Min)
    14| Model fingerprint: 0x05ef7999
    15| Model has 9 linear objective coefficients
    16| Variable types: 0 continuous, 90 integer (90 binary)
    17| Coefficient statistics:
    18|   Matrix range     [1e+00, 1e+01]
    19|   Objective range  [1e+00, 1e+00]
    20|   Bounds range     [1e+00, 1e+00]
    21|   RHS range        [1e+00, 1e+00]
    22| 
    23| Variable types: 0 continuous, 90 integer (90 binary)
    24| 
    25| Root relaxation: objective 4.800000e+00, 31 iterations, 0.00 seconds (0.00 work units)
    26| 
    27|     Nodes    |    Current Node    |     Objective Bounds      |     Work
    28|  Expl Unexpl |  Obj  Depth IntInf | Incumbent    BestBd   Gap | It/Node Time
    29| 
    30|      0     0    4.80000    0    8          -    4.80000      -     -    0s
    31|      0     0    4.80000    0    8          -    4.80000      -     -    0s
    32|      0     2    4.80000    0    8          -    4.80000      -     -    0s
    33| *   38    36              38       7.0000000    4.80000  31.4
    34| 
    35| Explored 1211 nodes (3669 simplex iterations) in 0.04 seconds (0.03 work units)
    36| Thread count was 1 (of 20 available processors)
    37| 
    38| Solution count 1: 7
    39| 
    40| Optimal solution found (tolerance 1.00e-04)
    41| Best objective 7.000000000000e+00, best bound 7.000000000000e+00, gap 0.0000
    ```

- **25** Without presolve the root relaxation has value $\frac{24}{5}$, the value of the original model.

- **30–32** No incumbent solution yet (<code>-</code>). At line $32$ the root is branched and $2$ children wait in the list.

- **33** After $38$ nodes a node LP, at depth $38$, has an integer optimal solution with $7$ bins (<code>\*</code>). The gap is $\frac{7 - 4.8}{7} \approx 31.4\%$.

- **35** To prove that $7$ is optimal the solver explores $1211$ nodes ($3669$ iterations), against $1$ node with the default parameters.

### 9.3.4 The gap as a stopping criterion

- For a KP with $n = 30$ items (strongly correlated: $w_j \in \{10, \dots, 100\}$, $p_j = w_j + 10$, $c = 846$), the default run is solved by the presolve, with $z(\ILP) = 1036$. We switch off the three components and set <code>MIPGap</code> $= 0.01$:

??? codice "Gurobi log: KP with $n=30$, <code>Presolve=0</code>, <code>Cuts=0</code>, <code>Heuristics=0</code>, <code>MIPGap=0.01</code>"

    ```python
    01| Gurobi Optimizer version 13.0.3 build v13.0.3rc0 (linux64 - "Ubuntu 26.04 LTS")
    02| 
    03| CPU model: Intel(R) Core(TM) i5-14500T, instruction set [SSE2|AVX|AVX2]
    04| Thread count: 20 physical cores, 20 logical processors, using up to 1 threads
    05| 
    06| Non-default parameters:
    07| MIPGap  0.01
    08| Heuristics  0
    09| Cuts  0
    10| LogToConsole  0
    11| Presolve  0
    12| Threads  1
    13| 
    14| Optimize a model with 1 rows, 30 columns and 30 nonzeros (Max)
    15| Model fingerprint: 0x713eb478
    16| Model has 30 linear objective coefficients
    17| Variable types: 0 continuous, 30 integer (30 binary)
    18| Coefficient statistics:
    19|   Matrix range     [2e+01, 1e+02]
    20|   Objective range  [2e+01, 1e+02]
    21|   Bounds range     [1e+00, 1e+00]
    22|   RHS range        [8e+02, 8e+02]
    23| 
    24| Variable types: 0 continuous, 30 integer (30 binary)
    25| 
    26| Root relaxation: objective 1.044592e+03, 1 iterations, 0.00 seconds (0.00 work units)
    27| 
    28|     Nodes    |    Current Node    |     Objective Bounds      |     Work
    29|  Expl Unexpl |  Obj  Depth IntInf | Incumbent    BestBd   Gap | It/Node Time
    30| 
    31|      0     0 1044.59155    0    1          - 1044.59155      -     -    0s
    32|      0     0 1044.57143    0    1          - 1044.57143      -     -    0s
    33|      0     2 1044.57143    0    1          - 1044.57143      -     -    0s
    34| *   21    20              20    1032.0000000 1044.41270  1.20
    35| *   61    39              20    1033.0000000 1044.24324  1.09
    36| *   84    53              22    1035.0000000 1044.00000  0.87
    37| 
    38| Explored 85 nodes (83 simplex iterations) in 0.00 seconds (0.00 work units)
    39| Thread count was 1 (of 20 available processors)
    40| 
    41| Solution count 3: 1035 1033 1032
    42| 
    43| Optimal solution found (tolerance 1.00e-02)
    44| Best objective 1.035000000000e+03, best bound 1.044000000000e+03, gap 0.8696
    ```

- **26** The root relaxation needs $1$ simplex iteration: as in the Dantzig bound, only the split item is fractional (<code>IntInf</code> $=1$).

- **34–36** Three incumbent solutions, all integer node LP solutions (<code>\*</code>): $1032$, $1033$, $1035$. The best bound decreases from $1044.41270$ to $1044$ and the gap from $1.20\%$ to $0.87\%$.

- **38–44** The solver stops after $85$ nodes, since $\frac{1044 - 1035}{1035} \approx 0.87\% < 1\%$, and it writes “<code>Optimal solution found (tolerance 1.00e-02)</code>” for the value $1035$.

- The optimal value is $1036$: the bound $1044$ certifies only that $1035$ is within $0.87\%$ of the optimum. With the default <code>MIPGap</code>, the same run explores $3339$ nodes to close the gap.

### 9.3.5 Branching-variable selection

- With presolve, cuts and heuristics off, we change only <code>VarBranch</code>. The table gives the explored nodes (simplex iterations in parentheses).

<div class="tabella" markdown="0"><table>
<tr>
<th><code>VarBranch</code></th>
<th>KP, <span class="arithmatex">\(n = 30\)</span></th>
<th>BPP, <span class="arithmatex">\(n = 9\)</span></th>
</tr>
<tr>
<td><span class="arithmatex">\(-1\)</span> automatic</td>
<td><span class="arithmatex">\(3339\)</span> (<span class="arithmatex">\(3388\)</span>)</td>
<td><span class="arithmatex">\(1211\)</span> (<span class="arithmatex">\(3669\)</span>)</td>
</tr>
<tr>
<td><span class="arithmatex">\(0\)</span> pseudo reduced cost</td>
<td><span class="arithmatex">\(3339\)</span> (<span class="arithmatex">\(3388\)</span>)</td>
<td><span class="arithmatex">\(1499\)</span> (<span class="arithmatex">\(3877\)</span>)</td>
</tr>
<tr>
<td><span class="arithmatex">\(1\)</span> pseudo shadow price</td>
<td><span class="arithmatex">\(3346\)</span> (<span class="arithmatex">\(3392\)</span>)</td>
<td><span class="arithmatex">\(5009\)</span> (<span class="arithmatex">\(14846\)</span>)</td>
</tr>
<tr>
<td><span class="arithmatex">\(2\)</span> maximum infeasibility</td>
<td><span class="arithmatex">\(3341\)</span> (<span class="arithmatex">\(3390\)</span>)</td>
<td><span class="arithmatex">\(69404\)</span> (<span class="arithmatex">\(116946\)</span>)</td>
</tr>
<tr>
<td><span class="arithmatex">\(3\)</span> strong branching</td>
<td><span class="arithmatex">\(3341\)</span> (<span class="arithmatex">\(3390\)</span>)</td>
<td><span class="arithmatex">\(61192\)</span> (<span class="arithmatex">\(105887\)</span>)</td>
</tr>
</table></div>

- For the KP the choice is almost irrelevant: every node LP solution has at most one fractional variable, the split item, so there is nothing to choose.

- For the BPP the number of nodes changes by a factor larger than $50$. The model is highly <strong>symmetric</strong>: the $9$ bins are identical, so every solution appears many times with the bins renamed.

- On such models the behaviour of the rules is hard to predict; here the automatic choice is the best.

!!! chiave ""

    Reading a log means following the two bounds: the <code>Incumbent</code> column is the primal side (heuristics and integer nodes), the <code>BestBd</code> column the dual side (relaxation, presolve, cuts, branching).

## 9.4 Typical mistakes

!!! attenzione "Warning"

    - <strong>Reading “Optimal solution found” as a proof of optimality.</strong> The sentence reports the tolerance in use: with <code>MIPGap</code> $= 0.01$ the solution can be $1\%$ away from the optimum.

    - <strong>Swapping the bounds in a minimization problem.</strong> For the BPP, <code>Incumbent</code> is the upper bound and <code>BestBd</code> the lower bound.

    - <strong>Believing that the tree explains everything.</strong> With the default parameters, most of the work of the solver is outside the tree: in our logs the presolve alone solves the KP.

    - <strong>Comparing node counts across runs.</strong> The numbers depend on the version of the solver, on the computer and on the seed; with other parameters the same instance can need $1$ or $61192$ nodes.

    - <strong>Expecting the solver to follow the rules of the notes.</strong> The choices of the solver (cuts, heuristics, node selection) are not documented in detail, and they change with the version.

## 9.5 In summary

!!! chiave "In summary"

    - A MIP solver is a branch-and-cut algorithm with presolve, cuts, primal heuristics, branching and node-selection rules around it.

    - Presolve and cuts raise the lower bound (minimization), heuristics find the incumbent solution, branching closes the gap.

    - The log shows the bounds: <code>Incumbent</code> is the primal side, <code>BestBd</code> the dual side, <code>Gap</code> their relative distance.

    - The solver stops when the gap is below <code>MIPGap</code>: the solution is optimal only up to that tolerance.

    - Switching off the components shows how much each of them saves on a given instance.

    <strong>Dashboard of the BPP instance.</strong> Lower and upper bounds on $z(\ILP) = 7$ in the logs of this chapter:

    <div class="tabella" markdown="0"><table>
    <tr>
    <th>Stage</th>
    <th>Lower bound</th>
    <th>Upper bound</th>
    </tr>
    <tr>
    <td>LP relaxation of the original model</td>
    <td><span class="arithmatex">\(\frac{24}{5}\)</span></td>
    <td>—</td>
    </tr>
    <tr>
    <td>Heuristic solution</td>
    <td><span class="arithmatex">\(\frac{24}{5}\)</span></td>
    <td><span class="arithmatex">\(7\)</span></td>
    </tr>
    <tr>
    <td>LP relaxation after presolve</td>
    <td><span class="arithmatex">\(\frac{11}{2}\)</span></td>
    <td><span class="arithmatex">\(7\)</span></td>
    </tr>
    <tr>
    <td>Root node, after the cuts</td>
    <td><span class="arithmatex">\(7\)</span></td>
    <td><span class="arithmatex">\(7\)</span></td>
    </tr>
    </table></div>
