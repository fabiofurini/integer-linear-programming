---
title: "Exercises · LP-based branch-and-bound"
---

# Exercises · LP-based branch-and-bound

<div class="info-capitolo" markdown>

**Exercises · Chapter 7** · each solution opens with a click · [:material-book-open-variant: Chapter 7](../exact-methods/bb.md)

</div>

## Branch-and-bound algorithm

<a id="esercizio-7-1"></a>

!!! esercizio "Exercise 7.1: Branch-and-bound algorithm (P1)"

    !!! nota ""

        Consider the following ILP problem in canonical maximization form with $n=2$ integer variables and $m=1$ constraint:

        $$
        \begin{array}{rrrrrrr}
        {\max} & {x_1} & {+} & {8}\, {x_2} \\[2ex]
        & {3}\, {x_1} & {+} & {6}\, {x_2} & {\le} & {16} \\[2ex]
        & {x_1,} &  & {x_2} & {\in} & {\Z_{\ge 0}}
        \end{array}
        $$

        and an optimal simplex dictionary of its LP relaxation in standard form:

        \begin{equation*}
        \begin{cases}
        \begin{array}{rrrrrrr}
        {x_2} & {=} & {\dfrac{8}{3}} & {-} & {\dfrac{1}{2}}\, {x_1} & {-} & {\dfrac{1}{6}}\, {x_3} \\[2ex]
        {z} & {=} & {\dfrac{64}{3}} & {-} & {3}\, {x_1} & {-} & {\dfrac{4}{3}}\, {x_3}
        \end{array}
        \end{cases}
        \end{equation*}

    **Questions**

    1. Execute the branch-and-bound algorithm using:

        1. the smallest-index rule as branching-variable selection strategy;

        2. the LIFO rule as branching-node selection strategy;

        3. solve first the down-branching node and then the up-branching node;

        4. set the lower bound $\LB$ to $-\infty$ (no heuristic algorithm is executed).

        Solve the LP problems of the branching nodes using the dual simplex algorithm. In the dual simplex algorithm, the leaving variable is the basic variable with negative value and smallest index, and ties in the ratio test are broken by the smallest index. Show graphically the optimal solution of the LP problems associated with the branching nodes.

    2. Determine and comment the branch-and-bound tree, showing the reason why each branching node is pruned.

??? soluzione "Solution"

    1. Execution of the branch-and-bound algorithm to solve the $\ILP$ problem.

        - <span style="color:#e03131"><strong>Initialization</strong></span>

            Let us consider the $\LP_{1}$ problem in standard form:

            $$
            \begin{array}{rrrrrrrrr}
            {\max} & {x_1} & {+} & {8}\, {x_2} &  &  \\[2ex]
            & {3}\, {x_1} & {+} & {6}\, {x_2} & {+} & {x_3} & {=} & {16} \\[2ex]
            & {x_1,} &  & {x_2,} &  & {x_3} & {\ge} & {0}
            \end{array}
            $$

            and its optimal simplex dictionary:

            \begin{equation*}
            \begin{cases}
            \begin{array}{rrrrrrr}
            {x_2} & {=} & {\dfrac{8}{3}} & {-} & {\dfrac{1}{2}}\, {x_1} & {-} & {\dfrac{1}{6}}\, {x_3} \\[2ex]
            {z} & {=} & {\dfrac{64}{3}} & {-} & {3}\, {x_1} & {-} & {\dfrac{4}{3}}\, {x_3}
            \end{array}
            \end{cases}
            \end{equation*}

            ![Figure 1](../img/ex-07-bb/fig01.svg){ .fig .ovale loading=lazy  }

            <em>Node 1: feasible region of $\LP_{1}$ (red), feasible points of the ILP problem (black) and optimal solution of $\LP_{1}$ (blue) with the objective function line through it.</em>

            An optimal solution ${\tilde{\boldsymbol x}}(\LP_{1})$ is:

            $$
            \tilde{x}_1=0,~~\tilde{x}_2=\dfrac{8}{3}
            $$

            the optimal value is:

            $$
            z(\LP_{1}) = \dfrac{64}{3}
            $$

            The optimal solution ${\tilde{\boldsymbol x}}(\LP_{1})$ is <strong>fractional</strong> and the $\LP_{1}$ problem is <strong>feasible</strong>, so the $\LP_{1}$ problem is inserted in the problem list $L$ and the lower bound $\LB$ is set to $-\infty$.

            Due to the current lower bound $\LB$ and upper bound $\UB$, we have:

            $$
            -\infty ~~\le~~ z(\ILP) ~~\le~~ \underbrace{\left\lfloor \dfrac{64}{3} \right\rfloor}_{=21}
            $$

        - <span style="color:#e03131"><strong>Iteration 1</strong></span>

            The $\LP_{1}$ problem is extracted and deleted from the problem list $L$.

            The branching variable is $x_{2}$. The branching constraints are:

            $$
            x_2 \le \underbrace{\left\lfloor \dfrac{8}{3} \right\rfloor}_{ = 2} \mathrm{~~~and~~~} x_2 \ge \underbrace{\left\lfloor \dfrac{8}{3} \right\rfloor +1}_{= 3}
            $$

            Two $\LP_{2}$ and $\LP_{3}$ problems are created with these branching constraints.

            1. Solution of the $\LP_{2}$ problem

                Let us consider the $\LP_{2}$ problem in standard form:

                $$
                \begin{array}{rrrrrrrrrrr}
                {\max} & {x_1} & {+} & {8}\, {x_2} &  &  &  &  \\[2ex]
                & {3}\, {x_1} & {+} & {6}\, {x_2} & {+} & {x_3} &  &  & {=} & {16} \\[2ex]
                &  &  & {x_2} &  &  & {+} & {x_4} & {=} & {2} \\[2ex]
                & {x_1,} &  & {x_2,} &  & {x_3,} &  & {x_4} & {\ge} & {0}
                \end{array}
                $$

                For the branching constraint we have:

                $$
                x_2 + x_4 = 2 \quad \quad \Rightarrow \quad x_4 = 2 - x_2
                $$

                Using the equations of the optimal simplex dictionary we can express this branching constraint as a function of the non-basic variables. Substituting, we get

                $$
                x_4 = 2 - \left(\dfrac{8}{3} - \dfrac{1}{2} \; x_1 - \dfrac{1}{6} \; x_3 \right)
                \quad \quad \Rightarrow \quad
                x_4 = -\dfrac{2}{3} + \dfrac{1}{2} \, x_1 + \dfrac{1}{6} \, x_3
                $$

                We then add this equation to the previous optimal simplex dictionary and we obtain the following simplex dictionary:

                \begin{equation*}
                \begin{cases}
                \begin{array}{rrrrrrr}
                {x_2} & {=} & {\dfrac{8}{3}} & {-} & {\dfrac{1}{2}}\, {x_1} & {-} & {\dfrac{1}{6}}\, {x_3} \\[2ex]
                {x_4} & {=} & {-\dfrac{2}{3}} & {+} & {\dfrac{1}{2}}\, {x_1} & {+} & {\dfrac{1}{6}}\, {x_3} \\[2ex]
                {z} & {=} & {\dfrac{64}{3}} & {-} & {3}\, {x_1} & {-} & {\dfrac{4}{3}}\, {x_3}
                \end{array}
                \end{cases}
                \end{equation*}

                where $\pazocal{B}=\{2,4\}$ and $\pazocal{N}=\{1,3\}$ and

                - the basic primal non-feasible solution is:

                    $$
                    \bar x_2=\dfrac{8}{3},~~
                    \bar x_4=-\dfrac{2}{3},~~~~~~
                    \bar x_1=0,~~
                    \bar x_3=0~~
                    \mathrm{~~of~value~~~} \dfrac{64}{3}
                    $$

                - the basic dual feasible solution is:

                    $$
                    \bar \pi_2=0,~~
                    \bar \pi_4=0,~~~~~~
                    \bar \pi_1=3,~~
                    \bar \pi_3=\dfrac{4}{3}~~
                    \mathrm{~~of~value~~~} \dfrac{64}{3}
                    $$

                If we increase the value of $\pi_{4}$, we can get a better dual solution. We have:

                $$
                \begin{cases}
                \pi_1 &= 3 - \dfrac{1}{2}\,\pi_4 \\[2ex]
                \pi_3 &= \dfrac{4}{3} - \dfrac{1}{6}\,\pi_4
                \end{cases}
                \qquad
                \qquad
                \begin{cases}
                \underbrace{ 3 - \dfrac{1}{2}\,\pi_4 \ge 0}_{\pi_1 \ge 0} \\[2ex]
                \underbrace{ \dfrac{4}{3} - \dfrac{1}{6}\,\pi_4 \ge 0}_{\pi_3 \ge 0}
                \end{cases}
                \qquad
                \qquad
                \begin{cases}
                \pi_4 &\le 6 \\[2ex]
                \pi_4 &\le 8
                \end{cases}
                $$

                By increasing the value of $\pi_{4}$ to $\min \left\{6, 8\right\}=6$, the value of $\pi_{1}$ decreases to $0$. Then we have that:

                - $\pi_{4}$ becomes a dual basic variable and accordingly $x_{4}$ becomes a primal non-basic variable.

                - $\pi_{1}$ becomes a dual non-basic variable and accordingly $x_{1}$ becomes a primal basic variable.

                We have:

                $$
                x_4 = -\dfrac{2}{3} + \dfrac{1}{2} \, x_1 + \dfrac{1}{6} \, x_3
                \quad \Rightarrow \quad
                x_1 = \dfrac{4}{3} - \dfrac{1}{3} \, x_3 + 2 \, x_4
                $$

                Substituting into the other equations of the primal simplex dictionary, we get:

                $$
                x_2
                = \dfrac{8}{3} - \dfrac{1}{2} \left( \dfrac{4}{3} - \dfrac{1}{3} \, x_3 + 2 \, x_4 \right) - \dfrac{1}{6} \; x_3
                = 2 - x_4
                $$

                $$
                z
                = \dfrac{64}{3} - 3 \left( \dfrac{4}{3} - \dfrac{1}{3} \, x_3 + 2 \, x_4 \right) - \dfrac{4}{3} \; x_3
                = \dfrac{52}{3} - \dfrac{1}{3} \, x_3 - 6 \, x_4
                $$

                Accordingly, we now have the following updated primal simplex dictionary:

                \begin{equation*}
                \begin{cases}
                \begin{array}{rrrrrrr}
                {x_1} & {=} & {\dfrac{4}{3}} & {-} & {\dfrac{1}{3}}\, {x_3} & {+} & {2}\, {x_4} \\[2ex]
                {x_2} & {=} & {2} &  &  & {-} & {x_4} \\[2ex]
                {z} & {=} & {\dfrac{52}{3}} & {-} & {\dfrac{1}{3}}\, {x_3} & {-} & {6}\, {x_4}
                \end{array}
                \end{cases}
                \end{equation*}

                where $\pazocal{B}=\{1,2\}$ and $\pazocal{N}=\{3,4\}$ and

                - the basic primal optimal solution is:

                    $$
                    \tilde x_1=\dfrac{4}{3},~~
                    \tilde x_2=2,~~~~~~
                    \tilde x_3=0,~~
                    \tilde x_4=0~~
                    \mathrm{~~of~value~~~} \dfrac{52}{3}
                    $$

                - the basic dual optimal solution is:

                    $$
                    \tilde \pi_1=0,~~
                    \tilde \pi_2=0,~~~~~~
                    \tilde \pi_3=\dfrac{1}{3},~~
                    \tilde \pi_4=6~~
                    \mathrm{~~of~value~~~} \dfrac{52}{3}
                    $$

                ![Figure 2](../img/ex-07-bb/fig02.svg){ .fig .ovale loading=lazy  }

                <em>Node 2: feasible region of $\LP_{2}$ (red), with the branching constraint $x_2 \le 2$ (dashed), and its optimal solution (blue).</em>

                An optimal solution ${\tilde{\boldsymbol x}}(\LP_{2})$ is:

                $$
                \tilde{x}_1=\dfrac{4}{3},~~\tilde{x}_2=2
                $$

                the optimal value is:

                $$
                z(\LP_{2}) = \dfrac{52}{3}
                $$

                The $\LP_{2}$ problem is <strong>feasible</strong> and we have

                $$
                \left\lfloor z(\LP_{2}) \right\rfloor > \LB
                \qquad \text{since} \qquad
                \underbrace{\left\lfloor \dfrac{52}{3} \right\rfloor}_{=17} > -\infty
                $$

                The optimal solution ${\tilde{\boldsymbol x}}(\LP_{2})$ is <strong>fractional</strong>, accordingly, the $\LP_{2}$ problem is <strong>inserted in the problem list</strong> $L$.

            2. Solution of the $\LP_{3}$ problem

                Let us consider the $\LP_{3}$ problem in standard form:

                $$
                \begin{array}{rrrrrrrrrrr}
                {\max} & {x_1} & {+} & {8}\, {x_2} &  &  &  &  \\[2ex]
                & {3}\, {x_1} & {+} & {6}\, {x_2} & {+} & {x_3} &  &  & {=} & {16} \\[2ex]
                &  &  & {x_2} &  &  & {-} & {x_4} & {=} & {3} \\[2ex]
                & {x_1,} &  & {x_2,} &  & {x_3,} &  & {x_4} & {\ge} & {0}
                \end{array}
                $$

                For the branching constraint we have:

                $$
                x_2 - x_4 = 3 \quad \quad \Rightarrow \quad x_4 = -3 + x_2
                $$

                Using the equations of the optimal simplex dictionary we can express this branching constraint as a function of the non-basic variables. Substituting, we get

                $$
                x_4 = -3 + \left(\dfrac{8}{3} - \dfrac{1}{2} \; x_1 - \dfrac{1}{6} \; x_3 \right)
                \quad \quad \Rightarrow \quad
                x_4 = -\dfrac{1}{3} - \dfrac{1}{2} \, x_1 - \dfrac{1}{6} \, x_3
                $$

                We then add this equation to the previous optimal simplex dictionary and we obtain the following simplex dictionary:

                \begin{equation*}
                \begin{cases}
                \begin{array}{rrrrrrr}
                {x_2} & {=} & {\dfrac{8}{3}} & {-} & {\dfrac{1}{2}}\, {x_1} & {-} & {\dfrac{1}{6}}\, {x_3} \\[2ex]
                {x_4} & {=} & {-\dfrac{1}{3}} & {-} & {\dfrac{1}{2}}\, {x_1} & {-} & {\dfrac{1}{6}}\, {x_3} \\[2ex]
                {z} & {=} & {\dfrac{64}{3}} & {-} & {3}\, {x_1} & {-} & {\dfrac{4}{3}}\, {x_3}
                \end{array}
                \end{cases}
                \end{equation*}

                where $\pazocal{B}=\{2,4\}$ and $\pazocal{N}=\{1,3\}$ and

                - the basic primal non-feasible solution is:

                    $$
                    \bar x_2=\dfrac{8}{3},~~
                    \bar x_4=-\dfrac{1}{3},~~~~~~
                    \bar x_1=0,~~
                    \bar x_3=0~~
                    \mathrm{~~of~value~~~} \dfrac{64}{3}
                    $$

                - the basic dual feasible solution is:

                    $$
                    \bar \pi_2=0,~~
                    \bar \pi_4=0,~~~~~~
                    \bar \pi_1=3,~~
                    \bar \pi_3=\dfrac{4}{3}~~
                    \mathrm{~~of~value~~~} \dfrac{64}{3}
                    $$

                If we increase the value of $\pi_{4}$, we can get a better dual solution. We have:

                $$
                \begin{cases}
                \pi_1 &= 3 + \dfrac{1}{2}\,\pi_4 \\[2ex]
                \pi_3 &= \dfrac{4}{3} + \dfrac{1}{6}\,\pi_4
                \end{cases}
                \qquad
                \qquad
                \begin{cases}
                \underbrace{ 3 + \dfrac{1}{2}\,\pi_4 \ge 0}_{\pi_1 \ge 0} \\[2ex]
                \underbrace{ \dfrac{4}{3} + \dfrac{1}{6}\,\pi_4 \ge 0}_{\pi_3 \ge 0}
                \end{cases}
                \qquad
                \qquad
                \begin{cases}
                \pi_4 &\ge -6 \\[2ex]
                \pi_4 &\ge -8
                \end{cases}
                $$

                Accordingly, we can increase the value of $\pi_{4}$ without violating dual feasibility, which implies that the dual problem is <strong>unbounded</strong>. As a result, the primal problem $\LP_{3}$ is <strong>infeasible</strong>, and the corresponding branching node is <u>pruned by infeasibility</u>.

            At the end of the first iteration, branching on $x_{2}$ from Node 1 has generated the down-branching node, Node 2, and the up-branching node, Node 3. Node 2 is inserted in the list $L$, while Node 3 is pruned by infeasibility. The corresponding branch-and-bound tree is:

            ![Figure 3](../img/ex-07-bb/fig03.svg){ .fig loading=lazy  }

            Due to the current lower bound $\LB$ and upper bound $\UB$, we have:

            $$
            -\infty ~~\le~~ z(\ILP) ~~\le~~ \underbrace{\left\lfloor \dfrac{52}{3} \right\rfloor}_{=17}
            $$

        - <span style="color:#e03131"><strong>Iteration 2</strong></span>

            The $\LP_{2}$ problem is extracted and deleted from the problem list $L$.

            The branching variable is $x_{1}$. The branching constraints are:

            $$
            x_1 \le \underbrace{\left\lfloor \dfrac{4}{3} \right\rfloor}_{ = 1} \mathrm{~~~and~~~} x_1 \ge \underbrace{\left\lfloor \dfrac{4}{3} \right\rfloor +1}_{= 2}
            $$

            The $\LP_{4}$ problem is created with the first branching constraint (the second child is solved only if needed).

            1. Solution of the $\LP_{4}$ problem

                Let us consider the $\LP_{4}$ problem in standard form:

                $$
                \begin{array}{rrrrrrrrrrrrr}
                {\max} & {x_1} & {+} & {8}\, {x_2} &  &  &  &  &  &  \\[2ex]
                & {3}\, {x_1} & {+} & {6}\, {x_2} & {+} & {x_3} &  &  &  &  & {=} & {16} \\[2ex]
                &  &  & {x_2} &  &  & {+} & {x_4} &  &  & {=} & {2} \\[2ex]
                & {x_1} &  &  &  &  &  &  & {+} & {x_5} & {=} & {1} \\[2ex]
                & {x_1,} &  & {x_2,} &  & {x_3,} &  & {x_4,} &  & {x_5} & {\ge} & {0}
                \end{array}
                $$

                For the branching constraint we have:

                $$
                x_1 + x_5 = 1 \quad \quad \Rightarrow \quad x_5 = 1 - x_1
                $$

                Using the equations of the optimal simplex dictionary we can express this branching constraint as a function of the non-basic variables. Substituting, we get

                $$
                x_5 = 1 - \left(\dfrac{4}{3} - \dfrac{1}{3} \; x_3 + 2 \; x_4 \right)
                \quad \quad \Rightarrow \quad
                x_5 = -\dfrac{1}{3} + \dfrac{1}{3} \, x_3 - 2 \, x_4
                $$

                We then add this equation to the previous optimal simplex dictionary and we obtain the following simplex dictionary:

                \begin{equation*}
                \begin{cases}
                \begin{array}{rrrrrrr}
                {x_1} & {=} & {\dfrac{4}{3}} & {-} & {\dfrac{1}{3}}\, {x_3} & {+} & {2}\, {x_4} \\[2ex]
                {x_2} & {=} & {2} &  &  & {-} & {x_4} \\[2ex]
                {x_5} & {=} & {-\dfrac{1}{3}} & {+} & {\dfrac{1}{3}}\, {x_3} & {-} & {2}\, {x_4} \\[2ex]
                {z} & {=} & {\dfrac{52}{3}} & {-} & {\dfrac{1}{3}}\, {x_3} & {-} & {6}\, {x_4}
                \end{array}
                \end{cases}
                \end{equation*}

                where $\pazocal{B}=\{1,2,5\}$ and $\pazocal{N}=\{3,4\}$ and

                - the basic primal non-feasible solution is:

                    $$
                    \bar x_1=\dfrac{4}{3},~~
                    \bar x_2=2,~~
                    \bar x_5=-\dfrac{1}{3},~~~~~~
                    \bar x_3=0,~~
                    \bar x_4=0~~
                    \mathrm{~~of~value~~~} \dfrac{52}{3}
                    $$

                - the basic dual feasible solution is:

                    $$
                    \bar \pi_1=0,~~
                    \bar \pi_2=0,~~
                    \bar \pi_5=0,~~~~~~
                    \bar \pi_3=\dfrac{1}{3},~~
                    \bar \pi_4=6~~
                    \mathrm{~~of~value~~~} \dfrac{52}{3}
                    $$

                If we increase the value of $\pi_{5}$, we can get a better dual solution. We have:

                $$
                \begin{cases}
                \pi_3 &= \dfrac{1}{3} - \dfrac{1}{3}\,\pi_5 \\[2ex]
                \pi_4 &= 6 + 2\,\pi_5
                \end{cases}
                \qquad
                \qquad
                \begin{cases}
                \underbrace{ \dfrac{1}{3} - \dfrac{1}{3}\,\pi_5 \ge 0}_{\pi_3 \ge 0} \\[2ex]
                \underbrace{ 6 + 2\,\pi_5 \ge 0}_{\pi_4 \ge 0}
                \end{cases}
                \qquad
                \qquad
                \begin{cases}
                \pi_5 &\le 1 \\[2ex]
                \pi_5 &\ge -3
                \end{cases}
                $$

                By increasing the value of $\pi_{5}$ to $1$, the value of $\pi_{3}$ decreases to $0$. Then we have that:

                - $\pi_{5}$ becomes a dual basic variable and accordingly $x_{5}$ becomes a primal non-basic variable.

                - $\pi_{3}$ becomes a dual non-basic variable and accordingly $x_{3}$ becomes a primal basic variable.

                We have:

                $$
                x_5 = -\dfrac{1}{3} + \dfrac{1}{3} \, x_3 - 2 \, x_4
                \quad \Rightarrow \quad
                x_3 = 1 + 6 \, x_4 + 3 \, x_5
                $$

                Substituting into the other equations of the primal simplex dictionary, we get:

                $$
                x_1
                = \dfrac{4}{3} - \dfrac{1}{3} \left( 1 + 6 \, x_4 + 3 \, x_5 \right) + 2 \; x_4
                = 1 - x_5
                $$

                $$
                z
                = \dfrac{52}{3} - \dfrac{1}{3} \left( 1 + 6 \, x_4 + 3 \, x_5 \right) - 6 \; x_4
                = 17 - 8 \, x_4 - x_5
                $$

                Accordingly, we now have the following updated primal simplex dictionary:

                \begin{equation*}
                \begin{cases}
                \begin{array}{rrrrrrr}
                {x_1} & {=} & {1} &  &  & {-} & {x_5} \\[2ex]
                {x_2} & {=} & {2} & {-} & {x_4} &  &  \\[2ex]
                {x_3} & {=} & {1} & {+} & {6}\, {x_4} & {+} & {3}\, {x_5} \\[2ex]
                {z} & {=} & {17} & {-} & {8}\, {x_4} & {-} & {x_5}
                \end{array}
                \end{cases}
                \end{equation*}

                where $\pazocal{B}=\{1,2,3\}$ and $\pazocal{N}=\{4,5\}$ and

                - the basic primal optimal solution is:

                    $$
                    \tilde x_1=1,~~
                    \tilde x_2=2,~~
                    \tilde x_3=1,~~~~~~
                    \tilde x_4=0,~~
                    \tilde x_5=0~~
                    \mathrm{~~of~value~~~} 17
                    $$

                - the basic dual optimal solution is:

                    $$
                    \tilde \pi_1=0,~~
                    \tilde \pi_2=0,~~
                    \tilde \pi_3=0,~~~~~~
                    \tilde \pi_4=8,~~
                    \tilde \pi_5=1~~
                    \mathrm{~~of~value~~~} 17
                    $$

                ![Figure 4](../img/ex-07-bb/fig04.svg){ .fig .ovale loading=lazy  }

                <em>Node 4: feasible region of $\LP_{4}$ (red), with the branching constraint $x_1 \le 1$ (dashed), and its optimal solution (blue).</em>

                An optimal solution ${\tilde{\boldsymbol x}}(\LP_{4})$ is:

                $$
                \tilde{x}_1=1,~~\tilde{x}_2=2
                $$

                the optimal value is:

                $$
                z(\LP_{4}) = 17
                $$

                The $\LP_{4}$ problem is <strong>feasible</strong> and we have

                $$
                \left\lfloor z(\LP_{4}) \right\rfloor > \LB
                \qquad \text{since} \qquad
                17 > -\infty
                $$

                The optimal solution ${\tilde{\boldsymbol x}}(\LP_{4})$ is <strong>integer</strong>, accordingly, the lower bound $\LB$ becomes $17$ and the incumbent solution ${\bar{\boldsymbol x}}(\ILP)$ becomes $\bar{x}_1=1,\bar{x}_2=2$.

                Accordingly, the branching node is <u>pruned by integrality</u>.

            At the end of the second iteration, branching on $x_{1}$ from Node 2 has generated the down-branching node, Node 4. This node is pruned by integrality. The up-branching node is not solved, since after Node 4 the lower bound satisfies

            $$
            \left\lfloor z(\LP_{2}) \right\rfloor \le \LB.
            $$

            This is the portion of the branch-and-bound tree showing the LP problems of the branching nodes that are solved:

            ![Figure 5](../img/ex-07-bb/fig05.svg){ .fig loading=lazy  }

            Due to the current lower bound $\LB$ and upper bound $\UB$, we have:

            $$
            17 ~~\le~~ z(\ILP) ~~\le~~ 17
            $$

        - <span style="color:#e03131"><strong>Termination</strong></span>

            Since the list $L$ is empty, the lower bound $\LB$ is equal to the upper bound $\UB$ and the $\ILP$ problem is solved to optimality.

            The optimal value is $z(\ILP)=17$ (the value of the lower bound $\LB$) and an optimal solution ${\tilde{\boldsymbol x}}(\ILP)$ is $\tilde x_1=1,\tilde x_2=2$ (the incumbent solution ${\bar{\boldsymbol x}}(\ILP)$).

    2. The final branch-and-bound tree is:

        ![Figure 6](../img/ex-07-bb/fig06.svg){ .fig loading=lazy  }

        The reasons why the branching nodes are pruned are the following.

        - Node 1 has a fractional optimal solution with $\lfloor z(\LP_{1})\rfloor = 21$: it is branched on $x_2$.

        - Node 2 has a fractional optimal solution with $\lfloor z(\LP_{2})\rfloor = 17$: it is branched on $x_1$.

        - The up-branching node of Node 2 is not solved, and it is not drawn, since $\lfloor z(\LP_{2})\rfloor = 17$ $\le \LB = 17$ after its first child.

        - Node 3 is pruned by infeasibility: the $\LP_{3}$ problem has no feasible solution.

        - Node 4 is pruned by integrality: $\tilde{\boldsymbol x}(\LP_{4}) = (1, 2)$ is integer, with value $17$, which becomes the incumbent solution.

        The optimal value is $z(\ILP) = 17$ and the branch-and-bound algorithm returns the optimal solution $\tilde{\boldsymbol x}(\ILP) = (1, 2)$.

<a id="esercizio-7-2"></a>

!!! esercizio "Exercise 7.2: Branch-and-bound algorithm (P2)"

    !!! nota ""

        Consider the following ILP problem in canonical maximization form with $n=2$ integer variables and $m=2$ constraints:

        $$
        \begin{array}{rrrrrrr}
        {\max} & {x_1} & {+} & {3}\, {x_2} \\[2ex]
        & {-}{4}\, {x_1} & {+} & {10}\, {x_2} & {\le} & {35} \\[2ex]
        & {3}\, {x_1} & {+} & {x_2} & {\le} & {12} \\[2ex]
        & {x_1,} &  & {x_2} & {\in} & {\Z_{\ge 0}}
        \end{array}
        $$

        and an optimal simplex dictionary of its LP relaxation in standard form:

        \begin{equation*}
        \begin{cases}
        \begin{array}{rrrrrrr}
        {x_1} & {=} & {\dfrac{5}{2}} & {+} & {\dfrac{1}{34}}\, {x_3} & {-} & {\dfrac{5}{17}}\, {x_4} \\[2ex]
        {x_2} & {=} & {\dfrac{9}{2}} & {-} & {\dfrac{3}{34}}\, {x_3} & {-} & {\dfrac{2}{17}}\, {x_4} \\[2ex]
        {z} & {=} & {16} & {-} & {\dfrac{4}{17}}\, {x_3} & {-} & {\dfrac{11}{17}}\, {x_4}
        \end{array}
        \end{cases}
        \end{equation*}

    **Questions**

    1. Execute the branch-and-bound algorithm using:

        1. the smallest-index rule as branching-variable selection strategy;

        2. the FIFO rule as branching-node selection strategy;

        3. solve first the down-branching node and then the up-branching node;

        4. set the lower bound $\LB$ to $-\infty$ (no heuristic algorithm is executed).

        Solve the LP problems of the branching nodes using the dual simplex algorithm. In the dual simplex algorithm, the leaving variable is the basic variable with negative value and smallest index, and ties in the ratio test are broken by the smallest index. Show graphically the optimal solution of the LP problems associated with the branching nodes.

    2. Determine and comment the branch-and-bound tree, showing the reason why each branching node is pruned.

??? soluzione "Solution"

    1. Execution of the branch-and-bound algorithm to solve the $\ILP$ problem.

        - <span style="color:#e03131"><strong>Initialization</strong></span>

            Let us consider the $\LP_{1}$ problem in standard form:

            $$
            \begin{array}{rrrrrrrrrrr}
            {\max} & {x_1} & {+} & {3}\, {x_2} &  &  &  &  \\[2ex]
            & {-}{4}\, {x_1} & {+} & {10}\, {x_2} & {+} & {x_3} &  &  & {=} & {35} \\[2ex]
            & {3}\, {x_1} & {+} & {x_2} &  &  & {+} & {x_4} & {=} & {12} \\[2ex]
            & {x_1,} &  & {x_2,} &  & {x_3,} &  & {x_4} & {\ge} & {0}
            \end{array}
            $$

            and its optimal simplex dictionary:

            \begin{equation*}
            \begin{cases}
            \begin{array}{rrrrrrr}
            {x_1} & {=} & {\dfrac{5}{2}} & {+} & {\dfrac{1}{34}}\, {x_3} & {-} & {\dfrac{5}{17}}\, {x_4} \\[2ex]
            {x_2} & {=} & {\dfrac{9}{2}} & {-} & {\dfrac{3}{34}}\, {x_3} & {-} & {\dfrac{2}{17}}\, {x_4} \\[2ex]
            {z} & {=} & {16} & {-} & {\dfrac{4}{17}}\, {x_3} & {-} & {\dfrac{11}{17}}\, {x_4}
            \end{array}
            \end{cases}
            \end{equation*}

            ![Figure 7](../img/ex-07-bb/fig07.svg){ .fig .ovale loading=lazy  }

            <em>Node 1: feasible region of $\LP_{1}$ (red), feasible points of the ILP problem (black) and optimal solution of $\LP_{1}$ (blue) with the objective function line through it.</em>

            An optimal solution ${\tilde{\boldsymbol x}}(\LP_{1})$ is:

            $$
            \tilde{x}_1=\dfrac{5}{2},~~\tilde{x}_2=\dfrac{9}{2}
            $$

            the optimal value is:

            $$
            z(\LP_{1}) = 16
            $$

            The optimal solution ${\tilde{\boldsymbol x}}(\LP_{1})$ is <strong>fractional</strong> and the $\LP_{1}$ problem is <strong>feasible</strong>, so the $\LP_{1}$ problem is inserted in the problem list $L$ and the lower bound $\LB$ is set to $-\infty$.

            Due to the current lower bound $\LB$ and upper bound $\UB$, we have:

            $$
            -\infty ~~\le~~ z(\ILP) ~~\le~~ 16
            $$

        - <span style="color:#e03131"><strong>Iteration 1</strong></span>

            The $\LP_{1}$ problem is extracted and deleted from the problem list $L$.

            The branching variable is $x_{1}$. The branching constraints are:

            $$
            x_1 \le \underbrace{\left\lfloor \dfrac{5}{2} \right\rfloor}_{ = 2} \mathrm{~~~and~~~} x_1 \ge \underbrace{\left\lfloor \dfrac{5}{2} \right\rfloor +1}_{= 3}
            $$

            Two $\LP_{2}$ and $\LP_{3}$ problems are created with these branching constraints.

            1. Solution of the $\LP_{2}$ problem

                Let us consider the $\LP_{2}$ problem in standard form:

                $$
                \begin{array}{rrrrrrrrrrrrr}
                {\max} & {x_1} & {+} & {3}\, {x_2} &  &  &  &  &  &  \\[2ex]
                & {-}{4}\, {x_1} & {+} & {10}\, {x_2} & {+} & {x_3} &  &  &  &  & {=} & {35} \\[2ex]
                & {3}\, {x_1} & {+} & {x_2} &  &  & {+} & {x_4} &  &  & {=} & {12} \\[2ex]
                & {x_1} &  &  &  &  &  &  & {+} & {x_5} & {=} & {2} \\[2ex]
                & {x_1,} &  & {x_2,} &  & {x_3,} &  & {x_4,} &  & {x_5} & {\ge} & {0}
                \end{array}
                $$

                For the branching constraint we have:

                $$
                x_1 + x_5 = 2 \quad \quad \Rightarrow \quad x_5 = 2 - x_1
                $$

                Using the equations of the optimal simplex dictionary we can express this branching constraint as a function of the non-basic variables. Substituting, we get

                $$
                x_5 = 2 - \left(\dfrac{5}{2} + \dfrac{1}{34} \; x_3 - \dfrac{5}{17} \; x_4 \right)
                \quad \quad \Rightarrow \quad
                x_5 = -\dfrac{1}{2} - \dfrac{1}{34} \, x_3 + \dfrac{5}{17} \, x_4
                $$

                We then add this equation to the previous optimal simplex dictionary and we obtain the following simplex dictionary:

                \begin{equation*}
                \begin{cases}
                \begin{array}{rrrrrrr}
                {x_1} & {=} & {\dfrac{5}{2}} & {+} & {\dfrac{1}{34}}\, {x_3} & {-} & {\dfrac{5}{17}}\, {x_4} \\[2ex]
                {x_2} & {=} & {\dfrac{9}{2}} & {-} & {\dfrac{3}{34}}\, {x_3} & {-} & {\dfrac{2}{17}}\, {x_4} \\[2ex]
                {x_5} & {=} & {-\dfrac{1}{2}} & {-} & {\dfrac{1}{34}}\, {x_3} & {+} & {\dfrac{5}{17}}\, {x_4} \\[2ex]
                {z} & {=} & {16} & {-} & {\dfrac{4}{17}}\, {x_3} & {-} & {\dfrac{11}{17}}\, {x_4}
                \end{array}
                \end{cases}
                \end{equation*}

                where $\pazocal{B}=\{1,2,5\}$ and $\pazocal{N}=\{3,4\}$ and

                - the basic primal non-feasible solution is:

                    $$
                    \bar x_1=\dfrac{5}{2},~~
                    \bar x_2=\dfrac{9}{2},~~
                    \bar x_5=-\dfrac{1}{2},~~~~~~
                    \bar x_3=0,~~
                    \bar x_4=0~~
                    \mathrm{~~of~value~~~} 16
                    $$

                - the basic dual feasible solution is:

                    $$
                    \bar \pi_1=0,~~
                    \bar \pi_2=0,~~
                    \bar \pi_5=0,~~~~~~
                    \bar \pi_3=\dfrac{4}{17},~~
                    \bar \pi_4=\dfrac{11}{17}~~
                    \mathrm{~~of~value~~~} 16
                    $$

                If we increase the value of $\pi_{5}$, we can get a better dual solution. We have:

                $$
                \begin{cases}
                \pi_3 &= \dfrac{4}{17} + \dfrac{1}{34}\,\pi_5 \\[2ex]
                \pi_4 &= \dfrac{11}{17} - \dfrac{5}{17}\,\pi_5
                \end{cases}
                \qquad
                \qquad
                \begin{cases}
                \underbrace{ \dfrac{4}{17} + \dfrac{1}{34}\,\pi_5 \ge 0}_{\pi_3 \ge 0} \\[2ex]
                \underbrace{ \dfrac{11}{17} - \dfrac{5}{17}\,\pi_5 \ge 0}_{\pi_4 \ge 0}
                \end{cases}
                \qquad
                \qquad
                \begin{cases}
                \pi_5 &\ge -8 \\[2ex]
                \pi_5 &\le \dfrac{11}{5}
                \end{cases}
                $$

                By increasing the value of $\pi_{5}$ to $\dfrac{11}{5}$, the value of $\pi_{4}$ decreases to $0$. Then we have that:

                - $\pi_{5}$ becomes a dual basic variable and accordingly $x_{5}$ becomes a primal non-basic variable.

                - $\pi_{4}$ becomes a dual non-basic variable and accordingly $x_{4}$ becomes a primal basic variable.

                We have:

                $$
                x_5 = -\dfrac{1}{2} - \dfrac{1}{34} \, x_3 + \dfrac{5}{17} \, x_4
                \quad \Rightarrow \quad
                x_4 = \dfrac{17}{10} + \dfrac{1}{10} \, x_3 + \dfrac{17}{5} \, x_5
                $$

                Substituting into the other equations of the primal simplex dictionary, we get:

                $$
                x_1
                = \dfrac{5}{2} + \dfrac{1}{34} \; x_3 - \dfrac{5}{17} \left( \dfrac{17}{10} + \dfrac{1}{10} \, x_3 + \dfrac{17}{5} \, x_5 \right)
                = 2 - x_5
                $$

                $$
                x_2
                = \dfrac{9}{2} - \dfrac{3}{34} \; x_3 - \dfrac{2}{17} \left( \dfrac{17}{10} + \dfrac{1}{10} \, x_3 + \dfrac{17}{5} \, x_5 \right)
                = \dfrac{43}{10} - \dfrac{1}{10} \, x_3 - \dfrac{2}{5} \, x_5
                $$

                $$
                z
                = 16 - \dfrac{4}{17} \; x_3 - \dfrac{11}{17} \left( \dfrac{17}{10} + \dfrac{1}{10} \, x_3 + \dfrac{17}{5} \, x_5 \right)
                = \dfrac{149}{10} - \dfrac{3}{10} \, x_3 - \dfrac{11}{5} \, x_5
                $$

                Accordingly, we now have the following updated primal simplex dictionary:

                \begin{equation*}
                \begin{cases}
                \begin{array}{rrrrrrr}
                {x_1} & {=} & {2} &  &  & {-} & {x_5} \\[2ex]
                {x_2} & {=} & {\dfrac{43}{10}} & {-} & {\dfrac{1}{10}}\, {x_3} & {-} & {\dfrac{2}{5}}\, {x_5} \\[2ex]
                {x_4} & {=} & {\dfrac{17}{10}} & {+} & {\dfrac{1}{10}}\, {x_3} & {+} & {\dfrac{17}{5}}\, {x_5} \\[2ex]
                {z} & {=} & {\dfrac{149}{10}} & {-} & {\dfrac{3}{10}}\, {x_3} & {-} & {\dfrac{11}{5}}\, {x_5}
                \end{array}
                \end{cases}
                \end{equation*}

                where $\pazocal{B}=\{1,2,4\}$ and $\pazocal{N}=\{3,5\}$ and

                - the basic primal optimal solution is:

                    $$
                    \tilde x_1=2,~~
                    \tilde x_2=\dfrac{43}{10},~~
                    \tilde x_4=\dfrac{17}{10},~~~~~~
                    \tilde x_3=0,~~
                    \tilde x_5=0~~
                    \mathrm{~~of~value~~~} \dfrac{149}{10}
                    $$

                - the basic dual optimal solution is:

                    $$
                    \tilde \pi_1=0,~~
                    \tilde \pi_2=0,~~
                    \tilde \pi_4=0,~~~~~~
                    \tilde \pi_3=\dfrac{3}{10},~~
                    \tilde \pi_5=\dfrac{11}{5}~~
                    \mathrm{~~of~value~~~} \dfrac{149}{10}
                    $$

                ![Figure 8](../img/ex-07-bb/fig08.svg){ .fig .ovale loading=lazy  }

                <em>Node 2: feasible region of $\LP_{2}$ (red), with the branching constraint $x_1 \le 2$ (dashed), and its optimal solution (blue).</em>

                An optimal solution ${\tilde{\boldsymbol x}}(\LP_{2})$ is:

                $$
                \tilde{x}_1=2,~~\tilde{x}_2=\dfrac{43}{10}
                $$

                the optimal value is:

                $$
                z(\LP_{2}) = \dfrac{149}{10}
                $$

                The $\LP_{2}$ problem is <strong>feasible</strong> and we have

                $$
                \left\lfloor z(\LP_{2}) \right\rfloor > \LB
                \qquad \text{since} \qquad
                \underbrace{\left\lfloor \dfrac{149}{10} \right\rfloor}_{=14} > -\infty
                $$

                The optimal solution ${\tilde{\boldsymbol x}}(\LP_{2})$ is <strong>fractional</strong>, accordingly, the $\LP_{2}$ problem is <strong>inserted in the problem list</strong> $L$.

            2. Solution of the $\LP_{3}$ problem

                Let us consider the $\LP_{3}$ problem in standard form:

                $$
                \begin{array}{rrrrrrrrrrrrr}
                {\max} & {x_1} & {+} & {3}\, {x_2} &  &  &  &  &  &  \\[2ex]
                & {-}{4}\, {x_1} & {+} & {10}\, {x_2} & {+} & {x_3} &  &  &  &  & {=} & {35} \\[2ex]
                & {3}\, {x_1} & {+} & {x_2} &  &  & {+} & {x_4} &  &  & {=} & {12} \\[2ex]
                & {x_1} &  &  &  &  &  &  & {-} & {x_5} & {=} & {3} \\[2ex]
                & {x_1,} &  & {x_2,} &  & {x_3,} &  & {x_4,} &  & {x_5} & {\ge} & {0}
                \end{array}
                $$

                For the branching constraint we have:

                $$
                x_1 - x_5 = 3 \quad \quad \Rightarrow \quad x_5 = -3 + x_1
                $$

                Using the equations of the optimal simplex dictionary we can express this branching constraint as a function of the non-basic variables. Substituting, we get

                $$
                x_5 = -3 + \left(\dfrac{5}{2} + \dfrac{1}{34} \; x_3 - \dfrac{5}{17} \; x_4 \right)
                \quad \quad \Rightarrow \quad
                x_5 = -\dfrac{1}{2} + \dfrac{1}{34} \, x_3 - \dfrac{5}{17} \, x_4
                $$

                We then add this equation to the previous optimal simplex dictionary and we obtain the following simplex dictionary:

                \begin{equation*}
                \begin{cases}
                \begin{array}{rrrrrrr}
                {x_1} & {=} & {\dfrac{5}{2}} & {+} & {\dfrac{1}{34}}\, {x_3} & {-} & {\dfrac{5}{17}}\, {x_4} \\[2ex]
                {x_2} & {=} & {\dfrac{9}{2}} & {-} & {\dfrac{3}{34}}\, {x_3} & {-} & {\dfrac{2}{17}}\, {x_4} \\[2ex]
                {x_5} & {=} & {-\dfrac{1}{2}} & {+} & {\dfrac{1}{34}}\, {x_3} & {-} & {\dfrac{5}{17}}\, {x_4} \\[2ex]
                {z} & {=} & {16} & {-} & {\dfrac{4}{17}}\, {x_3} & {-} & {\dfrac{11}{17}}\, {x_4}
                \end{array}
                \end{cases}
                \end{equation*}

                where $\pazocal{B}=\{1,2,5\}$ and $\pazocal{N}=\{3,4\}$ and

                - the basic primal non-feasible solution is:

                    $$
                    \bar x_1=\dfrac{5}{2},~~
                    \bar x_2=\dfrac{9}{2},~~
                    \bar x_5=-\dfrac{1}{2},~~~~~~
                    \bar x_3=0,~~
                    \bar x_4=0~~
                    \mathrm{~~of~value~~~} 16
                    $$

                - the basic dual feasible solution is:

                    $$
                    \bar \pi_1=0,~~
                    \bar \pi_2=0,~~
                    \bar \pi_5=0,~~~~~~
                    \bar \pi_3=\dfrac{4}{17},~~
                    \bar \pi_4=\dfrac{11}{17}~~
                    \mathrm{~~of~value~~~} 16
                    $$

                If we increase the value of $\pi_{5}$, we can get a better dual solution. We have:

                $$
                \begin{cases}
                \pi_3 &= \dfrac{4}{17} - \dfrac{1}{34}\,\pi_5 \\[2ex]
                \pi_4 &= \dfrac{11}{17} + \dfrac{5}{17}\,\pi_5
                \end{cases}
                \qquad
                \qquad
                \begin{cases}
                \underbrace{ \dfrac{4}{17} - \dfrac{1}{34}\,\pi_5 \ge 0}_{\pi_3 \ge 0} \\[2ex]
                \underbrace{ \dfrac{11}{17} + \dfrac{5}{17}\,\pi_5 \ge 0}_{\pi_4 \ge 0}
                \end{cases}
                \qquad
                \qquad
                \begin{cases}
                \pi_5 &\le 8 \\[2ex]
                \pi_5 &\ge -\dfrac{11}{5}
                \end{cases}
                $$

                By increasing the value of $\pi_{5}$ to $8$, the value of $\pi_{3}$ decreases to $0$. Then we have that:

                - $\pi_{5}$ becomes a dual basic variable and accordingly $x_{5}$ becomes a primal non-basic variable.

                - $\pi_{3}$ becomes a dual non-basic variable and accordingly $x_{3}$ becomes a primal basic variable.

                We have:

                $$
                x_5 = -\dfrac{1}{2} + \dfrac{1}{34} \, x_3 - \dfrac{5}{17} \, x_4
                \quad \Rightarrow \quad
                x_3 = 17 + 10 \, x_4 + 34 \, x_5
                $$

                Substituting into the other equations of the primal simplex dictionary, we get:

                $$
                x_1
                = \dfrac{5}{2} + \dfrac{1}{34} \left( 17 + 10 \, x_4 + 34 \, x_5 \right) - \dfrac{5}{17} \; x_4
                = 3 + x_5
                $$

                $$
                x_2
                = \dfrac{9}{2} - \dfrac{3}{34} \left( 17 + 10 \, x_4 + 34 \, x_5 \right) - \dfrac{2}{17} \; x_4
                = 3 - x_4 - 3 \, x_5
                $$

                $$
                z
                = 16 - \dfrac{4}{17} \left( 17 + 10 \, x_4 + 34 \, x_5 \right) - \dfrac{11}{17} \; x_4
                = 12 - 3 \, x_4 - 8 \, x_5
                $$

                Accordingly, we now have the following updated primal simplex dictionary:

                \begin{equation*}
                \begin{cases}
                \begin{array}{rrrrrrr}
                {x_1} & {=} & {3} &  &  & {+} & {x_5} \\[2ex]
                {x_2} & {=} & {3} & {-} & {x_4} & {-} & {3}\, {x_5} \\[2ex]
                {x_3} & {=} & {17} & {+} & {10}\, {x_4} & {+} & {34}\, {x_5} \\[2ex]
                {z} & {=} & {12} & {-} & {3}\, {x_4} & {-} & {8}\, {x_5}
                \end{array}
                \end{cases}
                \end{equation*}

                where $\pazocal{B}=\{1,2,3\}$ and $\pazocal{N}=\{4,5\}$ and

                - the basic primal optimal solution is:

                    $$
                    \tilde x_1=3,~~
                    \tilde x_2=3,~~
                    \tilde x_3=17,~~~~~~
                    \tilde x_4=0,~~
                    \tilde x_5=0~~
                    \mathrm{~~of~value~~~} 12
                    $$

                - the basic dual optimal solution is:

                    $$
                    \tilde \pi_1=0,~~
                    \tilde \pi_2=0,~~
                    \tilde \pi_3=0,~~~~~~
                    \tilde \pi_4=3,~~
                    \tilde \pi_5=8~~
                    \mathrm{~~of~value~~~} 12
                    $$

                ![Figure 9](../img/ex-07-bb/fig09.svg){ .fig .ovale loading=lazy  }

                <em>Node 3: feasible region of $\LP_{3}$ (red), with the branching constraint $x_1 \ge 3$ (dashed), and its optimal solution (blue).</em>

                An optimal solution ${\tilde{\boldsymbol x}}(\LP_{3})$ is:

                $$
                \tilde{x}_1=3,~~\tilde{x}_2=3
                $$

                the optimal value is:

                $$
                z(\LP_{3}) = 12
                $$

                The $\LP_{3}$ problem is <strong>feasible</strong> and we have

                $$
                \left\lfloor z(\LP_{3}) \right\rfloor > \LB
                \qquad \text{since} \qquad
                12 > -\infty
                $$

                The optimal solution ${\tilde{\boldsymbol x}}(\LP_{3})$ is <strong>integer</strong>, accordingly, the lower bound $\LB$ becomes $12$ and the incumbent solution ${\bar{\boldsymbol x}}(\ILP)$ becomes $\bar{x}_1=3,\bar{x}_2=3$.

                Accordingly, the branching node is <u>pruned by integrality</u>.

            At the end of the first iteration, branching on $x_{1}$ from Node 1 has generated the down-branching node, Node 2, and the up-branching node, Node 3. Node 2 is inserted in the list $L$, while Node 3 is pruned by integrality. The corresponding branch-and-bound tree is:

            ![Figure 10](../img/ex-07-bb/fig10.svg){ .fig loading=lazy  }

            Due to the current lower bound $\LB$ and upper bound $\UB$, we have:

            $$
            12 ~~\le~~ z(\ILP) ~~\le~~ \underbrace{\left\lfloor \dfrac{149}{10} \right\rfloor}_{=14}
            $$

        - <span style="color:#e03131"><strong>Iteration 2</strong></span>

            The $\LP_{2}$ problem is extracted and deleted from the problem list $L$.

            The branching variable is $x_{2}$. The branching constraints are:

            $$
            x_2 \le \underbrace{\left\lfloor \dfrac{43}{10} \right\rfloor}_{ = 4} \mathrm{~~~and~~~} x_2 \ge \underbrace{\left\lfloor \dfrac{43}{10} \right\rfloor +1}_{= 5}
            $$

            The $\LP_{4}$ problem is created with the first branching constraint (the second child is solved only if needed).

            1. Solution of the $\LP_{4}$ problem

                Let us consider the $\LP_{4}$ problem in standard form:

                $$
                \begin{array}{rrrrrrrrrrrrrrr}
                {\max} & {x_1} & {+} & {3}\, {x_2} &  &  &  &  &  &  &  &  \\[2ex]
                & {-}{4}\, {x_1} & {+} & {10}\, {x_2} & {+} & {x_3} &  &  &  &  &  &  & {=} & {35} \\[2ex]
                & {3}\, {x_1} & {+} & {x_2} &  &  & {+} & {x_4} &  &  &  &  & {=} & {12} \\[2ex]
                & {x_1} &  &  &  &  &  &  & {+} & {x_5} &  &  & {=} & {2} \\[2ex]
                &  &  & {x_2} &  &  &  &  &  &  & {+} & {x_6} & {=} & {4} \\[2ex]
                & {x_1,} &  & {x_2,} &  & {x_3,} &  & {x_4,} &  & {x_5,} &  & {x_6} & {\ge} & {0}
                \end{array}
                $$

                For the branching constraint we have:

                $$
                x_2 + x_6 = 4 \quad \quad \Rightarrow \quad x_6 = 4 - x_2
                $$

                Using the equations of the optimal simplex dictionary we can express this branching constraint as a function of the non-basic variables. Substituting, we get

                $$
                x_6 = 4 - \left(\dfrac{43}{10} - \dfrac{1}{10} \; x_3 - \dfrac{2}{5} \; x_5 \right)
                \quad \quad \Rightarrow \quad
                x_6 = -\dfrac{3}{10} + \dfrac{1}{10} \, x_3 + \dfrac{2}{5} \, x_5
                $$

                We then add this equation to the previous optimal simplex dictionary and we obtain the following simplex dictionary:

                \begin{equation*}
                \begin{cases}
                \begin{array}{rrrrrrr}
                {x_1} & {=} & {2} &  &  & {-} & {x_5} \\[2ex]
                {x_2} & {=} & {\dfrac{43}{10}} & {-} & {\dfrac{1}{10}}\, {x_3} & {-} & {\dfrac{2}{5}}\, {x_5} \\[2ex]
                {x_4} & {=} & {\dfrac{17}{10}} & {+} & {\dfrac{1}{10}}\, {x_3} & {+} & {\dfrac{17}{5}}\, {x_5} \\[2ex]
                {x_6} & {=} & {-\dfrac{3}{10}} & {+} & {\dfrac{1}{10}}\, {x_3} & {+} & {\dfrac{2}{5}}\, {x_5} \\[2ex]
                {z} & {=} & {\dfrac{149}{10}} & {-} & {\dfrac{3}{10}}\, {x_3} & {-} & {\dfrac{11}{5}}\, {x_5}
                \end{array}
                \end{cases}
                \end{equation*}

                where $\pazocal{B}=\{1,2,4,6\}$ and $\pazocal{N}=\{3,5\}$ and

                - the basic primal non-feasible solution is:

                    $$
                    \bar x_1=2,~~
                    \bar x_2=\dfrac{43}{10},~~
                    \bar x_4=\dfrac{17}{10},~~
                    \bar x_6=-\dfrac{3}{10},~~~~~~
                    \bar x_3=0,~~
                    \bar x_5=0~~
                    \mathrm{~~of~value~~~} \dfrac{149}{10}
                    $$

                - the basic dual feasible solution is:

                    $$
                    \bar \pi_1=0,~~
                    \bar \pi_2=0,~~
                    \bar \pi_4=0,~~
                    \bar \pi_6=0,~~~~~~
                    \bar \pi_3=\dfrac{3}{10},~~
                    \bar \pi_5=\dfrac{11}{5}~~
                    \mathrm{~~of~value~~~} \dfrac{149}{10}
                    $$

                If we increase the value of $\pi_{6}$, we can get a better dual solution. We have:

                $$
                \begin{cases}
                \pi_3 &= \dfrac{3}{10} - \dfrac{1}{10}\,\pi_6 \\[2ex]
                \pi_5 &= \dfrac{11}{5} - \dfrac{2}{5}\,\pi_6
                \end{cases}
                \qquad
                \qquad
                \begin{cases}
                \underbrace{ \dfrac{3}{10} - \dfrac{1}{10}\,\pi_6 \ge 0}_{\pi_3 \ge 0} \\[2ex]
                \underbrace{ \dfrac{11}{5} - \dfrac{2}{5}\,\pi_6 \ge 0}_{\pi_5 \ge 0}
                \end{cases}
                \qquad
                \qquad
                \begin{cases}
                \pi_6 &\le 3 \\[2ex]
                \pi_6 &\le \dfrac{11}{2}
                \end{cases}
                $$

                By increasing the value of $\pi_{6}$ to $\min \left\{3, \dfrac{11}{2}\right\}=3$, the value of $\pi_{3}$ decreases to $0$. Then we have that:

                - $\pi_{6}$ becomes a dual basic variable and accordingly $x_{6}$ becomes a primal non-basic variable.

                - $\pi_{3}$ becomes a dual non-basic variable and accordingly $x_{3}$ becomes a primal basic variable.

                We have:

                $$
                x_6 = -\dfrac{3}{10} + \dfrac{1}{10} \, x_3 + \dfrac{2}{5} \, x_5
                \quad \Rightarrow \quad
                x_3 = 3 - 4 \, x_5 + 10 \, x_6
                $$

                Substituting into the other equations of the primal simplex dictionary, we get:

                $$
                x_2
                = \dfrac{43}{10} - \dfrac{1}{10} \left( 3 - 4 \, x_5 + 10 \, x_6 \right) - \dfrac{2}{5} \; x_5
                = 4 - x_6
                $$

                $$
                x_4
                = \dfrac{17}{10} + \dfrac{1}{10} \left( 3 - 4 \, x_5 + 10 \, x_6 \right) + \dfrac{17}{5} \; x_5
                = 2 + 3 \, x_5 + x_6
                $$

                $$
                z
                = \dfrac{149}{10} - \dfrac{3}{10} \left( 3 - 4 \, x_5 + 10 \, x_6 \right) - \dfrac{11}{5} \; x_5
                = 14 - x_5 - 3 \, x_6
                $$

                Accordingly, we now have the following updated primal simplex dictionary:

                \begin{equation*}
                \begin{cases}
                \begin{array}{rrrrrrr}
                {x_1} & {=} & {2} & {-} & {x_5} &  &  \\[2ex]
                {x_2} & {=} & {4} &  &  & {-} & {x_6} \\[2ex]
                {x_3} & {=} & {3} & {-} & {4}\, {x_5} & {+} & {10}\, {x_6} \\[2ex]
                {x_4} & {=} & {2} & {+} & {3}\, {x_5} & {+} & {x_6} \\[2ex]
                {z} & {=} & {14} & {-} & {x_5} & {-} & {3}\, {x_6}
                \end{array}
                \end{cases}
                \end{equation*}

                where $\pazocal{B}=\{1,2,3,4\}$ and $\pazocal{N}=\{5,6\}$ and

                - the basic primal optimal solution is:

                    $$
                    \tilde x_1=2,~~
                    \tilde x_2=4,~~
                    \tilde x_3=3,~~
                    \tilde x_4=2,~~~~~~
                    \tilde x_5=0,~~
                    \tilde x_6=0~~
                    \mathrm{~~of~value~~~} 14
                    $$

                - the basic dual optimal solution is:

                    $$
                    \tilde \pi_1=0,~~
                    \tilde \pi_2=0,~~
                    \tilde \pi_3=0,~~
                    \tilde \pi_4=0,~~~~~~
                    \tilde \pi_5=1,~~
                    \tilde \pi_6=3~~
                    \mathrm{~~of~value~~~} 14
                    $$

                ![Figure 11](../img/ex-07-bb/fig11.svg){ .fig .ovale loading=lazy  }

                <em>Node 4: feasible region of $\LP_{4}$ (red), with the branching constraint $x_2 \le 4$ (dashed), and its optimal solution (blue).</em>

                An optimal solution ${\tilde{\boldsymbol x}}(\LP_{4})$ is:

                $$
                \tilde{x}_1=2,~~\tilde{x}_2=4
                $$

                the optimal value is:

                $$
                z(\LP_{4}) = 14
                $$

                The $\LP_{4}$ problem is <strong>feasible</strong> and we have

                $$
                \left\lfloor z(\LP_{4}) \right\rfloor > \LB
                \qquad \text{since} \qquad
                14 > 12
                $$

                The optimal solution ${\tilde{\boldsymbol x}}(\LP_{4})$ is <strong>integer</strong>, accordingly, the lower bound $\LB$ becomes $14$ and the incumbent solution ${\bar{\boldsymbol x}}(\ILP)$ becomes $\bar{x}_1=2,\bar{x}_2=4$.

                Accordingly, the branching node is <u>pruned by integrality</u>.

            At the end of the second iteration, branching on $x_{2}$ from Node 2 has generated the down-branching node, Node 4. This node is pruned by integrality. The up-branching node is not solved, since after Node 4 the lower bound satisfies

            $$
            \left\lfloor z(\LP_{2}) \right\rfloor \le \LB.
            $$

            This is the portion of the branch-and-bound tree showing the LP problems of the branching nodes that are solved:

            ![Figure 12](../img/ex-07-bb/fig12.svg){ .fig loading=lazy  }

            Due to the current lower bound $\LB$ and upper bound $\UB$, we have:

            $$
            14 ~~\le~~ z(\ILP) ~~\le~~ 14
            $$

        - <span style="color:#e03131"><strong>Termination</strong></span>

            Since the list $L$ is empty, the lower bound $\LB$ is equal to the upper bound $\UB$ and the $\ILP$ problem is solved to optimality.

            The optimal value is $z(\ILP)=14$ (the value of the lower bound $\LB$) and an optimal solution ${\tilde{\boldsymbol x}}(\ILP)$ is $\tilde x_1=2,\tilde x_2=4$ (the incumbent solution ${\bar{\boldsymbol x}}(\ILP)$).

    2. The final branch-and-bound tree is:

        ![Figure 13](../img/ex-07-bb/fig13.svg){ .fig loading=lazy  }

        The reasons why the branching nodes are pruned are the following.

        - Node 1 has a fractional optimal solution with $z(\LP_{1}) = 16$: it is branched on $x_1$.

        - Node 2 has a fractional optimal solution with $\lfloor z(\LP_{2})\rfloor = 14$: it is branched on $x_2$.

        - The up-branching node of Node 2 is not solved, and it is not drawn, since $\lfloor z(\LP_{2})\rfloor = 14$ $\le \LB = 14$ after its first child.

        - Node 3 is pruned by integrality: $\tilde{\boldsymbol x}(\LP_{3}) = (3, 3)$ is integer, with value $12$, which becomes the incumbent solution.

        - Node 4 is pruned by integrality: $\tilde{\boldsymbol x}(\LP_{4}) = (2, 4)$ is integer, with value $14$, which becomes the incumbent solution.

        The optimal value is $z(\ILP) = 14$ and the branch-and-bound algorithm returns the optimal solution $\tilde{\boldsymbol x}(\ILP) = (2, 4)$.

<a id="esercizio-7-3"></a>

## Strong branching

<a id="esercizio-7-13"></a>

!!! esercizio "Exercise 7.13: Strong branching (P1)"

    !!! nota ""

        Consider the following ILP problem in canonical maximization form with $n=2$ integer variables and $m=1$ constraint:

        $$
        \begin{array}{rrrrrrr}
        {\max} & {x_1} & {+} & {8}\, {x_2} \\[2ex]
        & {3}\, {x_1} & {+} & {6}\, {x_2} & {\le} & {16} \\[2ex]
        & {x_1,} &  & {x_2} & {\in} & {\Z_{\ge 0}}
        \end{array}
        $$

    **Questions**

    1. Solve graphically the LP relaxation $\LP_{1}$ of the ILP problem.

    2. Determine the branching variable with the strong branching branching-variable selection strategy (in case of ties, choose the variable with the smallest index). Solve graphically the LP problems of the branching nodes.

??? soluzione "Solution"

    !!! nota ""

        For a fractional variable $x_j$ of $\tilde{\boldsymbol x}(\LP_{1})$, the two branching nodes are $\LP_{2}$ (with $x_j\le\lfloor\tilde x_j\rfloor$) and $\LP_{3}$ (with $x_j\ge\lfloor\tilde x_j\rfloor+1$), and

        $$
        \delta_1^d(x_j) = z(\LP_{1}) - z(\LP_{2}), \qquad \delta_1^u(x_j) = z(\LP_{1}) - z(\LP_{3}),
        $$

        with $\delta = +\infty$ for an infeasible node. Strong branching selects the variable with the largest score $\min\{\delta_1^d(x_j),\,\delta_1^u(x_j)\}$.

    1. For the $\LP_{1}$ problem, graphically we have:

        ![Figure 102](../img/ex-07-bb/fig102.svg){ .fig .ovale loading=lazy  }

        <em>Feasible region of $\LP_{1}$ (red), integer points (black) and optimal solution of $\LP_{1}$ (blue) with the objective function line through it.</em>

        An optimal solution $\tilde{\boldsymbol x}(\LP_{1})$ is:

        $$
        \tilde{x}_1=0,\quad \tilde{x}_2=\dfrac{8}{3}
        $$

        the optimal value is:

        $$
        z(\LP_{1}) = \dfrac{64}{3}
        $$

        Only $x_2$ is fractional: it is the only candidate branching variable, and strong branching computes its score anyway.

    2. Strong branching. Graphically, we have:

        1. Branching on $x_2$:

            - With $x_2 \le 2$, an optimal solution of $\LP_{2}$ is

                $$
                \tilde{x}_1=\dfrac{4}{3},\quad \tilde{x}_2=2 \quad \text{and} \quad z(\LP_{2}) = \dfrac{52}{3},
                $$

                accordingly

                $$
                \delta_1^d(x_2) = \dfrac{64}{3} - \dfrac{52}{3} = 4.
                $$

            - With $x_2 \ge 3$, the $\LP_{3}$ problem is <strong>infeasible</strong>, accordingly

                $$
                \delta_1^u(x_2) = +\infty
                $$

            For the variable $x_2$ we have:

            $$
            \min\left\{\delta_1^d(x_2),\,\delta_1^u(x_2)\right\} = \min\left\{4,\,+\infty\right\} = 4.
            $$

            ![Figure 103](../img/ex-07-bb/fig103.svg){ .fig .ovale loading=lazy  }

            <em>Branching on $x_2$: feasible regions of $\LP_{2}$ ($x_2\le 2$) and $\LP_{3}$ ($x_2\ge 3$) in red, integer points (black) and optimal solutions (blue).</em>

        Therefore, strong branching selects $x_2$, the only fractional variable, with score $4$.

<a id="esercizio-7-14"></a>

!!! esercizio "Exercise 7.14: Strong branching (P2)"

    !!! nota ""

        Consider the following ILP problem in canonical maximization form with $n=2$ integer variables and $m=2$ constraints:

        $$
        \begin{array}{rrrrrrr}
        {\max} & {x_1} & {+} & {3}\, {x_2} \\[2ex]
        & {-}{4}\, {x_1} & {+} & {10}\, {x_2} & {\le} & {35} \\[2ex]
        & {3}\, {x_1} & {+} & {x_2} & {\le} & {12} \\[2ex]
        & {x_1,} &  & {x_2} & {\in} & {\Z_{\ge 0}}
        \end{array}
        $$

    **Questions**

    1. Solve graphically the LP relaxation $\LP_{1}$ of the ILP problem.

    2. Determine the branching variable with the strong branching branching-variable selection strategy (in case of ties, choose the variable with the smallest index). Solve graphically the LP problems of the branching nodes.

??? soluzione "Solution"

    !!! nota ""

        For a fractional variable $x_j$ of $\tilde{\boldsymbol x}(\LP_{1})$, the two branching nodes are $\LP_{2}$ (with $x_j\le\lfloor\tilde x_j\rfloor$) and $\LP_{3}$ (with $x_j\ge\lfloor\tilde x_j\rfloor+1$), and

        $$
        \delta_1^d(x_j) = z(\LP_{1}) - z(\LP_{2}), \qquad \delta_1^u(x_j) = z(\LP_{1}) - z(\LP_{3}),
        $$

        with $\delta = +\infty$ for an infeasible node. Strong branching selects the variable with the largest score $\min\{\delta_1^d(x_j),\,\delta_1^u(x_j)\}$.

    1. For the $\LP_{1}$ problem, graphically we have:

        ![Figure 104](../img/ex-07-bb/fig104.svg){ .fig .ovale loading=lazy  }

        <em>Feasible region of $\LP_{1}$ (red), integer points (black) and optimal solution of $\LP_{1}$ (blue) with the objective function line through it.</em>

        An optimal solution $\tilde{\boldsymbol x}(\LP_{1})$ is:

        $$
        \tilde{x}_1=\dfrac{5}{2},\quad \tilde{x}_2=\dfrac{9}{2}
        $$

        the optimal value is:

        $$
        z(\LP_{1}) = 16
        $$

    2. Strong branching. Graphically, we have:

        1. Branching on $x_1$:

            - With $x_1 \le 2$, an optimal solution of $\LP_{2}$ is

                $$
                \tilde{x}_1=2,\quad \tilde{x}_2=\dfrac{43}{10} \quad \text{and} \quad z(\LP_{2}) = \dfrac{149}{10},
                $$

                accordingly

                $$
                \delta_1^d(x_1) = 16 - \dfrac{149}{10} = \dfrac{11}{10}.
                $$

            - With $x_1 \ge 3$, an optimal solution of $\LP_{3}$ is

                $$
                \tilde{x}_1=3,\quad \tilde{x}_2=3 \quad \text{and} \quad z(\LP_{3}) = 12,
                $$

                accordingly

                $$
                \delta_1^u(x_1) = 16 - 12 = 4.
                $$

            For the variable $x_1$ we have:

            $$
            \min\left\{\delta_1^d(x_1),\,\delta_1^u(x_1)\right\} = \min\left\{\dfrac{11}{10},\,4\right\} = \dfrac{11}{10}.
            $$

            ![Figure 105](../img/ex-07-bb/fig105.svg){ .fig .ovale loading=lazy  }

            <em>Branching on $x_1$: feasible regions of $\LP_{2}$ ($x_1\le 2$) and $\LP_{3}$ ($x_1\ge 3$) in red, integer points (black) and optimal solutions (blue).</em>

        2. Branching on $x_2$:

            - With $x_2 \le 4$, an optimal solution of $\LP_{2}$ is

                $$
                \tilde{x}_1=\dfrac{8}{3},\quad \tilde{x}_2=4 \quad \text{and} \quad z(\LP_{2}) = \dfrac{44}{3},
                $$

                accordingly

                $$
                \delta_1^d(x_2) = 16 - \dfrac{44}{3} = \dfrac{4}{3}.
                $$

            - With $x_2 \ge 5$, the $\LP_{3}$ problem is <strong>infeasible</strong>, accordingly

                $$
                \delta_1^u(x_2) = +\infty
                $$

            For the variable $x_2$ we have:

            $$
            \min\left\{\delta_1^d(x_2),\,\delta_1^u(x_2)\right\} = \min\left\{\dfrac{4}{3},\,+\infty\right\} = \dfrac{4}{3}.
            $$

            ![Figure 106](../img/ex-07-bb/fig106.svg){ .fig .ovale loading=lazy  }

            <em>Branching on $x_2$: feasible regions of $\LP_{2}$ ($x_2\le 4$) and $\LP_{3}$ ($x_2\ge 5$) in red, integer points (black) and optimal solutions (blue).</em>

        Therefore, strong branching selects $x_2$, since its score is the largest:

        $$
        \dfrac{4}{3} > \dfrac{11}{10}.
        $$

<a id="esercizio-7-15"></a>

