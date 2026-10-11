---
title: "MIP heuristics"
---

# MIP heuristics

<div class="info-capitolo" markdown>

**Part 5 · MIP heuristics · Chapter 10** · [:material-file-pdf-box: Lecture notes, volume 4 (PDF)](../pdf/lecture-notes-4-mip-heuristics.pdf) · [:material-pencil: Exercises](../exercises/mip-heuristics.md)

</div>

<span class="arithmatex">\(\newcommand{\rnd}[1]{\left[\,#1\,\right]}\)</span>

!!! nota "Prerequisites"

    - From this module, the chapters «Primal and dual bounds» and «LP-based branch-and-bound algorithms»: the LP relaxation, the lower and upper bounds, the optimality gap, and the branching operations.

    - From this module, the chapter «MIP solvers: what they do»: the incumbent solution, the sub-problems solved by a solver, and the heuristics inside it.

    - From the module <em>Linear Programming</em>: the [dual simplex algorithm (Chapter 10)](https://fabiofurini.github.io/linear-programming/duality/10-dual-simplex/), which re-optimizes an LP after a change of a bound.

!!! chiave "Objectives"

    At the end of this chapter you will be able to:

    - explain how a heuristic algorithm uses the LP relaxation and a MIP solver;

    - execute by hand rounding, diving, the feasibility pump, RINS, local branching and proximity search on a small MIP;

    - execute relax-and-fix and fix-and-optimize on a MIP whose variables are split into blocks;

    - decide which method to use when a feasible solution is missing, or when an incumbent solution must be improved;

    - compute the gap of a heuristic solution when a dual bound is available.

## 10.1 MIP-based heuristics { #sez-sec-intro }

<a id="box-1-def-mip"></a>

!!! definizione "Definition 10.1: MIP in general form"

    A <strong>MIP</strong> with $N$ variables $\vet x=(x_1,x_2,\dots,x_N)$ reads

    <a id="eq-eq-mip"></a>

    \begin{align*}
    && \min ~~ f(\vet x) \label{eq:mip-obj} \tag{10.1a}\\[1ex]
    && \text{subject to} \quad A\,\vet x &\le b, & \label{eq:mip-cons} \tag{10.1b}\\[1ex]
    && l_j \le x_j &\le u_j, & \forall j \in \{1,2,\dots,N\}, \label{eq:mip-bounds} \tag{10.1c}\\[1ex]
    && x_j &\in \Z, & \forall j \in \mathscr{D}, \label{eq:mip-int} \tag{10.1d}
    \end{align*}

    where $f$ is a linear function and $\mathscr{D}\subseteq\{1,2,\dots,N\}$ is the set of the indices of the <strong>integer variables</strong>. The feasible region of the <strong>LP relaxation</strong> is

    $$
    \mathcal{L} = \big\{\vet x\in\R^N : A\,\vet x\le b,~ l\le \vet x\le u\big\},
    $$

    and $z(\LP)=\min\{f(\vet x): \vet x\in\mathcal{L}\}$ is the value of the LP relaxation.

- A maximization problem, such as the car example, is handled in the same way, exchanging $\min$ and $\max$, $\le$ and $\ge$ in the comparisons of objective values.

<a id="box-2-def-mipheur"></a>

!!! definizione "Definition 10.2: MIP-based heuristic"

    A <strong>heuristic algorithm</strong> returns a feasible solution of a MIP problem [(10.1)](#eq-eq-mip) without any guarantee of optimality. It is <strong>MIP-based</strong> (a <strong>matheuristic</strong>) if it uses the LP relaxation and a MIP solver to search for the solution.

- A heuristic gives a primal bound (an upper bound for a minimization problem, a lower bound for a maximization problem), never a dual bound. The branch-and-bound algorithm of this module finds the incumbent solution only by chance, as a leaf of the tree.

- The optimal solution of the <strong>LP relaxation</strong> tells <em>where to look</em>: it is the solution of a simpler problem, often close to the good solutions of the original one.

- The <strong>MIP solver</strong> explores <em>exactly</em> a set of solutions far too large to be enumerated, provided that the set is described by linear constraints.

<a id="box-3-def-incumbent"></a>

!!! definizione "Definition 10.3: incumbent, rounding and completion"

    - A feasible solution $\bar{\vet x}$ of the MIP [(10.1)](#eq-eq-mip), the best one known, is the <strong>incumbent</strong>, and $f(\bar{\vet x})$ is its value.

    - The <strong>rounding</strong> of a real number $v$ to the nearest integer is $\rnd{v} = \big\lfloor v + \frac12 \big\rfloor$, so that a value $\frac12$ is rounded up.

    - A point $\bar{\vet x}$ with $\bar x_j \in \Z$ for all $j \in \mathscr{D}$ is <strong>completed</strong> by fixing the integer variables to the values $\bar x_j$ and solving the LP on the continuous variables.

- Throughout the chapter, $\tilde{\vet x}$ denotes an optimal solution of an LP relaxation, and $\bar{\vet x}$ a point whose components $\bar x_j$, $j\in\mathscr{D}$, are integer.

- If the LP of the completion has an optimal solution, $\bar{\vet x}$ is turned into a feasible solution of the MIP; if it is infeasible, so is $\bar{\vet x}$. When all the variables are integer, the completion simply checks the constraints.

- The methods are built on five ideas, one or two per section.

<div class="tabella" markdown="0"><table>
<tr>
<th>idea</th>
<th>methods</th>
</tr>
<tr>
<td>round the relaxation</td>
<td>rounding; diving (fix one variable, re-solve, repeat)</td>
</tr>
<tr>
<td>chase feasibility</td>
<td>the feasibility pump</td>
</tr>
<tr>
<td>fix where relaxation and incumbent agree</td>
<td>RINS</td>
</tr>
<tr>
<td>bound the distance from the incumbent</td>
<td>local branching; proximity search</td>
</tr>
<tr>
<td>decompose into blocks of variables</td>
<td>relax-and-fix; fix-and-optimize</td>
</tr>
</table></div>

- The examples are solved with the MIP solver Gurobi, used from Python through <code>gurobipy</code>; the module <em>MIP Modelling</em> ([<u>fabiofurini.github.io/mip-modelling</u>](https://fabiofurini.github.io/mip-modelling/), [chapter 3 «From the model to Python/Gurobi»](https://fabiofurini.github.io/mip-modelling/modelling-3/)) explains how to use it. When an LP has several optimal solutions, the text reports the one returned by the solver; all the codes are in the last section.

## 10.2 The examples of the chapter { #sez-sec-models }

- The methods are illustrated on the <strong>car example</strong> of this module and on four small problems of the <strong>catalogue of MIP problems</strong>. All of them are ILP problems in canonical maximization form, with integer data and $x_j\in\Z_{\ge 0}$ for every $j$, so that $\mathscr{D}=\{1,2,\dots,N\}$.

- The <strong>car example</strong> reads

    $$
    \max~ 3\,x_1+2\,x_2 \quad\text{s.t.}\quad 10\,x_1+4\,x_2\le 45,\quad 4\,x_1+14\,x_2\le 49,\quad x_1,x_2\in\Z_{\ge 0}
    $$

<div class="tabella" markdown="0"><table>
<tr>
<th>problem</th>
<th>objective <span class="arithmatex">\(\max\)</span></th>
<th>constraints</th>
</tr>
<tr>
<td><span class="arithmatex">\(P7\)</span></td>
<td><span class="arithmatex">\(3x_1+x_2\)</span></td>
<td><span class="arithmatex">\(x_1+4x_2\le 12\)</span>,  <span class="arithmatex">\(2x_1+x_2\le 5\)</span></td>
</tr>
<tr>
<td><span class="arithmatex">\(P42\)</span></td>
<td><span class="arithmatex">\(3x_1+2x_2+4x_3\)</span></td>
<td><span class="arithmatex">\(x_1+x_2+2x_3\le 4\)</span>,  <span class="arithmatex">\(2x_1+3x_3\le 5\)</span>,  <span class="arithmatex">\(2x_1+x_2+3x_3\le 7\)</span></td>
</tr>
<tr>
<td><span class="arithmatex">\(P46\)</span></td>
<td><span class="arithmatex">\(5x_1+6x_2+10x_3\)</span></td>
<td><span class="arithmatex">\(9x_1+2x_2+2x_3\le 11\)</span>,  <span class="arithmatex">\(2x_1+6x_2+6x_3\le 16\)</span>,  <span class="arithmatex">\(4x_1+2x_2+7x_3\le 10\)</span></td>
</tr>
<tr>
<td><span class="arithmatex">\(P72\)</span></td>
<td><span class="arithmatex">\(7x_1+6x_2+5x_3-2x_4+3x_5\)</span></td>
<td><span class="arithmatex">\(x_1+3x_2+5x_3-2x_4+2x_5\le 4\)</span>,  <span class="arithmatex">\(4x_1+2x_2-2x_3+x_4+x_5\le 3\)</span>,</td>
</tr>
<tr>
<td></td>
<td></td>
<td><span class="arithmatex">\(2x_1+4x_2+4x_3-2x_4+5x_5\le 5\)</span>,  <span class="arithmatex">\(3x_1+x_2+2x_3-x_4-2x_5\le 1\)</span></td>
</tr>
</table></div>

- The following table reports the value $z(\LP)$ of the LP relaxation, the upper bound $\lfloor z(\LP)\rfloor$ (the objective takes integer values), and the optimal value $z(\ILP)$, all computed with the exact simplex algorithm and checked with Gurobi.

<div class="tabella" markdown="0"><table>
<tr>
<th>problem</th>
<th>variables</th>
<th>constraints</th>
<th><span class="arithmatex">\(z(\LP)\)</span></th>
<th><span class="arithmatex">\(\lfloor z(\LP)\rfloor\)</span></th>
<th><span class="arithmatex">\(z(\ILP)\)</span></th>
</tr>
<tr>
<td>car</td>
<td><span class="arithmatex">\(2\)</span></td>
<td><span class="arithmatex">\(2\)</span></td>
<td><span class="arithmatex">\(\frac{31}{2}\)</span></td>
<td><span class="arithmatex">\(15\)</span></td>
<td><span class="arithmatex">\(14\)</span></td>
</tr>
<tr>
<td><span class="arithmatex">\(P7\)</span></td>
<td><span class="arithmatex">\(2\)</span></td>
<td><span class="arithmatex">\(2\)</span></td>
<td><span class="arithmatex">\(\frac{15}{2}\)</span></td>
<td><span class="arithmatex">\(7\)</span></td>
<td><span class="arithmatex">\(7\)</span></td>
</tr>
<tr>
<td><span class="arithmatex">\(P42\)</span></td>
<td><span class="arithmatex">\(3\)</span></td>
<td><span class="arithmatex">\(3\)</span></td>
<td><span class="arithmatex">\(\frac{21}{2}\)</span></td>
<td><span class="arithmatex">\(10\)</span></td>
<td><span class="arithmatex">\(10\)</span></td>
</tr>
<tr>
<td><span class="arithmatex">\(P46\)</span></td>
<td><span class="arithmatex">\(3\)</span></td>
<td><span class="arithmatex">\(3\)</span></td>
<td><span class="arithmatex">\(\frac{499}{25}\)</span></td>
<td><span class="arithmatex">\(19\)</span></td>
<td><span class="arithmatex">\(16\)</span></td>
</tr>
<tr>
<td><span class="arithmatex">\(P72\)</span></td>
<td><span class="arithmatex">\(5\)</span></td>
<td><span class="arithmatex">\(4\)</span></td>
<td><span class="arithmatex">\(\frac{507}{59}\)</span></td>
<td><span class="arithmatex">\(8\)</span></td>
<td><span class="arithmatex">\(7\)</span></td>
</tr>
</table></div>

<a id="box-4-ex-lpsol"></a>

!!! esempio "Example 10.1: The LP solutions of the examples"

    <div class="tabella" markdown="0"><table>
    <tr>
    <th>problem</th>
    <th>optimal solution <span class="arithmatex">\(\tilde{\vet x}\)</span> of the LP relaxation</th>
    </tr>
    <tr>
    <td>car</td>
    <td><span class="arithmatex">\(\big(\frac72,\frac52\big)\)</span></td>
    </tr>
    <tr>
    <td><span class="arithmatex">\(P7\)</span></td>
    <td><span class="arithmatex">\(\big(\frac52,0\big)\)</span></td>
    </tr>
    <tr>
    <td><span class="arithmatex">\(P42\)</span></td>
    <td><span class="arithmatex">\(\big(\frac52,\frac32,0\big)\)</span></td>
    </tr>
    <tr>
    <td><span class="arithmatex">\(P46\)</span></td>
    <td><span class="arithmatex">\(\big(\frac{17}{25},\frac{49}{25},\frac{12}{25}\big)\)</span></td>
    </tr>
    <tr>
    <td><span class="arithmatex">\(P72\)</span></td>
    <td><span class="arithmatex">\(\big(\frac{39}{59},0,\frac{91}{59},\frac{166}{59},\frac{37}{59}\big)\)</span></td>
    </tr>
    </table></div>

    The first three solutions have at most two fractional components, with fractional part $\frac12$; the last two are the examples where the methods have more room to fail.

## 10.3 Rounding { #sez-sec-rounding }

<a id="box-5-def-rounding"></a>

!!! definizione "Definition 10.4: rounding"

    <em>Rounding</em> is the simplest MIP-based heuristic. It solves the LP relaxation, rounds every integer variable of the LP solution to the nearest integer, and completes the continuous variables. It returns a feasible solution, or it fails if the rounded point violates a constraint.

- The pseudo-code of rounding reads as follows:

!!! algoritmo "Algorithm: Rounding"

    <strong>Input:</strong> MIP [(10.1)](#eq-eq-mip) with integer variables $\mathscr{D}$  
    <strong>Output:</strong> Feasible solution $\bar{\vet x}$, or FAIL  
    <strong>function</strong> Rounding(MIP)  
    &emsp;&emsp;$\tilde{\vet x} :=$ optimal solution of the LP relaxation&emsp;*// $\min\{f(\vet x): \vet x\in\mathcal{L}\}$*  
    &emsp;&emsp;<strong>for</strong> $j\in\mathscr{D}$ <strong>do</strong>  
    &emsp;&emsp;&emsp;&emsp;$\bar x_j \leftarrow \rnd{\tilde x_j}$&emsp;*// round to the nearest integer*  
    &emsp;&emsp;fix $x_j:=\bar x_j$ for all $j\in\mathscr{D}$ and solve the LP on the continuous variables  
    &emsp;&emsp;<strong>if</strong> the LP is feasible <strong>then</strong>  
    &emsp;&emsp;&emsp;&emsp;<strong>return</strong> $\bar{\vet x}$ completed with the continuous part of the LP solution  
    &emsp;&emsp;<strong>else</strong>  
    &emsp;&emsp;&emsp;&emsp;<strong>return</strong> FAIL&emsp;*// the rounded point violates a constraint*

- <strong>Why it can work.</strong> When the formulation is strong, the LP solution is close to an optimal integer solution, and many of its integer variables are already integer: rounding changes only a few of them, by less than $\frac12$ each.

- <strong>Why it can fail.</strong> Rounding treats the variables one at a time, while the constraints link them. A constraint with all non-negative coefficients and right-hand side $b_i\ge0$ stays satisfied if the variables are rounded <em>down</em>, and one of the form $\ge$ if they are rounded <em>up</em>.

- An <strong>equality</strong> constraint is almost always violated by independent roundings: two fractional values that sum to $1$ become, for instance, $1$ and $1$.

<a id="box-6-obs-rounddown"></a>

!!! teorema "Observation 10.5"

    Let all the coefficients of $A$ be non-negative, $b\ge 0$ and $l=0$. If $\tilde{\vet x}\in\mathcal{L}$, then the point $\lfloor \tilde{\vet x}\rfloor$, obtained by rounding down every integer variable, belongs to $\mathcal{L}$.

??? dimostrazione "Proof"

    For every $j$, $0\le\lfloor \tilde x_j\rfloor\le \tilde x_j\le u_j$, so the bounds hold. For every row $i$, since $a_{ij}\ge0$, $\sum_j a_{ij}\lfloor \tilde x_j\rfloor\le\sum_j a_{ij}\tilde x_j\le b_i$. <span class="qed">□</span>

- The car example satisfies the assumptions of the observation: rounding down is always feasible, and in general not optimal.

<a id="box-7-ex-rounding"></a>

!!! esempio "Example 10.2: Rounding on the car example and on $P42$"

    <strong>Car example.</strong> The LP solution $\tilde{\vet x}=\big(\frac72,\frac52\big)$ is rounded to $\bar{\vet x}=(4,3)$, which violates the first constraint:

    $$
    10\cdot 4+4\cdot 3=52>45 .
    $$

    <strong>Rounding fails.</strong> Rounding down instead gives $(3,2)$: $38\le45$, $40\le49$, of value $13$, while $z(\ILP)=14$.

    <strong>$P42$.</strong> The LP solution $\big(\frac52,\frac32,0\big)$ is rounded to $(3,2,0)$, which violates the second constraint: $2\cdot3=6>5$. <strong>Rounding fails.</strong> Rounding down gives $(2,1,0)$, of value $8$, while $z(\ILP)=10$.

    <strong>$P72$.</strong> The LP solution is rounded to $(1,0,2,3,1)$, which violates the first constraint: $1+10-6+2=7>4$. <strong>Rounding fails.</strong>

- The two examples summarize the method: the rounding to the nearest integer fails when several variables are fractional at the same time and the constraints are tight; the rounding down is safe, but leaves part of the LP value on the table.

## 10.4 Diving { #sez-sec-diving }

- Rounding takes all its decisions at once, on the basis of a single LP solution. <strong>Diving</strong> takes them one at a time, and after each decision it asks the LP again: the LP solution is re-computed with the decisions taken so far, and it adapts to them.

<a id="box-8-def-diving"></a>

!!! definizione "Definition 10.6: diving"

    <em>Diving</em> repeatedly solves the LP relaxation, selects one integer variable with a fractional value, fixes it to an integer value, and solves the LP again. It stops with a feasible solution when the LP solution is integer on all the integer variables, and it fails when the LP becomes infeasible. A <em>rule</em> decides which variable is fixed and to which value.

- The pseudo-code of diving reads as follows:

!!! algoritmo "Algorithm: Diving"

    <strong>Input:</strong> MIP [(10.1)](#eq-eq-mip); a rule; for guided diving, an incumbent $\bar{\vet x}$  
    <strong>Output:</strong> Feasible solution, or FAIL  
    <strong>function</strong> Diving(MIP, rule)  
    &emsp;&emsp;<strong>while</strong> true <strong>do</strong>  
    &emsp;&emsp;&emsp;&emsp;solve the LP relaxation with the current bounds $l,u$  
    &emsp;&emsp;&emsp;&emsp;<strong>if</strong> the LP is infeasible <strong>then</strong>  
    &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;<strong>return</strong> FAIL&emsp;*// the dive ends in an infeasible LP*  
    &emsp;&emsp;&emsp;&emsp;$\tilde{\vet x} :=$ optimal solution of the LP  
    &emsp;&emsp;&emsp;&emsp;$F := \{j\in\mathscr{D} : \tilde x_j\notin\Z\}$&emsp;*// fractional integer variables*  
    &emsp;&emsp;&emsp;&emsp;<strong>if</strong> $F=\emptyset$ <strong>then</strong>  
    &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;<strong>return</strong> $\tilde{\vet x}$&emsp;*// integer on $\mathscr{D}$: feasible*  
    &emsp;&emsp;&emsp;&emsp;select $j\in F$ and a value $v\in\Z$ according to the rule  
    &emsp;&emsp;&emsp;&emsp;$l_j\leftarrow v$;   $u_j\leftarrow v$&emsp;*// fix $x_j:=v$*

- <strong>Fractional diving.</strong> Select the variable that is <em>least</em> fractional, i.e., the $j\in F$ that minimizes $\big|\tilde x_j-\rnd{\tilde x_j}\big|$, and fix it to $v=\rnd{\tilde x_j}$. The LP is almost sure about this variable, so the fixing changes the LP solution as little as possible. In the codes of this chapter, ties are broken in favour of the variable with the smallest index.

- <strong>Guided diving</strong> [\[3\]](#ref-DRL05). With an incumbent $\bar{\vet x}$, select the $j\in F$ that minimizes $|\tilde x_j-\bar x_j|$, and fix it to $v=\bar x_j$. The incumbent guides the dive toward a region of good solutions, and the LP decides in which order its values are adopted.

- Many other rules exist: <em>coefficient diving</em> fixes the variable that appears in the fewest constraints that can be violated by rounding it; <em>pseudo-cost diving</em> uses the history of the branch-and-bound tree; <em>vector-length diving</em> is tailored to set covering constraints. Modern solvers run several of them.

- <strong>Diving and branch-and-bound.</strong> Fixing $x_j:=\rnd{\tilde x_j}$ is a branching decision. A dive is a single path from the root of the branch-and-bound tree to a leaf, always taking one of the two children, without ever coming back: depth-first search without backtracking.

- <strong>Cost.</strong> Every step solves an LP that differs from the previous one by a single bound, so the dual simplex algorithm re-optimizes it starting from the previous basis in a few iterations. A dive with $|\mathscr{D}|$ integer variables needs at most $|\mathscr{D}|$ LPs, usually far fewer, since an LP solution has many integer components already.

- <strong>Failure.</strong> The LP becomes infeasible when the fixings taken so far are incompatible. Implementations then undo the last fixing and try the other value (a single backtrack), or give up.

<a id="box-9-ex-divecar"></a>

!!! esempio "Example 10.3: Fractional diving on the car example"

    <strong>Step 1.</strong> The LP solution is $\tilde{\vet x}=\big(\frac72,\frac52\big)$, of value $\frac{31}{2}$. Both variables are at distance $\frac12$ from the nearest integer; the tie is broken by the index: $x_1:=\rnd{\frac72}=4$.

    <strong>Step 2.</strong> With $x_1=4$ the constraints become $4x_2\le5$ and $14x_2\le33$, so the LP solution is $\tilde{\vet x}=\big(4,\frac54\big)$, of value $12+\frac52=\frac{29}{2}$. Fix $x_2:=\rnd{\frac54}=1$.

    <strong>Step 3.</strong> The LP solution $(4,1)$, of value $14$, is integer: the dive returns $(4,1)$, which is the <strong>optimal</strong> solution of the ILP problem.

    ![Figure 1](../img/10-mip-heuristics/fig01.svg){ .fig loading=lazy  }

<a id="box-10-ex-guided"></a>

!!! esempio "Example 10.4: Guided diving on the car example and on $P7$"

    <strong>Car example.</strong> The incumbent is $\bar{\vet x}=(3,2)$, of value $13$ (rounding down). In the LP solution $\big(\frac72,\frac52\big)$ both variables are at distance $\frac12$ from $\bar{\vet x}$; fix $x_1:=3$. The new LP solution is $\big(3,\frac{37}{14}\big)$, of value $\frac{100}{7}$; fix $x_2:=\bar x_2=2$. The LP solution $(3,2)$ is integer, of value $13$: the dive <strong>returns the incumbent</strong>, without improving it.

    <strong>$P7$.</strong> The incumbent is $\bar{\vet x}=(2,0)$, of value $6$ (rounding down of $\big(\frac52,0\big)$). The only fractional variable is $x_1$: fix $x_1:=2$. The LP solution $(2,1)$, of value $7$, is integer: guided diving finds the <strong>optimal</strong> solution after one fixing, even if the incumbent has $\bar x_2=0$, because the LP rebuilds the variables that are not fixed.

- The incumbent only decides the <em>values</em> of the fixed variables: on the car example both fixed values agree with the poor incumbent, on $P7$ the fixed value is the right one.

<a id="box-11-ex-divefail"></a>

!!! esempio "Example 10.5: A dive that fails on $P42$ and on $P72$"

    <strong>$P42$.</strong> The LP solution is $\big(\frac52,\frac32,0\big)$; $x_1$ and $x_2$ are at distance $\frac12$, and the tie is broken by the index: $x_1:=\rnd{\frac52}=3$. The second constraint gives $2\cdot3+3x_3\le5$, which is violated for every $x_3\ge0$: the LP is infeasible, <strong>the dive fails at the first step</strong>.

    <strong>$P72$.</strong> The fractional parts of the LP solution are $\frac{39}{59},\frac{91}{59},\frac{166}{59},\frac{37}{59}$ (for $x_1,x_3,x_4,x_5$), and the least fractional is $x_4$, at distance $\frac{11}{59}$ from $3$: $x_4:=3$. The new LP has value $\frac{728}{85}$ and solution $\big(\frac{56}{85},0,\frac{138}{85},3,\frac{52}{85}\big)$; the least fractional variable is $x_1$, rounded to $1$. Fixing $x_1:=1$ and $x_4:=3$ makes the LP <strong>infeasible</strong>.

- In both cases the first fixings follow the LP, which does not see that the constraints, together, leave no room. A dive that fails gives no solution at all: this is the reason for the feasibility pump of the next section.

## 10.5 The feasibility pump { #sez-sec-fp }

- Rounding and diving fail on $P42$ and on $P72$, and on many MIPs they fail most of the time: for a general MIP, even <em>deciding whether a feasible solution exists</em> is NP-complete. The <strong>feasibility pump</strong> of Fischetti, Glover and Lodi [\[4\]](#ref-FGL05) gives up the objective function and concentrates on a single goal: to find a feasible solution.

- The idea is to work with two points at the same time. A point of $\mathcal{L}$ satisfies all the linear constraints, but may be fractional; an integer point is integer, but may violate the constraints. A feasible solution is a point that is both. The pump makes the two points chase each other until they coincide, and to do so it needs to measure how far apart they are.

<a id="box-12-def-distance"></a>

!!! definizione "Definition 10.7: Distance from an integer point"

    Let $\bar{\vet x}$ be a point with $\bar x_j\in\Z$ for all $j\in\mathscr{D}$. The <strong>distance</strong> of a point $\vet x\in\R^N$ from $\bar{\vet x}$ is

    $$
    \Delta(\vet x,\bar{\vet x}) = \sum_{j\in\mathscr{D}} |x_j-\bar x_j| ,
    $$

    the $L_1$ distance computed on the integer variables only.

- When all the integer variables are <strong>binary</strong>, $|x_j-\bar x_j|$ equals $x_j$ if $\bar x_j=0$ and $1-x_j$ if $\bar x_j=1$, for every $x_j\in[0,1]$. The distance is then a <em>linear</em> function of $\vet x$:

!!! chiave ""

    $$
    \Delta(\vet x,\bar{\vet x}) \;=\; \sum_{j\in\mathscr{D}:\ \bar x_j=0} x_j \;+\; \sum_{j\in\mathscr{D}:\ \bar x_j=1} (1-x_j)
    \qquad \text{for binary variables}
    $$

- For a general integer variable the same holds if $\bar x_j=l_j$ (the term is $x_j-l_j$) or $\bar x_j=u_j$ (the term is $u_j-x_j$). Otherwise $|x_j-\bar x_j|$ is replaced by an auxiliary variable $d_j$ with the two constraints $d_j\ge x_j-\bar x_j$ and $d_j\ge \bar x_j-x_j$; at the minimum, $d_j=|x_j-\bar x_j|$. In all cases, the point of $\mathcal{L}$ closest to $\bar{\vet x}$ is computed by solving <strong>one LP</strong>.

<a id="box-13-def-fp"></a>

!!! definizione "Definition 10.8: feasibility pump"

    The <em>feasibility pump</em> builds two sequences of points: points $\tilde{\vet x}^{0},\tilde{\vet x}^{1},\tilde{\vet x}^{2},\dots$ of $\mathcal{L}$, which satisfy the linear constraints, and integer points $\bar{\vet x}^0,\bar{\vet x}^1,\bar{\vet x}^2,\dots$, which satisfy the integrality constraints. It starts from the LP solution $\tilde{\vet x}^{0}$; each integer point is the <em>rounding</em> of the last point of $\mathcal{L}$, and each point of $\mathcal{L}$ is the <em>projection</em> of the last integer point, i.e., the point of $\mathcal{L}$ at minimum distance from it. It stops when the distance is $0$: the two points coincide and are a feasible solution. When the rounding returns an integer point already visited, the pump <em>perturbs</em> it.

- The pseudo-code of the feasibility pump reads as follows:

!!! algoritmo "Algorithm: Feasibility pump"

    <strong>Input:</strong> MIP [(10.1)](#eq-eq-mip); maximum number of iterations $maxIter$; seed of the random numbers  
    <strong>Output:</strong> Feasible solution, or FAIL  
    <strong>function</strong> FeasibilityPump(MIP, $maxIter$, $seed$)  
    &emsp;&emsp;$\tilde{\vet x} :=$ optimal solution of the LP relaxation  
    &emsp;&emsp;<strong>if</strong> $\tilde x_j\in\Z$ for all $j\in\mathscr{D}$ <strong>then</strong>  
    &emsp;&emsp;&emsp;&emsp;<strong>return</strong> $\tilde{\vet x}$  
    &emsp;&emsp;$\bar{\vet x} \leftarrow \rnd{\tilde{\vet x}}$&emsp;*// round the integer components*  
    &emsp;&emsp;<strong>for</strong> $it := 1$ <strong>to</strong> $maxIter$ <strong>do</strong>  
    &emsp;&emsp;&emsp;&emsp;$\tilde{\vet x} \leftarrow \arg\min\{\Delta(\vet x,\bar{\vet x}) : \vet x\in\mathcal{L}\}$&emsp;*// projection: one LP*  
    &emsp;&emsp;&emsp;&emsp;<strong>if</strong> $\Delta(\tilde{\vet x},\bar{\vet x})=0$ <strong>then</strong>  
    &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;<strong>return</strong> $\tilde{\vet x}$&emsp;*// $\tilde{\vet x}$ is integer on $\mathscr{D}$: feasible*  
    &emsp;&emsp;&emsp;&emsp;$\hat{\vet x} \leftarrow \rnd{\tilde{\vet x}}$  
    &emsp;&emsp;&emsp;&emsp;<strong>if</strong> $\hat x_j=\bar x_j$ for all $j\in\mathscr{D}$ <strong>then</strong>&emsp;*// short cycle*  
    &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;$j \leftarrow \arg\max_{k\in\mathscr{D}} |\tilde x_k-\bar x_k|$  
    &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;move $\hat x_j$ by one unit toward $\tilde x_j$&emsp;*// flip*  
    &emsp;&emsp;&emsp;&emsp;<strong>if</strong> $\hat{\vet x}$ equals one of the last $3$ points $\bar{\vet x}$ <strong>then</strong>&emsp;*// long cycle*  
    &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;<strong>for</strong> $j\in\mathscr{D}$ <strong>do</strong>  
    &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;$\rho_j \leftarrow$ uniform random number in $[-0.3,\,0.7]$  
    &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;<strong>if</strong> $|\tilde x_j-\hat x_j|+\max\{\rho_j,0\}>\frac12$ <strong>then</strong>  
    &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;$\hat x_j\leftarrow 1-\hat x_j$&emsp;*// restart*  
    &emsp;&emsp;&emsp;&emsp;$\bar{\vet x}\leftarrow\hat{\vet x}$  
    &emsp;&emsp;<strong>return</strong> FAIL

- <strong>Why the pump works.</strong> Each projection moves the point of $\mathcal{L}$ toward the last integer point, and each rounding moves the integer point to the nearest one. The distance between the two sequences tends to decrease, and the points are <em>pumped</em> toward a region where $\mathcal{L}$ contains integer points. Each iteration costs one LP and a rounding.

- <strong>Short cycles.</strong> If the rounding of the projection gives back the same integer point, the pump would repeat the same two points forever. It then changes the components of $\bar{\vet x}$ on which $\tilde{\vet x}$ is most uncertain, those with the largest $|\tilde x_j-\bar x_j|$.

- The original pump flips a random number of components between $10$ and $30$. On the small instances of these notes one flip is enough: the code flips the single component with the largest $|\tilde x_j-\bar x_j|$ (the first one, in case of ties).

- <strong>Long cycles.</strong> The pump can also cycle among several integer points. When the new integer point equals one of the last ones, the original pump makes a <strong>restart</strong>: a binary component is flipped when $|\tilde x_j-\hat x_j|+\max\{\rho_j,0\}>\frac12$, with $\rho_j$ uniformly random in $[-0.3,0.7]$, so that the components where $\tilde x_j$ is far from $\hat x_j$ are flipped with high probability.

- <strong>Random numbers.</strong> In the codes of this chapter, random numbers are produced by the <em>minimal standard</em> generator of Park and Miller, with integer arithmetic on $64$ bits:

    $$
    seed \leftarrow (16807\cdot seed) \bmod (2^{31}-1), \qquad u = \frac{seed}{2^{31}-1}\in(0,1),
    $$

    and in the pump $\rho_j=u-0.3$. Fixing the initial seed makes every run reproducible.

- <strong>The objective.</strong> After the first LP, the original objective $f$ is ignored: the pump finds a feasible solution, but not necessarily a good one. The <em>objective feasibility pump</em> of Achterberg and Berthold [\[1\]](#ref-AB07) minimizes a combination $(1-\alpha)\,\Delta(\vet x,\bar{\vet x})+\alpha\, f(\vet x)$ with a weight $\alpha$ that decreases at every iteration: the solutions found are much better, at the price of a few more iterations.

- <strong>General integer variables.</strong> The pump was designed for binary variables, where the flip changes $\bar x_j$ into $1-\bar x_j$; for general integer variables it moves $\bar x_j$ by one unit toward $\tilde x_j$, and the distance uses the auxiliary variables $d_j$ [\[2\]](#ref-BFL07).

<a id="box-14-ex-fp2"></a>

!!! esempio "Example 10.6: The feasibility pump in two variables"

    Consider the integer program

    \begin{align*}
    && \max ~~ x_1 + 4\,x_2 & & \\[0.5ex]
    && \text{subject to} \quad 4\,x_1 + 3\,x_2 &\ge 7 & \\[0.5ex]
    && 4\,x_1 + 5\,x_2 &\le 11 & \\[0.5ex]
    && x_1 &\in \{0,1,\dots,5\} & \\[0.5ex]
    && x_2 &\in \{0,1,\dots,5\} &
    \end{align*}

    The region $\mathcal{L}$ is the triangle with vertices $\big(\frac14,2\big)$, $\big(\frac74,0\big)$, $\big(\frac{11}{4},0\big)$, which contains only two integer points, $(1,1)$ and $(2,0)$. The figure shows the two sequences of points: squares for the points of $\mathcal{L}$, circles for the integer points, and the diamonds $\{\vet x:\Delta(\vet x,\bar{\vet x})\le r\}$ of the two projections.

    ![Figure 2](../img/10-mip-heuristics/fig02.svg){ .fig loading=lazy  }

    The iterations are the following.

    <div class="tabella" markdown="0"><table>
    <tr>
    <th><span class="arithmatex">\(k\)</span></th>
    <th>integer point</th>
    <th>projection <span class="arithmatex">\(\tilde{\vet x}^{k}\)</span></th>
    <th><span class="arithmatex">\(\Delta\big(\tilde{\vet x}^{k},\bar{\vet x}^{k-1}\big)\)</span></th>
    <th>next integer point</th>
    </tr>
    <tr>
    <td><span class="arithmatex">\(0\)</span></td>
    <td>—</td>
    <td>LP optimum <span class="arithmatex">\(\big(\frac14,2\big)\)</span></td>
    <td>—</td>
    <td>rounding: <span class="arithmatex">\(\bar{\vet x}^0=(0,2)\)</span></td>
    </tr>
    <tr>
    <td><span class="arithmatex">\(1\)</span></td>
    <td><span class="arithmatex">\(\bar{\vet x}^0=(0,2)\)</span></td>
    <td><span class="arithmatex">\(\big(\frac14,2\big)\)</span></td>
    <td><span class="arithmatex">\(\frac14\)</span></td>
    <td><span class="arithmatex">\(\rnd{\tilde{\vet x}^{1}}=\bar{\vet x}^0\)</span>: flip <span class="arithmatex">\(x_1\)</span>, <span class="arithmatex">\(\bar{\vet x}^1=(1,2)\)</span></td>
    </tr>
    <tr>
    <td><span class="arithmatex">\(2\)</span></td>
    <td><span class="arithmatex">\(\bar{\vet x}^1=(1,2)\)</span></td>
    <td><span class="arithmatex">\(\big(1,\frac75\big)\)</span></td>
    <td><span class="arithmatex">\(\frac35\)</span></td>
    <td>rounding: <span class="arithmatex">\(\bar{\vet x}^2=(1,1)\)</span></td>
    </tr>
    <tr>
    <td><span class="arithmatex">\(3\)</span></td>
    <td><span class="arithmatex">\(\bar{\vet x}^2=(1,1)\)</span></td>
    <td><span class="arithmatex">\((1,1)\)</span></td>
    <td><span class="arithmatex">\(0\)</span></td>
    <td>stop: feasible</td>
    </tr>
    </table></div>

    <strong>Iteration 0.</strong> The LP optimum is the vertex $\big(\frac14,2\big)$, of value $\frac{33}{4}$. Its rounding $(0,2)$ violates the first constraint: $4\cdot0+3\cdot2=6<7$.

    <strong>Iteration 1.</strong> With $\bar{\vet x}^0=(0,2)$, $x_1$ is at its lower bound and $\Delta(\vet x,\bar{\vet x}^0)=x_1+|x_2-2|$. The L$_1$ distance from a point to a segment that is neither horizontal, vertical nor of slope $\pm1$ is minimized at an endpoint or where the segment crosses the horizontal or the vertical line through the point; so the candidates are the three vertices and these crossings. The horizontal line $x_2=2$ touches $\mathcal{L}$ only at the vertex $\big(\frac14,2\big)$, and the vertical line $x_1=0$ misses it; the closest point is $\tilde{\vet x}^{1}=\big(\frac14,2\big)$, at distance $\frac14$. Its rounding is $(0,2)=\bar{\vet x}^0$ again: a short cycle. The component with the largest $|\tilde x^{1}_j-\bar x^0_j|$ is $x_1$ ($\frac14$ against $0$), which moves one unit toward $\frac14$: $\bar{\vet x}^1=(1,2)$.

    <strong>Iteration 2.</strong> The point $(1,2)$ violates the second constraint ($14>11$). The vertical line $x_1=1$ crosses $\mathcal{L}$ between $(1,1)$ and $\big(1,\frac75\big)$, at distances $1$ and $\frac35$ from $(1,2)$; the horizontal line $x_2=2$ gives $\big(\frac14,2\big)$, at distance $\frac34$; the other two vertices are farther. Hence $\tilde{\vet x}^{2}=\big(1,\frac75\big)$, and its rounding $(1,1)$ is a new point.

    <strong>Iteration 3.</strong> The point $(1,1)$ belongs to $\mathcal{L}$ ($4+3=7\ge7$ and $4+5=9\le 11$): its distance from $\mathcal{L}$ is $0$, and the pump returns $(1,1)$, of value $5$. It is optimal, since the other integer point $(2,0)$ has value $2$.

<a id="box-15-ex-fpcar"></a>

!!! esempio "Example 10.7: The feasibility pump on the car example and on $P72$"

    <strong>Car example.</strong> Rounding the LP solution gives $\bar{\vet x}^0=(4,3)$, which is infeasible.

    <div class="tabella" markdown="0"><table>
    <tr>
    <th><span class="arithmatex">\(k\)</span></th>
    <th>projection <span class="arithmatex">\(\tilde{\vet x}^{k}\)</span></th>
    <th><span class="arithmatex">\(\Delta\big(\tilde{\vet x}^{k},\bar{\vet x}^{k-1}\big)\)</span></th>
    <th>next integer point</th>
    </tr>
    <tr>
    <td><span class="arithmatex">\(1\)</span></td>
    <td><span class="arithmatex">\(\big(\frac72,\frac52\big)\)</span></td>
    <td><span class="arithmatex">\(1\)</span></td>
    <td><span class="arithmatex">\(\rnd{\tilde{\vet x}^1}=\bar{\vet x}^0\)</span>: flip <span class="arithmatex">\(x_1\)</span>, <span class="arithmatex">\(\bar{\vet x}^1=(3,3)\)</span></td>
    </tr>
    <tr>
    <td><span class="arithmatex">\(2\)</span></td>
    <td><span class="arithmatex">\(\big(3,\frac{37}{14}\big)\)</span></td>
    <td><span class="arithmatex">\(\frac{5}{14}\)</span></td>
    <td><span class="arithmatex">\(\rnd{\tilde{\vet x}^2}=\bar{\vet x}^1\)</span>: flip <span class="arithmatex">\(x_2\)</span>, <span class="arithmatex">\(\bar{\vet x}^2=(3,2)\)</span></td>
    </tr>
    <tr>
    <td><span class="arithmatex">\(3\)</span></td>
    <td><span class="arithmatex">\((3,2)\)</span></td>
    <td><span class="arithmatex">\(0\)</span></td>
    <td>stop: feasible</td>
    </tr>
    </table></div>

    At iteration $1$ the two components of $\tilde{\vet x}^1$ are at the same distance $\frac12$ from $\bar{\vet x}^0$, and the flip takes the first one. The pump needs <strong>two flips</strong> and returns $(3,2)$, of value $13$: it is feasible, but not optimal ($z(\ILP)=14$), because after the first LP the objective is ignored.

    <strong>$P72$.</strong> Rounding and diving fail (Examples [10.2](#box-7-ex-rounding), [Example 10.5](#box-11-ex-divefail)). The pump rounds the LP solution to $\bar{\vet x}^0=(1,0,2,3,1)$, which violates the first constraint, and needs three projections and one flip (of $x_3$, from $2$ to $1$) to reach the feasible solution

    $$
    \bar{\vet x}=(0,0,1,3,1), \qquad f(\bar{\vet x})=2 ,
    $$

    very far from $z(\ILP)=7$ and from the upper bound $8$.

- The pump is a method to find a <em>first</em> feasible solution, and it does that well. The quality of the solution is the job of the improvement methods of the next sections, which start from an incumbent.

## 10.6 Improving an incumbent with a sub-MIP { #sez-sec-submip }

- The methods of the previous sections build a solution from the LP relaxation. The three methods of this section start from an <strong>incumbent</strong> $\bar{\vet x}$, computed by any heuristic — rounding, diving, the feasibility pump — and try to improve it.

- They all follow the same pattern: add to the MIP some constraints that restrict it to a set of solutions <em>related to</em> $\bar{\vet x}$, and solve the restricted problem, called the <strong>sub-MIP</strong>, with the MIP solver.

- The sub-MIP is a neighbourhood of $\bar{\vet x}$, but a very large one, explored exactly by the solver rather than by enumeration. It is the idea of <strong>large neighbourhood search</strong>, with the solver as the repair method.

- Only solutions <em>better</em> than the incumbent are of interest. With an improvement step $\theta>0$, the sub-MIP contains the <strong>cutoff constraint</strong>

!!! chiave ""

    $$
    f(\vet x)\;\le\; f(\bar{\vet x})-\theta \qquad\text{(minimization)},\qquad\qquad
    f(\vet x)\;\ge\; f(\bar{\vet x})+\theta \qquad\text{(maximization)}.
    $$

- When $f$ takes integer values on all the feasible solutions, as for all the examples of this chapter, $\theta=1$ excludes exactly the solutions that are not better than $\bar{\vet x}$. If the sub-MIP with the cutoff is infeasible, the neighbourhood contains no better solution: the solver has <em>proved</em> that $\bar{\vet x}$ is a local optimum for that neighbourhood.

- The methods differ in <em>which</em> constraints define the neighbourhood: RINS fixes variables (<strong>hard fixing</strong>), local branching and proximity search bound the number of variables that change (<strong>soft fixing</strong>).

### 10.6.1 RINS { #sez-sec-rins }

<a id="box-16-def-rins"></a>

!!! definizione "Definition 10.9: RINS"

    <em>RINS</em> (Relaxation Induced Neighbourhood Search) [\[3\]](#ref-DRL05) compares the incumbent $\bar{\vet x}$ with the solution $\tilde{\vet x}$ of the LP relaxation. The integer variables on which the two solutions <em>agree</em> are fixed to their common value; the sub-MIP on the remaining variables, with the cutoff constraint, is solved by the MIP solver.

- The pseudo-code of RINS reads as follows:

!!! algoritmo "Algorithm: RINS"

    <strong>Input:</strong> MIP [(10.1)](#eq-eq-mip); incumbent $\bar{\vet x}$; improvement step $\theta$  
    <strong>Output:</strong> A solution at least as good as $\bar{\vet x}$  
    <strong>function</strong> RINS(MIP, $\bar{\vet x}$, $\theta$)  
    &emsp;&emsp;$\tilde{\vet x} :=$ optimal solution of the LP relaxation  
    &emsp;&emsp;<strong>for</strong> $j\in\mathscr{D}$ <strong>do</strong>  
    &emsp;&emsp;&emsp;&emsp;<strong>if</strong> $\tilde x_j=\bar x_j$ <strong>then</strong>  
    &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;fix $x_j:=\bar x_j$&emsp;*// LP and incumbent agree*  
    &emsp;&emsp;add the cutoff constraint $f(\vet x)\le f(\bar{\vet x})-\theta$  
    &emsp;&emsp;solve the sub-MIP with the MIP solver (with a node limit)  
    &emsp;&emsp;<strong>if</strong> the sub-MIP has a feasible solution $\vet x'$ <strong>then</strong>  
    &emsp;&emsp;&emsp;&emsp;<strong>return</strong> $\vet x'$&emsp;*// better than $\bar{\vet x}$*  
    &emsp;&emsp;<strong>else</strong>  
    &emsp;&emsp;&emsp;&emsp;<strong>return</strong> $\bar{\vet x}$&emsp;*// no better solution in the neighbourhood*

- <strong>Why the agreement.</strong> The LP solution is optimistic and possibly infeasible, the incumbent is feasible and possibly poor: two advisers with opposite defects. Where they agree, the value is probably right in good solutions too; where they disagree, there is something to decide, and the decision is left to the solver.

- <strong>The size of the neighbourhood is not chosen.</strong> It is the number of variables on which $\tilde{\vet x}$ and $\bar{\vet x}$ disagree: small if the incumbent is already close to the LP solution, large otherwise. For this reason the sub-MIP is solved with a limit on the number of nodes.

- <strong>RINS inside branch-and-bound.</strong> Danna, Rothberg and Le Pape apply RINS at the nodes of the branch-and-bound tree, with the LP solution of the node: deeper nodes have different LP solutions, hence different neighbourhoods of the same incumbent. Every modern MIP solver runs RINS in this way.

- <strong>Hard fixing.</strong> A fixed variable cannot change in the sub-MIP. If a better solution needs to change a variable on which $\tilde{\vet x}$ and $\bar{\vet x}$ agree, RINS cannot find it.

<a id="box-17-ex-rins"></a>

!!! esempio "Example 10.8: RINS on $P7$ and on $P42$"

    <strong>$P7$.</strong> The incumbent is $\bar{\vet x}=(2,0)$, of value $6$ (rounding down), and the LP solution is $\tilde{\vet x}=\big(\frac52,0\big)$. They agree on $x_2=0$, which is fixed. The sub-MIP

    $$
    \max~ 3\,x_1 \quad\text{subject to}\quad x_1\le 12,\quad 2\,x_1\le 5,\quad 3\,x_1\ge 7,\quad x_1\in\Z_{\ge 0}
    $$

    is infeasible: the second constraint gives $x_1\le2$, the cutoff $x_1\ge3$. RINS returns the incumbent. The optimal solution $(2,1)$ has $x_2=1$, but $x_2$ is fixed to $0$ because the LP and the incumbent agree on it.

    <strong>$P42$.</strong> The incumbent is $\bar{\vet x}=(2,1,0)$, of value $8$ (rounding down), and $\tilde{\vet x}=\big(\frac52,\frac32,0\big)$. They agree on $x_3=0$ only. The sub-MIP on $x_1,x_2$ with the cutoff $3x_1+2x_2\ge 9$ is solved by Gurobi, which returns $(2,2,0)$, of value $10$: <strong>RINS finds an optimal solution</strong>.

- On $P7$ the variable that must change is exactly the one on which the LP and the incumbent agree; on $P42$ the agreement concerns a variable that does not need to change, and the neighbourhood contains the optimum.

### 10.6.2 Local branching { #sez-sec-lb }

- RINS decides <em>which</em> variables may change. Local branching [\[5\]](#ref-FL03) decides only <em>how many</em> of them may change, and leaves the choice of which ones to the solver. For a MIP whose integer variables are binary (or, with the auxiliary variables $d_j$ of the distance, general integers), the <strong>local branching constraint</strong> with radius $k\in\Z_{\ge1}$ is

!!! chiave ""

    $$
    \Delta(\vet x,\bar{\vet x}) \;=\; \sum_{j\in\mathscr{D}:\ \bar x_j=1} (1-x_j) \;+\; \sum_{j\in\mathscr{D}:\ \bar x_j=0} x_j \;\le\; k
    $$

- $\Delta(\vet x,\bar{\vet x})$ is the distance of [Definition 10.7](#box-12-def-distance) for binary variables: on binary points, it counts the variables whose value differs from the incumbent. The constraint is linear, so it can be added to the MIP like any other constraint.

<a id="box-18-def-lb"></a>

!!! definizione "Definition 10.10: local branching"

    <em>Local branching</em> solves, with the MIP solver, the sub-MIP made of the MIP, the cutoff constraint and the local branching constraint $\Delta(\vet x,\bar{\vet x})\le k$. If it finds a better solution, the new solution becomes the incumbent and the neighbourhood is centred on it. If it proves that the neighbourhood contains no better solution, the radius $k$ is enlarged. In both cases the explored neighbourhood is excluded from the following sub-MIPs by the <em>reverse</em> constraint $\Delta(\vet x,\bar{\vet x})\ge k+1$.

!!! algoritmo "Algorithm: Local branching"

    <strong>Input:</strong> MIP [(10.1)](#eq-eq-mip); incumbent $\bar{\vet x}$; radius $k$; maximum radius $k_{\max}$; step $k_{step}$; improvement step $\theta$  
    <strong>Output:</strong> A solution at least as good as $\bar{\vet x}$  
    <strong>function</strong> LocalBranching(MIP, $\bar{\vet x}$, $k$, $k_{\max}$, $k_{step}$, $\theta$)  
    &emsp;&emsp;$\mathscr{R} := \emptyset$&emsp;*// reverse constraints added so far*  
    &emsp;&emsp;<strong>while</strong> $k\le k_{\max}$ <strong>do</strong>  
    &emsp;&emsp;&emsp;&emsp;solve the MIP with the constraints $\mathscr{R}$, $\Delta(\vet x,\bar{\vet x})\le k$ and $f(\vet x)\le f(\bar{\vet x})-\theta$  
    &emsp;&emsp;&emsp;&emsp;$\mathscr{R} \leftarrow \mathscr{R}\cup\big\{\Delta(\vet x,\bar{\vet x})\ge k+1\big\}$&emsp;*// never explore it again*  
    &emsp;&emsp;&emsp;&emsp;<strong>if</strong> a feasible solution $\vet x'$ is found <strong>then</strong>  
    &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;$\bar{\vet x} \leftarrow \vet x'$&emsp;*// re-centre the neighbourhood*  
    &emsp;&emsp;&emsp;&emsp;<strong>else</strong>  
    &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;$k \leftarrow k+k_{step}$&emsp;*// no better solution within distance $k$*  
    &emsp;&emsp;<strong>return</strong> $\bar{\vet x}$

- <strong>Soft fixing.</strong> With $|\mathscr{D}|$ binary variables, the neighbourhood $\Delta(\vet x,\bar{\vet x})\le k$ contains $\sum_{i=0}^{k}\binom{|\mathscr{D}|}{i}$ binary points: for $k$ small it is much smaller than the whole space, but it does not commit to any particular variable. Any $k$ variables may change.

- <strong>A branching scheme.</strong> The name comes from the original use of the constraint as a <em>branching rule</em>: the left branch $\Delta(\vet x,\bar{\vet x})\le k$ is solved first, and the right branch $\Delta(\vet x,\bar{\vet x})\ge k+1$ contains the rest of the problem. If the left branch improves the incumbent, the right branch is split again around the new incumbent; the tree is a long chain of right branches:

![Figure 3](../img/10-mip-heuristics/fig03.svg){ .fig loading=lazy  }

- <strong>The radius.</strong> $k$ must be small enough for the sub-MIP to be solved quickly, and large enough for the neighbourhood to contain better solutions. The original method uses small values compared with $|\mathscr{D}|$, a time limit on each sub-MIP, and <em>diversification</em> — a larger $k$, or a jump to a different solution — when no improvement is found.

- <strong>Local branching and local search.</strong> With $k=1$ the neighbourhood contains the points obtained from $\bar{\vet x}$ by changing a single integer variable by one unit (a binary variable flips); with larger $k$ it contains the points that differ from $\bar{\vet x}$ in several variables, or by several units. It is a local search whose neighbourhood is explored exactly by the solver.

<a id="box-19-ex-lb"></a>

!!! esempio "Example 10.9: Local branching on $P7$ and on $P72$"

    <strong>$P7$.</strong> Start from $\bar{\vet x}^1=(2,0)$, of value $6$, with $k=1$, $k_{\max}=3$, $k_{step}=1$ and $\theta=1$.

    <strong>$k=1$.</strong> The sub-MIP with $\Delta(\vet x,\bar{\vet x}^1)\le1$ and the cutoff $3x_1+x_2\ge7$ returns $(2,1)$, of value $7$: the new incumbent $\bar{\vet x}^2$, found at distance $1$ (the variable $x_2$ changes by one unit). Add $\Delta(\vet x,\bar{\vet x}^1)\ge2$.

    <strong>$k=1,2,3$, around $\bar{\vet x}^2$.</strong> No solution of value at least $8$: $k$ becomes $4>k_{\max}$ and the method stops with $(2,1)$, the optimum. Where RINS failed, local branching lets the solver choose that $x_2$ changes.

    <strong>$P72$.</strong> Start from the solution of the feasibility pump, $\bar{\vet x}^1=(0,0,1,3,1)$, of value $2$, with the same parameters.

    <div class="tabella" markdown="0"><table>
    <tr>
    <th><span class="arithmatex">\(k\)</span></th>
    <th>around</th>
    <th>solution returned</th>
    <th>value</th>
    </tr>
    <tr>
    <td><span class="arithmatex">\(1\)</span></td>
    <td><span class="arithmatex">\(\bar{\vet x}^1\)</span></td>
    <td><span class="arithmatex">\(\bar{\vet x}^2=(0,0,1,2,1)\)</span>, at distance <span class="arithmatex">\(1\)</span></td>
    <td><span class="arithmatex">\(4\)</span></td>
    </tr>
    <tr>
    <td><span class="arithmatex">\(1\)</span></td>
    <td><span class="arithmatex">\(\bar{\vet x}^2\)</span></td>
    <td>no solution of value <span class="arithmatex">\(\ge5\)</span></td>
    <td>—</td>
    </tr>
    <tr>
    <td><span class="arithmatex">\(2\)</span></td>
    <td><span class="arithmatex">\(\bar{\vet x}^2\)</span></td>
    <td><span class="arithmatex">\(\bar{\vet x}^3=(0,1,1,2,0)\)</span>, at distance <span class="arithmatex">\(2\)</span></td>
    <td><span class="arithmatex">\(7\)</span></td>
    </tr>
    <tr>
    <td><span class="arithmatex">\(2,3\)</span></td>
    <td><span class="arithmatex">\(\bar{\vet x}^3\)</span></td>
    <td>no solution of value <span class="arithmatex">\(\ge8\)</span></td>
    <td>—</td>
    </tr>
    </table></div>

    With $k_{\max}=3$ the method stops with value $7$, the optimum, after two improvements. The radius enlarged from $1$ to $2$ because the improvement of $x_2$ and $x_5$ together needs two units of distance.

- Local branching performs a local search with a neighbourhood whose size is controlled by $k$, explored exactly by the solver, and it enlarges the neighbourhood when it gets stuck — exactly the idea of variable neighbourhood search, with the solver doing the exploration.

### 10.6.3 Proximity search { #sez-sec-ps }

- Local branching keeps the objective and constrains the distance. <strong>Proximity search</strong> of Fischetti and Monaci [\[6\]](#ref-FM14) does the opposite: it keeps the cutoff as a constraint and <em>minimizes</em> the distance.

<a id="box-20-def-ps"></a>

!!! definizione "Definition 10.11: proximity search"

    The <strong>proximity sub-MIP</strong> around the incumbent $\bar{\vet x}$ reads

    \begin{align*}
    && \min ~~ \Delta(\vet x,\bar{\vet x}) & & \\[1ex]
    && \text{subject to} \quad A\,\vet x &\le b, & \\[1ex]
    && l_j\le x_j &\le u_j, & \forall j\in\{1,2,\dots,N\}, \\[1ex]
    && f(\vet x) &\le f(\bar{\vet x})-\theta, & \\[1ex]
    && x_j &\in\Z, & \forall j\in\mathscr{D}.
    \end{align*}

    <em>Proximity search</em> solves it, replaces the incumbent with its solution, and repeats, until the proximity sub-MIP is infeasible.

!!! algoritmo "Algorithm: Proximity search"

    <strong>Input:</strong> MIP [(10.1)](#eq-eq-mip); incumbent $\bar{\vet x}$; improvement step $\theta$  
    <strong>Output:</strong> A solution at least as good as $\bar{\vet x}$  
    <strong>function</strong> ProximitySearch(MIP, $\bar{\vet x}$, $\theta$)  
    &emsp;&emsp;<strong>while</strong> true <strong>do</strong>  
    &emsp;&emsp;&emsp;&emsp;solve $\min\{\Delta(\vet x,\bar{\vet x}) : \vet x \text{ feasible for the MIP},~ f(\vet x)\le f(\bar{\vet x})-\theta\}$  
    &emsp;&emsp;&emsp;&emsp;<strong>if</strong> the proximity sub-MIP is infeasible <strong>then</strong>  
    &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;<strong>return</strong> $\bar{\vet x}$&emsp;*// no solution better than $f(\bar{\vet x})-\theta$*  
    &emsp;&emsp;&emsp;&emsp;$\vet x' :=$ its optimal solution  
    &emsp;&emsp;&emsp;&emsp;fix $x_j:=x'_j$ for $j\in\mathscr{D}$ and re-optimize $f$ on the continuous variables  
    &emsp;&emsp;&emsp;&emsp;$\bar{\vet x}\leftarrow \vet x'$&emsp;*// the closest improving solution*

- <strong>Why minimize the distance.</strong> The original objective is often what makes a MIP hard: its LP relaxation drives the solver toward fractional points far from feasible solutions. In the proximity sub-MIP the objective is the distance, and the LP solution stays close to $\bar{\vet x}$, which is almost feasible: the heuristics of the solver work well, and a feasible solution is usually found quickly.

- In practice the proximity sub-MIP is not solved to optimality: any feasible solution is an improvement.

- <strong>No radius to choose.</strong> The size of the move is decided by the sub-MIP itself: the smallest change that improves the objective by at least $\theta$.

- <strong>Re-optimizing the continuous part.</strong> The solution of the sub-MIP has the right integer part, but its continuous variables were chosen to minimize the distance, not $f$. Fixing the integer variables and solving the LP on the continuous ones gives the true value of the new incumbent.

- <strong>The step $\theta$.</strong> A large $\theta$ asks for large improvements, hence large moves and hard sub-MIPs; a small $\theta$ gives easy sub-MIPs and many iterations. With $\theta=1$ and integer objective values, the method also proves optimality.

<a id="box-21-prop-ps"></a>

!!! teorema "Proposition 10.12"

    If $f$ takes integer values on all the feasible solutions of the MIP and the proximity sub-MIP around $\bar{\vet x}$ with $\theta=1$ is infeasible, then $f(\bar{\vet x})=z(I)$.

??? dimostrazione "Proof"

    The feasible region of the proximity sub-MIP is the set of the feasible solutions $\vet x$ of the MIP with $f(\vet x)\le f(\bar{\vet x})-1$; it does not depend on the objective $\Delta$. If it is empty, every feasible solution has $f(\vet x)>f(\bar{\vet x})-1$, hence $f(\vet x)\ge f(\bar{\vet x})$ since both values are integer. So $\bar{\vet x}$ is optimal. <span class="qed">□</span>

<a id="box-22-ex-ps"></a>

!!! esempio "Example 10.10: Proximity search on $P7$ and on $P72$"

    <strong>$P7$.</strong> From $\bar{\vet x}=(2,0)$, of value $6$: the closest solution of value at least $7$ is $(2,1)$, at distance $1$. Around it, no solution has value at least $8$ (the LP bound is $\frac{15}{2}$): by [Proposition 10.12](#box-21-prop-ps), $(2,1)$ is <strong>optimal</strong>.

    <strong>$P72$.</strong> From the solution of the feasibility pump, of value $2$:

    <div class="tabella" markdown="0"><table>
    <tr>
    <th>iteration</th>
    <th>cutoff</th>
    <th><span class="arithmatex">\(\min\Delta\)</span></th>
    <th>solution returned</th>
    <th>value</th>
    </tr>
    <tr>
    <td><span class="arithmatex">\(1\)</span></td>
    <td><span class="arithmatex">\(f(\vet x)\ge 3\)</span></td>
    <td><span class="arithmatex">\(1\)</span></td>
    <td><span class="arithmatex">\((0,0,1,2,1)\)</span></td>
    <td><span class="arithmatex">\(4\)</span></td>
    </tr>
    <tr>
    <td><span class="arithmatex">\(2\)</span></td>
    <td><span class="arithmatex">\(f(\vet x)\ge 5\)</span></td>
    <td><span class="arithmatex">\(2\)</span></td>
    <td><span class="arithmatex">\((0,1,1,2,0)\)</span></td>
    <td><span class="arithmatex">\(7\)</span></td>
    </tr>
    <tr>
    <td><span class="arithmatex">\(3\)</span></td>
    <td><span class="arithmatex">\(f(\vet x)\ge 8\)</span></td>
    <td>—</td>
    <td>infeasible</td>
    <td>—</td>
    </tr>
    </table></div>

    At the third iteration the sub-MIP is infeasible, and [Proposition 10.12](#box-21-prop-ps) proves that $7$ is optimal, although the upper bound $\lfloor z(\LP)\rfloor=8$ of the root does not.

- The proof of optimality comes from the solver, which proves the infeasibility of the last sub-MIP with its own dual bounds: proximity search, used with $\theta=1$, is an exact method that improves the incumbent along the way.

## 10.7 Relax-and-fix and fix-and-optimize { #sez-sec-rf }

- The variables of many MIPs come in <strong>blocks</strong> with a natural order, for instance because they are indexed by a sequence, and the decisions of a block mainly interact with those of the nearby blocks. The two methods of this section exploit a partition of the integer variables into blocks: they solve a sequence of small MIPs instead of a large one.

- Once the integer variables are fixed, the rest is an LP: the hard decisions are the integer variables, and the LP relaxation is the tool that estimates the effect of the decisions not yet taken.

### 10.7.1 Relax-and-fix

<a id="box-23-def-rf"></a>

!!! definizione "Definition 10.13: relax-and-fix"

    <em>Relax-and-fix</em> splits the integer variables into blocks $B_1,B_2,\dots,B_p$, in a given order. At step $r$ it solves the MIP in which the variables of $B_r$ are integer, those of $B_1,\dots,B_{r-1}$ are fixed to the values decided at the previous steps, and those of $B_{r+1},\dots,B_p$ are <em>relaxed</em> (continuous). Then it fixes the variables of $B_r$ to their values, and moves to the next block. The solution of the last step is feasible.

!!! algoritmo "Algorithm: Relax-and-fix"

    <strong>Input:</strong> MIP [(10.1)](#eq-eq-mip); partition $B_1,B_2,\dots,B_p$ of $\mathscr{D}$, in a given order  
    <strong>Output:</strong> Feasible solution, or FAIL  
    <strong>function</strong> RelaxAndFix(MIP, $B_1,\dots,B_p$)  
    &emsp;&emsp;<strong>for</strong> $r := 1$ <strong>to</strong> $p$ <strong>do</strong>  
    &emsp;&emsp;&emsp;&emsp;solve the MIP with: $x_j$ fixed for $j\in B_1\cup\dots\cup B_{r-1}$, $x_j$ integer for $j\in B_r$, $x_j$ continuous for $j\in B_{r+1}\cup\dots\cup B_p$  
    &emsp;&emsp;&emsp;&emsp;<strong>if</strong> it is infeasible <strong>then</strong>  
    &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;<strong>return</strong> FAIL  
    &emsp;&emsp;&emsp;&emsp;$\vet x' :=$ its optimal solution  
    &emsp;&emsp;&emsp;&emsp;fix $x_j:=x'_j$ for all $j\in B_r$  
    &emsp;&emsp;<strong>return</strong> $\vet x'$&emsp;*// all the integer variables are integer*

- <strong>Why.</strong> The decisions of the first blocks are taken with care (integer), while the others are only estimated (relaxed). Each MIP has the integer variables of a single block, so it is small.

- <strong>Myopia.</strong> The relaxed blocks are optimistic: the relaxation can buy a fraction of a decision for a fraction of its cost. A decision that looks good because the rest is cheap may turn out to be bad when the rest becomes integer. The fixed decisions can even make a later step infeasible.

- <strong>Variants.</strong> Larger blocks give better solutions and harder MIPs; <em>overlapping</em> blocks solve $B_r\cup B_{r+1}$ as integer but fix only $B_r$, to see a little further.

<a id="box-24-ex-rf"></a>

!!! esempio "Example 10.11: Relax-and-fix on $P46$"

    The integer variables are $x_1,x_2,x_3$. The LP relaxation has value $\frac{499}{25}$, the upper bound is $19$ and $z(\ILP)=16$, attained by $(0,1,1)$.

    <strong>One variable per block, $B_1=\{x_1\}$, $B_2=\{x_2\}$, $B_3=\{x_3\}$.</strong>

    <div class="tabella" markdown="0"><table>
    <tr>
    <th>step <span class="arithmatex">\(r\)</span></th>
    <th>value of the MIP of step <span class="arithmatex">\(r\)</span></th>
    <th>solution of the MIP</th>
    <th>fixing</th>
    </tr>
    <tr>
    <td><span class="arithmatex">\(1\)</span></td>
    <td><span class="arithmatex">\(\frac{296}{15}\approx19.73\)</span></td>
    <td><span class="arithmatex">\(\big(0,\frac{26}{15},\frac{14}{15}\big)\)</span></td>
    <td><span class="arithmatex">\(x_1:=0\)</span></td>
    </tr>
    <tr>
    <td><span class="arithmatex">\(2\)</span></td>
    <td><span class="arithmatex">\(\frac{56}{3}\approx18.67\)</span></td>
    <td><span class="arithmatex">\(\big(0,2,\frac23\big)\)</span></td>
    <td><span class="arithmatex">\(x_2:=2\)</span></td>
    </tr>
    <tr>
    <td><span class="arithmatex">\(3\)</span></td>
    <td><span class="arithmatex">\(12\)</span></td>
    <td><span class="arithmatex">\((0,2,0)\)</span></td>
    <td><span class="arithmatex">\(x_3:=0\)</span></td>
    </tr>
    </table></div>

    The result is $(0,2,0)$, of value $12$, with a gap of $\frac{19-12}{12}\approx58.3\%$. The wrong decision is taken at step $2$: with $x_3$ relaxed, $x_2=2$ gives $\frac{56}{3}\approx18.67$ and $x_2=1$ gives $\frac{122}{7}\approx17.43$, so $x_2:=2$; but then the second constraint leaves room only for $x_3\le\frac23$, and $x_3$ becomes $0$ when it is integer.

    <strong>Larger blocks, $B_1=\{x_1\}$, $B_2=\{x_2,x_3\}$.</strong> Step $1$ is the same. At step $2$ the MIP on $x_2,x_3$ is solved with both variables integer: it returns $(0,1,1)$, of value $16$, the <strong>optimum</strong>.

- The size of the blocks trades the quality of the solution against the difficulty of the MIPs: the larger block sees the interaction between $x_2$ and $x_3$ that the single-variable blocks miss.

### 10.7.2 Fix-and-optimize

<a id="box-25-def-fo"></a>

!!! definizione "Definition 10.14: fix-and-optimize"

    <em>Fix-and-optimize</em> improves an incumbent $\bar{\vet x}$. It considers a sequence of <em>windows</em> $W_1,W_2,\dots,W_q\subseteq\mathscr{D}$, typically consecutive periods. For each window it fixes all the integer variables <em>outside</em> the window to their values in $\bar{\vet x}$, and solves the sub-MIP on the variables of the window, with the cutoff constraint. A better solution becomes the incumbent. The passes over the windows are repeated until a whole pass gives no improvement.

!!! algoritmo "Algorithm: Fix-and-optimize"

    <strong>Input:</strong> MIP [(10.1)](#eq-eq-mip); incumbent $\bar{\vet x}$; windows $W_1,\dots,W_q$; improvement step $\theta$  
    <strong>Output:</strong> A solution at least as good as $\bar{\vet x}$  
    <strong>function</strong> FixAndOptimize(MIP, $\bar{\vet x}$, $W_1,\dots,W_q$, $\theta$)  
    &emsp;&emsp;$improved :=$ true  
    &emsp;&emsp;<strong>while</strong> $improved$ <strong>do</strong>  
    &emsp;&emsp;&emsp;&emsp;$improved \leftarrow$ false  
    &emsp;&emsp;&emsp;&emsp;<strong>for</strong> $r := 1$ <strong>to</strong> $q$ <strong>do</strong>  
    &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;solve the MIP with $x_j:=\bar x_j$ for $j\in\mathscr{D}\setminus W_r$ and $f(\vet x)\le f(\bar{\vet x})-\theta$  
    &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;<strong>if</strong> a feasible solution $\vet x'$ is found <strong>then</strong>  
    &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;$\bar{\vet x}\leftarrow \vet x'$;   $improved\leftarrow$ true  
    &emsp;&emsp;<strong>return</strong> $\bar{\vet x}$

<a id="box-26-ex-fo"></a>

!!! esempio "Example 10.12: Fix-and-optimize on $P46$ and on $P72$"

    <strong>$P46$.</strong> Start from the solution of relax-and-fix, $\bar{\vet x}=(0,2,0)$, of value $12$, with the windows $W_1=\{x_1\}$ and $W_2=\{x_2,x_3\}$ and $\theta=1$.

    <div class="tabella" markdown="0"><table>
    <tr>
    <th>window</th>
    <th>incumbent after the window</th>
    <th></th>
    </tr>
    <tr>
    <td><span class="arithmatex">\(\{x_1\}\)</span></td>
    <td><span class="arithmatex">\(12\)</span></td>
    <td>no improvement (<span class="arithmatex">\(x_1=1\)</span> violates the first constraint)</td>
    </tr>
    <tr>
    <td><span class="arithmatex">\(\{x_2,x_3\}\)</span></td>
    <td><span class="arithmatex">\(16\)</span></td>
    <td><span class="arithmatex">\(\bar{\vet x}=(0,1,1)\)</span></td>
    </tr>
    <tr>
    <td>second pass, both windows</td>
    <td><span class="arithmatex">\(16\)</span></td>
    <td>no improvement: stop</td>
    </tr>
    </table></div>

    With the three windows of one variable each, $\{x_1\}$, $\{x_2\}$, $\{x_3\}$, no window improves $(0,2,0)$: it is a local optimum for those windows. The window $\{x_2,x_3\}$ revises the wrong decision of relax-and-fix and reaches the optimum $16$.

    <strong>$P72$.</strong> Start from the solution of the feasibility pump, $(0,0,1,3,1)$, of value $2$, with the windows $\{x_1,x_2\}$, $\{x_3,x_4\}$, $\{x_5\}$. The first window gives no improvement; the second gives $(0,0,2,4,1)$, of value $5$; the third and the second pass give none. The method stops with $5<7=z(\ILP)$: a local optimum with respect to these windows, since the optimal solution $(0,1,1,2,0)$ differs from it in $x_2$, $x_3$, $x_4$ and $x_5$, more variables than any window contains.

- Fix-and-optimize is a <strong>local search</strong> whose moves change all the integer variables of a window at once, explored exactly by the solver: the integer-programming counterpart of the neighbourhoods of local search. It stops in a local optimum with respect to all the windows.

- Relax-and-fix and fix-and-optimize are natural partners: the first builds a solution block by block, the second repairs the myopic decisions looking at several blocks together.

## 10.8 Heuristics inside the solver { #sez-sec-gurobi }

- A modern MIP solver is not only a branch-and-bound algorithm: it runs, automatically, most of the heuristics of this chapter (the chapter «MIP solvers: what they do» shows real logs). In Gurobi:

    - <em>before the root LP</em>, heuristics that need no relaxation look for a first solution;

    - <em>at the root</em>, after the LP, rounding, several diving heuristics and the feasibility pump are run;

    - <em>during the branch-and-bound</em>, RINS and other sub-MIP heuristics are run at some nodes, with the LP solution of the node, and the new incumbents are reported in the log by a line starting with <code>H</code> (heuristic) instead of <code>\*</code> (integer LP solution of a node).

- The effort spent in heuristics is controlled by parameters. The most important ones are:

<div class="tabella" markdown="0"><table>
<tr>
<th>parameter</th>
<th>default</th>
<th>meaning</th>
</tr>
<tr>
<td><code>Heuristics</code></td>
<td><span class="arithmatex">\(0.05\)</span></td>
<td>fraction of the work spent in the heuristics (<span class="arithmatex">\(0\)</span> to <span class="arithmatex">\(1\)</span>)</td>
</tr>
<tr>
<td><code>MIPFocus</code></td>
<td><span class="arithmatex">\(0\)</span></td>
<td><span class="arithmatex">\(1\)</span>: focus on finding good feasible solutions rather than on the bound</td>
</tr>
<tr>
<td><code>RINS</code></td>
<td><span class="arithmatex">\(-1\)</span></td>
<td>frequency of RINS (every <span class="arithmatex">\(n\)</span> nodes; <span class="arithmatex">\(-1\)</span> automatic, <span class="arithmatex">\(0\)</span> off)</td>
</tr>
<tr>
<td><code>PumpPasses</code></td>
<td><span class="arithmatex">\(-1\)</span></td>
<td>passes of the feasibility pump at the root (<span class="arithmatex">\(-1\)</span> automatic)</td>
</tr>
<tr>
<td><code>SubMIPNodes</code></td>
<td><span class="arithmatex">\(500\)</span></td>
<td>node limit of the sub-MIPs of RINS and similar heuristics</td>
</tr>
<tr>
<td><code>NoRelHeurWork</code></td>
<td><span class="arithmatex">\(0\)</span></td>
<td>work spent in the heuristic without relaxation before the root</td>
</tr>
<tr>
<td><code>ImproveStartGap</code></td>
<td><span class="arithmatex">\(0\)</span></td>
<td>when the gap reaches this value, switch to improving the incumbent only</td>
</tr>
</table></div>

- A heuristic solution computed outside the solver — by any of the methods of this chapter — can be passed to it as a <strong>MIP start</strong> (the attribute <code>Start</code> of the variables): the solver starts from it as incumbent, uses it for pruning and for RINS, and never returns anything worse.

- The numbers depend on the instance, on the version of the solver and on the machine; a single instance proves nothing. The lesson is qualitative: when the bound is strong, the time to solve is the time to find a good solution, and the heuristics of the solver — or a good MIP start — decide it.

## 10.9 What MIP-based heuristics give { #sez-sec-give }

- A heuristic gives only one side of the gap, and without a dual bound nothing can be said about the quality of its solution. A MIP-based heuristic works with a MIP model, and the model comes with its LP relaxation; run inside a MIP solver, it comes with the dual bound of the branch-and-bound as well. The heuristic <strong>knows how far it is from the optimum</strong> while it runs: the optimality gap

    $$
    \text{optimality gap} = \frac{\UB - \LB}{|\LB|}
    $$

    is computed at every moment, and when it is $0$ the solution is <em>proved</em> optimal.

<div class="tabella" markdown="0"><table>
<tr>
<th>instance</th>
<th>solution</th>
<th><span class="arithmatex">\(\LB\)</span></th>
<th><span class="arithmatex">\(\UB=\lfloor z(\LP)\rfloor\)</span></th>
<th>gap</th>
<th>certified by</th>
</tr>
<tr>
<td>car, rounding down</td>
<td><span class="arithmatex">\((3,2)\)</span></td>
<td><span class="arithmatex">\(13\)</span></td>
<td><span class="arithmatex">\(15\)</span></td>
<td><span class="arithmatex">\(15.4\%\)</span></td>
<td>—</td>
</tr>
<tr>
<td>car, diving</td>
<td><span class="arithmatex">\((4,1)\)</span></td>
<td><span class="arithmatex">\(14\)</span></td>
<td><span class="arithmatex">\(15\)</span></td>
<td><span class="arithmatex">\(7.1\%\)</span></td>
<td>proximity search from <span class="arithmatex">\((3,2)\)</span></td>
</tr>
<tr>
<td><span class="arithmatex">\(P7\)</span>, rounding down</td>
<td><span class="arithmatex">\((2,0)\)</span></td>
<td><span class="arithmatex">\(6\)</span></td>
<td><span class="arithmatex">\(7\)</span></td>
<td><span class="arithmatex">\(16.7\%\)</span></td>
<td>—</td>
</tr>
<tr>
<td><span class="arithmatex">\(P7\)</span>, local branching</td>
<td><span class="arithmatex">\((2,1)\)</span></td>
<td><span class="arithmatex">\(7\)</span></td>
<td><span class="arithmatex">\(7\)</span></td>
<td><span class="arithmatex">\(0\%\)</span></td>
<td>the bound</td>
</tr>
<tr>
<td><span class="arithmatex">\(P42\)</span>, RINS</td>
<td><span class="arithmatex">\((2,2,0)\)</span></td>
<td><span class="arithmatex">\(10\)</span></td>
<td><span class="arithmatex">\(10\)</span></td>
<td><span class="arithmatex">\(0\%\)</span></td>
<td>the bound</td>
</tr>
<tr>
<td><span class="arithmatex">\(P46\)</span>, relax-and-fix</td>
<td><span class="arithmatex">\((0,2,0)\)</span></td>
<td><span class="arithmatex">\(12\)</span></td>
<td><span class="arithmatex">\(19\)</span></td>
<td><span class="arithmatex">\(58.3\%\)</span></td>
<td>—</td>
</tr>
<tr>
<td><span class="arithmatex">\(P46\)</span>, fix-and-optimize</td>
<td><span class="arithmatex">\((0,1,1)\)</span></td>
<td><span class="arithmatex">\(16\)</span></td>
<td><span class="arithmatex">\(19\)</span></td>
<td><span class="arithmatex">\(18.8\%\)</span></td>
<td>proximity search</td>
</tr>
<tr>
<td><span class="arithmatex">\(P72\)</span>, feasibility pump</td>
<td><span class="arithmatex">\((0,0,1,3,1)\)</span></td>
<td><span class="arithmatex">\(2\)</span></td>
<td><span class="arithmatex">\(8\)</span></td>
<td><span class="arithmatex">\(300\%\)</span></td>
<td>—</td>
</tr>
<tr>
<td><span class="arithmatex">\(P72\)</span>, local branching</td>
<td><span class="arithmatex">\((0,1,1,2,0)\)</span></td>
<td><span class="arithmatex">\(7\)</span></td>
<td><span class="arithmatex">\(8\)</span></td>
<td><span class="arithmatex">\(14.3\%\)</span></td>
<td>proximity search</td>
</tr>
</table></div>

- On $P7$ and $P42$ the integer rounding of the LP value is already the optimal value: the difficulty is to find a solution that attains the bound, not to prove it. On the other instances the bound $\lfloor z(\LP)\rfloor$ is not tight, and the certificate comes from proximity search ([Proposition 10.12](#box-21-prop-ps)) or from the branch-and-bound of the solver.

!!! chiave ""

    A MIP-based heuristic does not only find solutions: inside a MIP solver it works with a dual bound, so the gap of its solution is always known, and a gap equal to $0$ certifies optimality.

- The methods of this chapter, in summary:

<div class="tabella" markdown="0"><table>
<tr>
<th>method</th>
<th>needs LP</th>
<th>needs incumbent</th>
<th>fixing</th>
<th>idea</th>
</tr>
<tr>
<td>rounding</td>
<td>yes</td>
<td>no</td>
<td>—</td>
<td>round the LP solution</td>
</tr>
<tr>
<td>diving</td>
<td>yes</td>
<td>guided: yes</td>
<td>one at a time</td>
<td>fix a variable, re-solve the LP, repeat</td>
</tr>
<tr>
<td>feasibility pump</td>
<td>yes</td>
<td>no</td>
<td>—</td>
<td>alternate rounding and projection until they meet</td>
</tr>
<tr>
<td>RINS</td>
<td>yes</td>
<td>yes</td>
<td>hard</td>
<td>fix where LP and incumbent agree, solve the sub-MIP</td>
</tr>
<tr>
<td>local branching</td>
<td>no</td>
<td>yes</td>
<td>soft</td>
<td>sub-MIP with <span class="arithmatex">\(\Delta(\vet x,\bar{\vet x})\le k\)</span></td>
</tr>
<tr>
<td>proximity search</td>
<td>no</td>
<td>yes</td>
<td>soft</td>
<td>minimize <span class="arithmatex">\(\Delta(\vet x,\bar{\vet x})\)</span> subject to the cutoff</td>
</tr>
<tr>
<td>relax-and-fix</td>
<td>yes</td>
<td>no</td>
<td>by blocks</td>
<td>integer now, relaxed later, then fix</td>
</tr>
<tr>
<td>fix-and-optimize</td>
<td>no</td>
<td>yes</td>
<td>by windows</td>
<td>re-optimize a window, the rest fixed</td>
</tr>
</table></div>

## 10.10 <code>Python</code> implementation { #sez-sec-python }

- The codes use Python 3 and <code>gurobipy</code>. The variables of a model are accessed by their position $j$ in <code>m.getVars()</code>, and the list <code>I</code> of the positions of the integer variables is the set $\mathscr{D}$; indices start from $0$.

- The module <code>common.py</code> contains the environment of the solver and the operations used by all the methods: the LP relaxation, the rounding $\rnd{\cdot}$, the completion of a point, the distance $\Delta$ and the cutoff constraint.

```python
import math
import gurobipy as gp
from gurobipy import GRB

EPS = 1e-6                                   # integrality and comparison tolerance

ENV = gp.Env(empty=True)                     # one silent, single-thread (deterministic) environment
ENV.setParam("OutputFlag", 0)
ENV.setParam("Threads", 1)
ENV.start()

def int_index(m):
    """positions j (in m.getVars()) of the integer and binary variables: the set I"""
    return [j for j, v in enumerate(m.getVars()) if v.VType != GRB.CONTINUOUS]

def nearest(v):
    """[v] = floor(v + 1/2): rounding to the nearest integer, 1/2 rounded up"""
    return math.floor(v + 0.5 + EPS)

def is_integral(v):
    return abs(v - round(v)) <= EPS

def solve_lp(m):
    """optimal solution of the LP relaxation of m (with its current bounds), None if infeasible"""
    m.update()
    r = m.relax()
    r.optimize()
    if r.Status != GRB.OPTIMAL:
        return None
    return [v.X for v in r.getVars()]

def obj_of(m, x):
    """value of the objective function of m at the point x"""
    e = m.getObjective()
    return e.getConstant() + sum(e.getCoeff(k) * x[e.getVar(k).index] for k in range(e.size()))

def round_point(x_star, I):
    """x_tilde = [x*]: round the integer components, copy the continuous ones"""
    x_tilde = list(x_star)
    for j in I:
        x_tilde[j] = nearest(x_star[j])
    return x_tilde

def evaluate(m, x_int):
    """fix the integer variables to x_int and optimize the continuous ones:
       returns (value, x), or None if the point cannot be completed to a feasible solution"""
    f = m.copy()
    V = f.getVars()
    for j in int_index(f):
        V[j].LB = V[j].UB = x_int[j]
    f.optimize()
    if f.Status != GRB.OPTIMAL:
        return None
    return f.ObjVal, [v.X for v in V]

def solve_mip(m):
    """optimal value and solution of the MIP m, None if infeasible"""
    m.optimize()
    if m.SolCount == 0:
        return None
    return m.ObjVal, [v.X for v in m.getVars()]

def distance(m, x_tilde, I):
    """the linear expression Delta(x, x_tilde) = sum_{j in I} |x_j - x_tilde_j| in the variables of m:
       x_j - l_j if x_tilde_j is at its lower bound, u_j - x_j if at its upper bound,
       otherwise an auxiliary variable d_j >= |x_j - x_tilde_j|"""
    V = m.getVars()
    expr = gp.LinExpr()
    for j in I:
        if x_tilde[j] == V[j].LB:
            expr += V[j] - x_tilde[j]
        elif x_tilde[j] == V[j].UB:
            expr += x_tilde[j] - V[j]
        else:
            d = m.addVar(name=f"d[{j}]")
            m.addConstr(d >= V[j] - x_tilde[j])
            m.addConstr(d >= x_tilde[j] - V[j])
            expr += d
    return expr

def delta(x, x_tilde, I):
    """value of Delta(x, x_tilde)"""
    return sum(abs(x[j] - x_tilde[j]) for j in I)

def add_cutoff(m, z, theta):
    """require a solution better than z by at least theta"""
    obj = m.getObjective()
    if m.ModelSense == GRB.MINIMIZE:
        m.addConstr(obj <= z - theta, name="cutoff")
    else:
        m.addConstr(obj >= z + theta, name="cutoff")

def note(trace, *items):
    """record a step of the algorithm (used to print the examples of the notes)"""
    if trace is not None:
        trace.append(items)
```

- The model of the examples (<code>models.py</code>): <code>max c x</code> subject to <code>A x &lt;= b</code>, with integer variables in $\Z_{\ge0}$ and optional upper bounds.

```python
from common import *

def build_mip(c, A, b, ub=None, name="MIP"):
    """max c x subject to A x <= b, 0 <= x <= ub, x integer"""
    m = gp.Model(name, env=ENV)
    n = len(c)
    x = m.addVars(n, lb=0, ub=GRB.INFINITY if ub is None else ub, vtype=GRB.INTEGER, name="x")
    m.setObjective(gp.quicksum(c[j] * x[j] for j in range(n)), GRB.MAXIMIZE)
    for i in range(len(b)):
        m.addConstr(gp.quicksum(A[i][j] * x[j] for j in range(n)) <= b[i], name=f"c{i}")
    m.update()
    return m
```

- Rounding and diving:

```python
def rounding(m, trace=None):
    I = int_index(m)
    x_star = solve_lp(m)                       # optimal solution of the LP relaxation
    note(trace, "LP", x_star)
    x_tilde = round_point(x_star, I)           # x_tilde_j = [x*_j] for every integer variable
    note(trace, "rounded", x_tilde)
    return evaluate(m, x_tilde)                # (value, x) if feasible, None otherwise
```

```python
def diving(m, rule="fractional", x_inc=None, trace=None):
    d = m.copy()                               # the bounds of the copy are changed, not those of m
    V = d.getVars()
    I = int_index(d)
    while True:
        x_star = solve_lp(d)                   # LP relaxation with the current bounds
        if x_star is None:
            note(trace, "LP infeasible")
            return None                        # the dive fails
        F = [j for j in I if not is_integral(x_star[j])]
        note(trace, "LP", obj_of(d, x_star), x_star)
        if not F:
            return evaluate(m, round_point(x_star, I))
        if rule == "fractional":               # the least fractional variable, to its nearest integer
            j = min(F, key=lambda k: round(abs(x_star[k] - nearest(x_star[k])), 9))
            v = nearest(x_star[j])
        else:                                  # guided: the variable closest to the incumbent, to its value
            j = min(F, key=lambda k: round(abs(x_star[k] - x_inc[k]), 9))
            v = x_inc[j]
        note(trace, "fix", V[j].VarName, v)
        V[j].LB = V[j].UB = v                  # fix x_j := v
```

- The feasibility pump:

```python
def projection(m, x_tilde, I):
    """x* = argmin { Delta(x, x_tilde) : x in P }, P the LP relaxation of m"""
    p = m.relax()
    p.setObjective(distance(p, x_tilde, I), GRB.MINIMIZE)
    p.optimize()
    return [v.X for v in p.getVars()][:m.NumVars]

def feasibility_pump(m, max_iter=100, seed=1, trace=None):
    I = int_index(m)
    x_star = solve_lp(m)                       # x* = optimal solution of the LP relaxation
    note(trace, "LP", x_star)
    if all(is_integral(x_star[j]) for j in I):
        return evaluate(m, x_star)
    x_tilde = round_point(x_star, I)           # x_tilde = [x*]
    note(trace, "round", x_tilde)
    history = [x_tilde]                        # the points x_tilde visited so far
    for it in range(1, max_iter + 1):
        x_star = projection(m, x_tilde, I)     # the point of P closest to x_tilde
        dist = delta(x_star, x_tilde, I)
        note(trace, "project", x_star, dist)
        if dist <= EPS:                        # x* = x_tilde is integer and feasible
            return evaluate(m, x_tilde)
        x_new = round_point(x_star, I)
        if all(x_new[j] == x_tilde[j] for j in I):          # short cycle: [x*] = x_tilde
            j = max(I, key=lambda k: (round(abs(x_star[k] - x_tilde[k]), 9), -k))
            x_new[j] += 1 if x_star[j] > x_tilde[j] else -1  # flip the most uncertain component
            note(trace, "flip", x_new, j)
        else:
            note(trace, "round", x_new)
        if any(all(x_new[j] == y[j] for j in I) for y in history[-3:]):   # long cycle
            for j in I:                        # restart: random flips (binary variables)
                seed = (16807 * seed) % 2147483647
                rho = seed / 2147483647 - 0.3  # rho_j uniform in [-0.3, 0.7]
                if abs(x_star[j] - x_new[j]) + max(rho, 0) > 0.5:
                    x_new[j] = 1 - x_new[j]
            note(trace, "restart", x_new)
        x_tilde = x_new
        history.append(x_tilde)
    return None                                # no feasible solution found
```

- RINS, local branching and proximity search:

```python
def rins(m, x_tilde, theta=1, trace=None):
    I = int_index(m)
    z_tilde = obj_of(m, x_tilde)               # value of the incumbent
    x_star = solve_lp(m)                       # optimal solution of the LP relaxation
    s = m.copy()
    V = s.getVars()
    fixed = []
    for j in I:
        if abs(x_star[j] - x_tilde[j]) <= EPS:     # LP and incumbent agree on x_j
            V[j].LB = V[j].UB = x_tilde[j]
            fixed.append(j)
    note(trace, "LP", x_star, "fixed", [V[j].VarName for j in fixed])
    add_cutoff(s, z_tilde, theta)              # look only for better solutions
    res = solve_mip(s)                         # the sub-MIP on the free variables
    if res is None:
        note(trace, "sub-MIP infeasible")
        return z_tilde, x_tilde
    note(trace, "sub-MIP", res[0])
    return res
```

```python
def local_branching(m, x_tilde, k, k_max, k_step, theta=1, trace=None):
    I = int_index(m)
    w = m.copy()                               # the master MIP, which receives the reverse constraints
    z_tilde = obj_of(m, x_tilde)
    while k <= k_max:
        s = w.copy()
        s.addConstr(distance(s, x_tilde, I) <= k, name="local_branch")    # Delta(x, x_tilde) <= k
        add_cutoff(s, z_tilde, theta)
        res = solve_mip(s)
        w.addConstr(distance(w, x_tilde, I) >= k + 1)                     # never explore it again
        if res is not None:                    # better solution: re-center the neighbourhood
            note(trace, "improved", k, res[0], res[1])
            z_tilde, x_tilde = res
        else:                                  # no better solution within distance k: enlarge it
            note(trace, "no improvement", k)
            k += k_step
    return z_tilde, x_tilde
```

```python
def proximity_search(m, x_tilde, theta=1, trace=None):
    I = int_index(m)
    z_tilde = obj_of(m, x_tilde)
    while True:
        p = m.copy()
        add_cutoff(p, z_tilde, theta)          # better than the incumbent by at least theta
        p.setObjective(distance(p, x_tilde, I), GRB.MINIMIZE)   # as close as possible to it
        res = solve_mip(p)
        if res is None:                        # no solution better than z_tilde - theta exists
            note(trace, "infeasible")
            return z_tilde, x_tilde
        z_new, x_new = evaluate(m, res[1])     # value of the new solution, continuous part re-optimized
        note(trace, "found", res[0], z_new, x_new)
        z_tilde, x_tilde = z_new, x_new
```

- Relax-and-fix and fix-and-optimize:

```python
def relax_and_fix(m, blocks, trace=None):
    r = m.copy()
    V = {v.VarName: v for v in r.getVars()}
    vtype = {name: V[name].VType for B in blocks for name in B}
    for B in blocks:                           # at first, all the blocks are relaxed
        for name in B:
            V[name].VType = GRB.CONTINUOUS
    for B in blocks:
        for name in B:                         # the current block is integer again
            V[name].VType = vtype[name]
        res = solve_mip(r)
        if res is None:
            note(trace, "infeasible")
            return None
        note(trace, "block", B, res[0], {n: V[n].X for BB in blocks for n in BB})
        for name in B:                         # fix the current block to its value
            V[name].LB = V[name].UB = round(V[name].X)
    return res
```

```python
def fix_and_optimize(m, x_tilde, windows, theta=1, trace=None):
    I = int_index(m)
    names = [v.VarName for v in m.getVars()]
    z_tilde = obj_of(m, x_tilde)
    improved = True
    while improved:                            # one pass over all the windows
        improved = False
        for W in windows:
            s = m.copy()
            V = s.getVars()
            for j in I:
                if names[j] not in W:          # outside the window: fixed to the incumbent
                    V[j].LB = V[j].UB = x_tilde[j]
            add_cutoff(s, z_tilde, theta)
            res = solve_mip(s)
            if res is not None:
                z_tilde, x_tilde = res
                improved = True
            note(trace, "window", W, z_tilde)
    return z_tilde, x_tilde
```

- All the functions of this section are in the folder <code>MIP\_HEUR\_CODE</code>, together with <code>instances.py</code>, which contains the instances of the chapter and of the exercises. The command <code>python3 run\_tests.py</code> runs every example of the chapter and every instance of the exercises, checking each value against the expected value, written in the file.

## 10.11 Typical mistakes { #sez-sec-mistakes }

!!! attenzione "Warning"

    - <strong>Rounding a problem with equality constraints.</strong> Independent roundings of the variables of an equality constraint almost never satisfy it: rounding fails, and a dive is needed.

    - <strong>Believing that a dive can undo a decision.</strong> A dive is one path of the tree, without backtracking: a wrong fixing is never revised, and a fixing that makes the LP infeasible ends the dive.

    - <strong>Using the feasibility pump to get a good solution.</strong> After the first LP the pump ignores the objective: on $P72$ it returns a solution of value $2$, against the optimal value $7$.

    - <strong>Trusting hard fixing.</strong> RINS fixes the variables on which the LP and the incumbent agree, so it cannot find a better solution that changes one of them ($P7$).

    - <strong>Forgetting the cutoff constraint.</strong> Without $f(\vet x)\le f(\bar{\vet x})-\theta$ the sub-MIP returns the incumbent solution again; with it, an infeasible sub-MIP proves a local optimum.

    - <strong>Claiming optimality without the conditions.</strong> [Proposition 10.12](#box-21-prop-ps) needs $\theta=1$ and integer objective values on all the feasible solutions.

    - <strong>Ignoring the myopia of relax-and-fix.</strong> The relaxed blocks are optimistic: a decision that looks cheap can force a bad one later ($P46$).

    - <strong>Generalizing from one instance or one solver run.</strong> Paths and times depend on the version of the solver and on the tie-breaking rules, so the lessons are qualitative.

## 10.12 In summary { #sez-sec-summary }

!!! chiave "In summary"

    - MIP-based heuristics use the LP relaxation to decide where to look and a MIP solver to explore a large neighbourhood exactly.

    - Rounding and diving build a solution from the LP; the feasibility pump looks only for feasibility; RINS, local branching and proximity search improve an incumbent solution with a sub-MIP.

    - Relax-and-fix builds a solution block by block, fix-and-optimize improves it window by window.

    - Inside a MIP solver the dual bound is available: the gap of the heuristic solution is known, and a gap equal to $0$ certifies optimality.

    <strong>Dashboard of the car example.</strong> The lower and upper bounds on $z(\ILP)$ found so far in this module:

    <div class="tabella" markdown="0"><table>
    <tr>
    <th>Chapter</th>
    <th>Lower bound</th>
    <th>Upper bound</th>
    <th>From</th>
    </tr>
    <tr>
    <td>Integer linear programming</td>
    <td><span class="arithmatex">\(12\)</span>, <span class="arithmatex">\(14\)</span></td>
    <td>–</td>
    <td>heuristic solutions <span class="arithmatex">\((4, 0)\)</span> and <span class="arithmatex">\((4, 1)\)</span></td>
    </tr>
    <tr>
    <td>LP relaxation and dual bounds</td>
    <td>–</td>
    <td><span class="arithmatex">\(15\)</span></td>
    <td><span class="arithmatex">\(\lfloor z(\LP) \rfloor\)</span>, with <span class="arithmatex">\(z(\LP) = \tfrac{31}{2}\)</span></td>
    </tr>
    <tr>
    <td>Cutting plane algorithm</td>
    <td><span class="arithmatex">\(14\)</span></td>
    <td><span class="arithmatex">\(14\)</span></td>
    <td><span class="arithmatex">\(5\)</span> Gomory fractional cuts</td>
    </tr>
    <tr>
    <td>LP-based branch-and-bound</td>
    <td><span class="arithmatex">\(14\)</span></td>
    <td><span class="arithmatex">\(14\)</span></td>
    <td><span class="arithmatex">\(6\)</span> LP problems solved</td>
    </tr>
    <tr>
    <td>This chapter, rounding down</td>
    <td><span class="arithmatex">\(13\)</span></td>
    <td><span class="arithmatex">\(15\)</span></td>
    <td><span class="arithmatex">\((3,2)\)</span></td>
    </tr>
    <tr>
    <td>This chapter, diving</td>
    <td><span class="arithmatex">\(14\)</span></td>
    <td><span class="arithmatex">\(15\)</span></td>
    <td><span class="arithmatex">\((4,1)\)</span>, certified optimal by proximity search</td>
    </tr>
    </table></div>

## References

<a id="ref-AB07"></a>**[1]** T. Achterberg, T. Berthold, Improving the feasibility pump, <em>Discrete Optimization</em> 4 (2007) 77–86.

<a id="ref-BFL07"></a>**[2]** L. Bertacco, M. Fischetti, A. Lodi, A feasibility pump heuristic for general mixed-integer problems, <em>Discrete Optimization</em> 4 (2007) 63–76.

<a id="ref-DRL05"></a>**[3]** E. Danna, E. Rothberg, C. Le Pape, Exploring relaxation induced neighborhoods to improve MIP solutions, <em>Mathematical Programming</em> 102 (2005) 71–90.

<a id="ref-FGL05"></a>**[4]** M. Fischetti, F. Glover, A. Lodi, The feasibility pump, <em>Mathematical Programming</em> 104 (2005) 91–104.

<a id="ref-FL03"></a>**[5]** M. Fischetti, A. Lodi, Local branching, <em>Mathematical Programming</em> 98 (2003) 23–47.

<a id="ref-FM14"></a>**[6]** M. Fischetti, M. Monaci, Proximity search for 0-1 mixed-integer convex programming, <em>Journal of Heuristics</em> 20 (2014) 709–731.
