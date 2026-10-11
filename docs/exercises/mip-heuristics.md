---
title: "Exercises · MIP heuristics"
---

# Exercises · MIP heuristics

<div class="info-capitolo" markdown>

**Exercises · Chapter 10** · each solution opens with a click · [:material-book-open-variant: Chapter 10](../mip-heuristics/mip-heuristics.md)

</div>

## Rounding and diving

<a id="esercizio-10-1"></a>

!!! esercizio "Exercise 10.1: Rounding and diving 1"

    We consider a knapsack of capacity $c=16$ and a set $\mathscr{I}=\{1,2,3,4,5\}$ of $5$ items, with the following profits and weights:

    <div class="tabella" markdown="0"><table>
    <tr>
    <th></th>
    <th><span class="arithmatex">\(i=1\)</span></th>
    <th><span class="arithmatex">\(i=2\)</span></th>
    <th><span class="arithmatex">\(i=3\)</span></th>
    <th><span class="arithmatex">\(i=4\)</span></th>
    <th><span class="arithmatex">\(i=5\)</span></th>
    </tr>
    <tr>
    <td><span class="arithmatex">\(p_i\)</span></td>
    <td><span class="arithmatex">\(11\)</span></td>
    <td><span class="arithmatex">\(15\)</span></td>
    <td><span class="arithmatex">\(18\)</span></td>
    <td><span class="arithmatex">\(14\)</span></td>
    <td><span class="arithmatex">\(10\)</span></td>
    </tr>
    <tr>
    <td><span class="arithmatex">\(w_i\)</span></td>
    <td><span class="arithmatex">\(3\)</span></td>
    <td><span class="arithmatex">\(5\)</span></td>
    <td><span class="arithmatex">\(7\)</span></td>
    <td><span class="arithmatex">\(10\)</span></td>
    <td><span class="arithmatex">\(9\)</span></td>
    </tr>
    </table></div>

    **Questions**

    1. Verify that the items are sorted by non-increasing efficiency, and compute the optimal solution $x^*$ of the LP relaxation of the KP model and its value $z_{LP}(I)$.

    2. Apply rounding: round $x^*$ to the nearest integer point $\tilde x$, check whether $\tilde x$ is feasible, and give its profit.

    3. Apply fractional diving: at each step solve the LP relaxation with the variables fixed so far, fix the least fractional variable to its nearest integer, and repeat until the LP solution is integer.

    4. The dive is a path of the branch-and-bound tree. Compute the value of the LP relaxation of the branches that the dive does not visit, and use them to decide whether the solution of the dive is optimal.

??? soluzione "Solution"

    1. The efficiencies are

        $$
        \frac{11}{3}\approx 3.67,\quad \frac{15}{5}=3,\quad \frac{18}{7}\approx 2.57,\quad \frac{14}{10}=1.4,\quad
        \frac{10}{9}\approx 1.11,
        $$

        non-increasing. The LP takes items $1,2,3$ (weight $3+5+7=15$), and the residual capacity $1$ is filled by the fraction $\frac{1}{10}$ of item $4$:

        $$
        x^*=\Big(1,1,1,\frac{1}{10},0\Big),\qquad z_{LP}(I)=11+15+18+\frac{14}{10}=\frac{227}{5}=45.4 .
        $$

    2. $\rnd{\frac1{10}}=0$, so $\tilde x=(1,1,1,0,0)$: weight $15\le16$, feasible, profit $44$.

    3. <strong>Step 1.</strong> The only fractional variable is $x_4=\frac1{10}$: fix $x_4:=0$.

        <strong>Step 2.</strong> Without item $4$, the LP takes items $1,2,3$ and the fraction $\frac19$ of item $5$: $x^*=\big(1,1,1,0,\frac19\big)$, of value $44+\frac{10}{9}=\frac{406}{9}\approx45.11$. Fix $x_5:=\rnd{\frac19}=0$.

        <strong>Step 3.</strong> The LP solution $(1,1,1,0,0)$, of value $44$, is integer: the dive returns $S=\{1,2,3\}$, of profit $44$.

    4. The dive does not visit the branches $x_4=1$ and ($x_4=0$, $x_5=1$).

        - $x_4=1$: item $4$ uses $10$ of the capacity, profit $14$; the LP adds item $1$ (residual $3$) and the fraction $\frac35$ of item $2$: value $14+11+9=34$.

        - $x_4=0$, $x_5=1$: item $5$ uses $9$, profit $10$; the LP adds item $1$ (residual $4$) and the fraction $\frac45$ of item $2$: value $10+11+12=33$.

        Both values are smaller than $44$: no solution in the abandoned branches is better than the solution of the dive, which is therefore <strong>optimal</strong>, $z(I)=44$. The dive, together with the bounds of the branches it leaves, is a complete branch-and-bound.

<a id="esercizio-10-2"></a>

!!! esercizio "Exercise 10.2: Rounding and diving 2"

    We consider a knapsack of capacity $c=15$ and a set $\mathscr{I}=\{1,2,3,4,5\}$ of $5$ items, with the following profits and weights:

    <div class="tabella" markdown="0"><table>
    <tr>
    <th></th>
    <th><span class="arithmatex">\(i=1\)</span></th>
    <th><span class="arithmatex">\(i=2\)</span></th>
    <th><span class="arithmatex">\(i=3\)</span></th>
    <th><span class="arithmatex">\(i=4\)</span></th>
    <th><span class="arithmatex">\(i=5\)</span></th>
    </tr>
    <tr>
    <td><span class="arithmatex">\(p_i\)</span></td>
    <td><span class="arithmatex">\(13\)</span></td>
    <td><span class="arithmatex">\(12\)</span></td>
    <td><span class="arithmatex">\(14\)</span></td>
    <td><span class="arithmatex">\(6\)</span></td>
    <td><span class="arithmatex">\(5\)</span></td>
    </tr>
    <tr>
    <td><span class="arithmatex">\(w_i\)</span></td>
    <td><span class="arithmatex">\(2\)</span></td>
    <td><span class="arithmatex">\(5\)</span></td>
    <td><span class="arithmatex">\(10\)</span></td>
    <td><span class="arithmatex">\(6\)</span></td>
    <td><span class="arithmatex">\(7\)</span></td>
    </tr>
    </table></div>

    **Questions**

    1. Compute the optimal solution $x^*$ of the LP relaxation and its value $z_{LP}(I)$ (the items are sorted by non-increasing efficiency).

    2. Round $x^*$ to the nearest integer point: is it feasible? Round it down instead, and give the profit.

    3. Apply fractional diving, step by step, and give the profit of the solution it returns.

    4. Repeat the dive starting with the opposite decision at the first step, and continue with the rule of fractional diving. Then use the LP bounds of the branches to prove that the best of the two solutions is optimal.

??? soluzione "Solution"

    1. The efficiencies are $6.5$, $2.4$, $1.4$, $1$, $\frac57\approx0.71$. The LP takes items $1,2$ (weight $7$) and the fraction $\frac{8}{10}=\frac45$ of item $3$:

        $$
        x^*=\Big(1,1,\frac45,0,0\Big),\qquad z_{LP}(I)=13+12+14\cdot\frac45=\frac{181}{5}=36.2 .
        $$

    2. $\rnd{\frac45}=1$ gives $(1,1,1,0,0)$, of weight $17>15$: <strong>infeasible</strong>. Rounding down gives $(1,1,0,0,0)$, feasible (Observation on rounding down), of profit $25$.

    3. <strong>Step 1.</strong> Fix $x_3:=\rnd{\frac45}=1$. Item $3$ uses $10$; the LP takes item $1$ (residual $3$) and the fraction $\frac35$ of item $2$: $x^*=\big(1,\frac35,1,0,0\big)$, of value $14+13+\frac{36}{5}=\frac{171}{5}=34.2$.

        <strong>Step 2.</strong> Fix $x_2:=\rnd{\frac35}=1$. Items $2,3$ fill the knapsack ($5+10=15$): the LP solution is $(0,1,1,0,0)$, integer, of value $26$. The dive returns $S=\{2,3\}$, of profit $26$.

    4. <strong>Step 1'.</strong> Fix $x_3:=0$. The LP takes items $1,2,4$ (weight $13$) and the fraction $\frac27$ of item $5$: value $13+12+6+\frac{10}{7}=\frac{227}{7}\approx32.43$.

        <strong>Step 2'.</strong> Fix $x_5:=\rnd{\frac27}=0$: the LP solution $(1,1,0,1,0)$ is integer, of profit $31$.

        The four leaves of the tree are:

        <div class="tabella" markdown="0"><table>
        <tr>
        <th>branch</th>
        <th>LP value</th>
        <th></th>
        </tr>
        <tr>
        <td><span class="arithmatex">\(x_3=1\)</span>, <span class="arithmatex">\(x_2=1\)</span></td>
        <td><span class="arithmatex">\(26\)</span></td>
        <td>integer</td>
        </tr>
        <tr>
        <td><span class="arithmatex">\(x_3=1\)</span>, <span class="arithmatex">\(x_2=0\)</span></td>
        <td><span class="arithmatex">\(30\)</span></td>
        <td>items <span class="arithmatex">\(3,1\)</span> and half of item <span class="arithmatex">\(4\)</span>; <span class="arithmatex">\(30<31\)</span></td>
        </tr>
        <tr>
        <td><span class="arithmatex">\(x_3=0\)</span>, <span class="arithmatex">\(x_5=0\)</span></td>
        <td><span class="arithmatex">\(31\)</span></td>
        <td>integer</td>
        </tr>
        <tr>
        <td><span class="arithmatex">\(x_3=0\)</span>, <span class="arithmatex">\(x_5=1\)</span></td>
        <td><span class="arithmatex">\(31\)</span></td>
        <td>not better than <span class="arithmatex">\(31\)</span></td>
        </tr>
        </table></div>

        No branch can contain a solution of profit larger than $31$, so $\{1,2,4\}$ is <strong>optimal</strong>: $z(I)=31$. The first dive ($26$) failed because its first decision, the most natural one for the LP ($x_3=\frac45\to1$), was wrong.

## Feasibility pump

<a id="esercizio-10-3"></a>

!!! esercizio "Exercise 10.3: Feasibility pump 1"

    Consider the integer program

    \begin{align*}
    && \max ~~ x_1 + x_2 & & \\[0.5ex]
    && \text{subject to} \quad 6\,x_1 - 2\,x_2 &\le 16 & \\[0.5ex]
    && 6\,x_1 - 7\,x_2 &\ge 8 & \\[0.5ex]
    && x_1 &\in \{0,1,\dots,5\} & \\[0.5ex]
    && x_2 &\in \{0,1,\dots,5\} &
    \end{align*}

    (Recall that the $L_1$ distance from a point to a segment that is neither horizontal, vertical, nor of slope $\pm1$ is minimized at an endpoint or where the segment crosses the horizontal or the vertical line through the point.)

    **Questions**

    1. Draw the region $\mathcal{L}$ of the LP relaxation and its integer points, and compute the optimal solution $x^{*0}$ of the LP relaxation.

    2. Round $x^{*0}$ to the nearest integer point $\tilde x^0$, and check whether it is feasible.

    3. Apply the feasibility pump: at each iteration compute the projection $x^{*k}$, i.e., the point of $\mathcal{L}$ that minimizes the $L_1$ distance $\Delta(x,\tilde x^{k-1})$, round it, and, if the rounding gives back the same integer point, move the component with the largest $|x^{*k}_j-\tilde x^{k-1}_j|$ by one unit toward $x^{*k}_j$. Stop when the distance is $0$.

    4. Is the solution found by the pump optimal? Compare its value with the LP bound.

??? soluzione "Solution"

    1. The constraint $6x_1-7x_2\ge 8$ with $x_2\ge0$ requires $x_1\ge\frac43$, and $6x_1-2x_2\le16$ with $x_2\ge 0$ gives $x_1\le\frac83$ on the axis. $\mathcal{L}$ is the triangle with vertices $\big(\frac43,0\big)$, $\big(\frac83,0\big)$ and the intersection of the two lines, $\big(\frac{16}{5},\frac85\big)$. Its integer points are $(2,0)$ and $(3,1)$. The objective $x_1+x_2$ is maximized at the vertex

        $$
        x^{*0}=\Big(\frac{16}{5},\frac85\Big),\qquad z_{LP}(I)=\frac{24}{5}=4.8 .
        $$

        ![Figure 1](../img/ex-10-mip-heuristics/fig01.svg){ .fig loading=lazy  }

    2. $\tilde x^0=\big(\rnd{3.2},\rnd{1.6}\big)=(3,2)$: $6\cdot3-2\cdot2=14\le16$, but $6\cdot3-7\cdot2=4<8$: infeasible.

    3. <strong>Iteration 1.</strong> Projection of $(3,2)$. The vertical line $x_1=3$ crosses $\mathcal{L}$ for $18-2x_2\le16$ and $18-7x_2\ge8$, i.e., between $(3,1)$ and $\big(3,\frac{10}{7}\big)$: the closest is $\big(3,\frac{10}7\big)$, at distance $2-\frac{10}{7}=\frac47$. The horizontal line $x_2=2$ does not cross $\mathcal{L}$ ($x_1\le\frac{10}3$ and $x_1\ge\frac{11}{3}$). The vertex $\big(\frac{16}5,\frac85\big)$ is at distance $\frac15+\frac25=\frac35>\frac47$, the other vertices are farther. Hence $x^{*1}=\big(3,\frac{10}7\big)$, $\Delta=\frac47$, and $\tilde x^1=\rnd{x^{*1}}=(3,1)$, a new point.

        <strong>Iteration 2.</strong> $(3,1)\in\mathcal{L}$ ($16\le16$, $11\ge8$): $\Delta=0$, the pump stops with $(3,1)$, of value $4$, after two projections.

    4. The other integer point $(2,0)$ has value $2$, so $(3,1)$ is <strong>optimal</strong>. The LP bound gives $z(I)\le\lfloor 4.8\rfloor=4$ as well, which certifies the optimality without enumerating the integer points.

<a id="esercizio-10-4"></a>

!!! esercizio "Exercise 10.4: Feasibility pump 2"

    Consider the integer program

    \begin{align*}
    && \max ~~ 4\,x_1 + 4\,x_2 & & \\[0.5ex]
    && \text{subject to} \quad -3\,x_1 + 5\,x_2 &\le 11 & \\[0.5ex]
    && 4\,x_1 - 3\,x_2 &\le -1 & \\[0.5ex]
    && x_1 &\in \{0,1,\dots,5\} & \\[0.5ex]
    && x_2 &\in \{0,1,\dots,5\} &
    \end{align*}

    (Recall that the $L_1$ distance from a point to a segment that is neither horizontal, vertical, nor of slope $\pm1$ is minimized at an endpoint or where the segment crosses the horizontal or the vertical line through the point.)

    **Questions**

    1. Draw the region $\mathcal{L}$ of the LP relaxation and its integer points, and compute the optimal solution $x^{*0}$ of the LP relaxation.

    2. Round $x^{*0}$ to the nearest integer point $\tilde x^0$, and check whether it is feasible.

    3. Apply the feasibility pump: at each iteration compute the projection $x^{*k}$, i.e., the point of $\mathcal{L}$ that minimizes the $L_1$ distance $\Delta(x,\tilde x^{k-1})$, round it, and, if the rounding gives back the same integer point, move the component with the largest $|x^{*k}_j-\tilde x^{k-1}_j|$ by one unit toward $x^{*k}_j$. Stop when the distance is $0$.

    4. How many times does the pump need a flip? Is the solution found optimal?

??? soluzione "Solution"

    1. With $x_1\ge0$, $\mathcal{L}$ is the triangle with vertices $\big(0,\frac13\big)$ and $\big(0,\frac{11}5\big)$, on the axis $x_1=0$, and the intersection of the two lines, $\big(\frac{28}{11},\frac{41}{11}\big)$. Its integer points are $(0,1)$, $(0,2)$, $(1,2)$, $(2,3)$. The objective is maximized at

        $$
        x^{*0}=\Big(\frac{28}{11},\frac{41}{11}\Big),\qquad z_{LP}(I)=\frac{276}{11}\approx 25.09 .
        $$

        ![Figure 2](../img/ex-10-mip-heuristics/fig02.svg){ .fig loading=lazy  }

    2. $\tilde x^0=\big(\rnd{2.55},\rnd{3.73}\big)=(3,4)$: $-9+20=11\le11$, but $12-12=0>-1$: infeasible.

    3. <strong>Iteration 1.</strong> Projection of $(3,4)$. The vertical line $x_1=3$ would need $x_2\ge\frac{13}3$ and $x_2\le4$: no crossing. The horizontal line $x_2=4$ would need $x_1\ge3$ and $x_1\le\frac{11}4$: no crossing. The closest point is the vertex $x^{*1}=\big(\frac{28}{11},\frac{41}{11}\big)$, at distance $\frac5{11}+\frac3{11}=\frac8{11}$. Its rounding is $(3,4)$ again: a <strong>short cycle</strong>. The largest difference is on $x_1$ ($\frac{5}{11}>\frac3{11}$), which moves toward $\frac{28}{11}$: $\tilde x^1=(2,4)$.

        <strong>Iteration 2.</strong> Projection of $(2,4)$. The vertical line $x_1=2$ crosses $\mathcal{L}$ for $x_2\ge3$ and $x_2\le\frac{17}{5}$: the closest point is $\big(2,\frac{17}5\big)$, at distance $\frac35$. The horizontal line $x_2=4$ does not cross $\mathcal{L}$, and the vertex $\big(\frac{28}{11},\frac{41}{11}\big)$ is at distance $\frac6{11}+\frac3{11}=\frac9{11}>\frac35$. Hence $x^{*2}=\big(2,\frac{17}5\big)$ and $\tilde x^2=(2,3)$.

        <strong>Iteration 3.</strong> $(2,3)\in\mathcal{L}$ ($-6+15=9\le11$, $8-9=-1\le-1$): $\Delta=0$, the pump stops with $(2,3)$, of value $20$.

    4. The pump needs <strong>one flip</strong>: without it, it would cycle forever between $(3,4)$ and the vertex $x^{*0}$. The values of the integer points are $4$, $8$, $12$, $20$: $(2,3)$ is <strong>optimal</strong>.

<a id="esercizio-10-5"></a>

!!! esercizio "Exercise 10.5: Feasibility pump 3"

    Consider the integer program

    \begin{align*}
    && \max ~~ 4\,x_1 + 2\,x_2 & & \\[0.5ex]
    && \text{subject to} \quad 5\,x_1 + 4\,x_2 &\le 9 & \\[0.5ex]
    && -7\,x_1 + 3\,x_2 &\le 5 & \\[0.5ex]
    && x_1 &\in \{0,1,\dots,5\} & \\[0.5ex]
    && x_2 &\in \{0,1,\dots,5\} &
    \end{align*}

    (Recall that the $L_1$ distance from a point to a segment that is neither horizontal, vertical, nor of slope $\pm1$ is minimized at an endpoint or where the segment crosses the horizontal or the vertical line through the point.)

    **Questions**

    1. Draw the region $\mathcal{L}$ of the LP relaxation and its integer points, and compute the optimal solution $x^{*0}$ of the LP relaxation.

    2. Round $x^{*0}$ to the nearest integer point $\tilde x^0$, and check whether it is feasible.

    3. Apply the feasibility pump: at each iteration compute the projection $x^{*k}$, i.e., the point of $\mathcal{L}$ that minimizes the $L_1$ distance $\Delta(x,\tilde x^{k-1})$, round it, and, if the rounding gives back the same integer point, move the component with the largest $|x^{*k}_j-\tilde x^{k-1}_j|$ by one unit toward $x^{*k}_j$. Stop when the distance is $0$.

    4. Find the optimal solution by inspecting the integer points, and compute the gap of the solution of the pump with respect to the LP bound. Why does the pump miss the optimum?

??? soluzione "Solution"

    1. $\mathcal{L}$ is the quadrilateral with vertices $(0,0)$, $\big(\frac95,0\big)$, $\big(\frac{7}{43},\frac{88}{43}\big)$ (intersection of the two lines) and $\big(0,\frac53\big)$. Its integer points are $(0,0)$, $(1,0)$, $(0,1)$, $(1,1)$. The objective values of the vertices are $0$, $\frac{36}5$, $\frac{204}{43}\approx4.74$, $\frac{10}3$, so

        $$
        x^{*0}=\Big(\frac95,0\Big),\qquad z_{LP}(I)=\frac{36}{5}=7.2 .
        $$

        ![Figure 3](../img/ex-10-mip-heuristics/fig03.svg){ .fig loading=lazy  }

    2. $\tilde x^0=(\rnd{1.8},0)=(2,0)$: $10>9$, infeasible.

    3. <strong>Iteration 1.</strong> With $\tilde x^0=(2,0)$, $\Delta(x,\tilde x^0)=|x_1-2|+x_2$. The vertical line $x_1=2$ does not cross $\mathcal{L}$; the horizontal line $x_2=0$ crosses it on the segment from $(0,0)$ to $\big(\frac95,0\big)$, whose closest point is $\big(\frac95,0\big)$, at distance $\frac15$; the other vertices are farther. So $x^{*1}=\big(\frac95,0\big)=x^{*0}$, whose rounding is $(2,0)$ again: a short cycle. The flip moves $x_1$ toward $\frac95$: $\tilde x^1=(1,0)$.

        <strong>Iteration 2.</strong> $(1,0)\in\mathcal{L}$: $\Delta=0$, the pump stops with $(1,0)$, of value $4$.

    4. The values of the integer points are $0$, $4$, $2$, $6$: the optimum is $(1,1)$, with $z(I)=6$ ($5+4=9\le9$, $-7+3\le5$). With respect to the LP bound $\lfloor7.2\rfloor=7$ the gap of the pump's solution is $\frac{7-4}{7}\approx42.9\%$ (its true relative error is $\frac{6-4}{6}\approx33.3\%$). The pump looks only at the distance from $\mathcal{L}$, never at the objective after the first LP: it stops at the first feasible point it meets. Improving it is the job of methods such as local branching or proximity search.

## RINS and local branching

<a id="esercizio-10-6"></a>

!!! esercizio "Exercise 10.6: RINS and local branching 1"

    We consider a knapsack of capacity $c=22$ and a set $\mathscr{I}=\{1,2,\dots,6\}$ of $6$ items, sorted by non-increasing efficiency, with the following profits and weights:

    <div class="tabella" markdown="0"><table>
    <tr>
    <th></th>
    <th><span class="arithmatex">\(i=1\)</span></th>
    <th><span class="arithmatex">\(i=2\)</span></th>
    <th><span class="arithmatex">\(i=3\)</span></th>
    <th><span class="arithmatex">\(i=4\)</span></th>
    <th><span class="arithmatex">\(i=5\)</span></th>
    <th><span class="arithmatex">\(i=6\)</span></th>
    </tr>
    <tr>
    <td><span class="arithmatex">\(p_i\)</span></td>
    <td><span class="arithmatex">\(18\)</span></td>
    <td><span class="arithmatex">\(21\)</span></td>
    <td><span class="arithmatex">\(4\)</span></td>
    <td><span class="arithmatex">\(19\)</span></td>
    <td><span class="arithmatex">\(10\)</span></td>
    <td><span class="arithmatex">\(7\)</span></td>
    </tr>
    <tr>
    <td><span class="arithmatex">\(w_i\)</span></td>
    <td><span class="arithmatex">\(8\)</span></td>
    <td><span class="arithmatex">\(10\)</span></td>
    <td><span class="arithmatex">\(2\)</span></td>
    <td><span class="arithmatex">\(12\)</span></td>
    <td><span class="arithmatex">\(7\)</span></td>
    <td><span class="arithmatex">\(10\)</span></td>
    </tr>
    </table></div>

    The incumbent is the solution of the greedy by <em>profit</em> $GR_p$.

    **Questions**

    1. Compute the incumbent $\tilde x$ and its profit, and the optimal solution $x^*$ of the LP relaxation.

    2. Apply RINS: list the variables fixed by RINS, write the sub-MIP with the cutoff constraint $\sum_i p_ix_i\ge f(\tilde x)+1$, and solve it by enumeration.

    3. Show that local branching with radius $k=2$ around $\tilde x$ finds no better solution.

    4. Apply proximity search with $\theta=1$ from $\tilde x$ (the optimal solution of this instance has profit $43$, as can be checked by enumerating the $2^6$ subsets). Compare the three methods.

??? soluzione "Solution"

    1. By non-increasing profit the order is $2$ ($21$), $4$ ($19$), $1$ ($18$), $5$, $6$, $3$. Item $2$ is taken ($rc=12$), item $4$ is taken ($rc=0$), nothing else fits: $\tilde x=(0,1,0,1,0,0)$, profit $40$. The LP takes items $1,2,3$ (weight $20$) and the fraction $\frac2{12}=\frac16$ of item $4$:

        $$
        x^*=\Big(1,1,1,\frac16,0,0\Big),\qquad z_{LP}(I)=43+\frac{19}{6}=\frac{277}{6}\approx46.17 .
        $$

    2. $x^*$ and $\tilde x$ agree on $x_2=1$, $x_5=0$, $x_6=0$, which are fixed; $x_1,x_3,x_4$ are free. The sub-MIP is

        \begin{align*}
        && \max ~~ 21+18\,x_1+4\,x_3+19\,x_4 & & \\[0.5ex]
        && \text{subject to} \quad 8\,x_1+2\,x_3+12\,x_4 &\le 12 & \\[0.5ex]
        && 21+18\,x_1+4\,x_3+19\,x_4 &\ge 41 & \\[0.5ex]
        && x_1 &\in\{0,1\} & \\[0.5ex]
        && x_3 &\in\{0,1\} & \\[0.5ex]
        && x_4 &\in\{0,1\} &
        \end{align*}

        If $x_4=1$ the capacity forces $x_1=x_3=0$ and the profit is $40<41$. If $x_4=0$, the best choice is $x_1=x_3=1$ (weight $10\le12$), of profit $43$. RINS returns $\{1,2,3\}$, of profit $43$: the <strong>optimum</strong>.

    3. The incumbent fills the knapsack ($10+12=22$), so adding an item is infeasible, and removing items decreases the profit. The solutions at distance $2$ that keep the weight feasible are the swaps (one item out, one in):

        <div class="tabella" markdown="0"><table>
        <tr>
        <th>out  in</th>
        <th><span class="arithmatex">\(1\)</span></th>
        <th><span class="arithmatex">\(3\)</span></th>
        <th><span class="arithmatex">\(5\)</span></th>
        <th><span class="arithmatex">\(6\)</span></th>
        </tr>
        <tr>
        <td><span class="arithmatex">\(2\)</span></td>
        <td><span class="arithmatex">\(37\)</span></td>
        <td><span class="arithmatex">\(23\)</span></td>
        <td><span class="arithmatex">\(29\)</span></td>
        <td><span class="arithmatex">\(26\)</span></td>
        </tr>
        <tr>
        <td><span class="arithmatex">\(4\)</span></td>
        <td><span class="arithmatex">\(39\)</span></td>
        <td><span class="arithmatex">\(25\)</span></td>
        <td><span class="arithmatex">\(31\)</span></td>
        <td><span class="arithmatex">\(28\)</span></td>
        </tr>
        </table></div>

        all with profit smaller than $41$: the sub-MIP with $\Delta(x,\tilde x)\le 2$ and the cutoff is infeasible. The optimum $(1,1,1,0,0,0)$ differs from $\tilde x$ in $x_1$, $x_3$, $x_4$: it is at distance $3$.

    4. <strong>Iteration 1.</strong> No solution of profit at least $41$ is at distance $\le2$ (question 3). At distance $3$ there are two: $(1,0,1,1,0,0)$, of weight $22$ and profit $41$, and $(1,1,1,0,0,0)$, of profit $43$. The proximity sub-MIP may return either.

        - If it returns $(1,1,1,0,0,0)$, the next sub-MIP asks for profit at least $44$ and is infeasible: the method stops, and $43$ is proved optimal.

        - If it returns $(1,0,1,1,0,0)$, the next sub-MIP asks for profit at least $42$: the closest solution is $(1,1,1,0,0,0)$, at distance $2$ (item $4$ out, item $2$ in); then profit $44$ is infeasible.

        RINS (hard fixing) succeeds because the variables on which $x^*$ and $\tilde x$ agree keep their optimal values; local branching with $k=2$ fails because the optimum is three changes away; proximity search finds it, since the size of the move is decided by the sub-MIP.

<a id="esercizio-10-7"></a>

!!! esercizio "Exercise 10.7: RINS and local branching 2"

    We consider a knapsack of capacity $c=13$ and a set $\mathscr{I}=\{1,2,\dots,6\}$ of $6$ items, sorted by non-increasing efficiency, with the following profits and weights:

    <div class="tabella" markdown="0"><table>
    <tr>
    <th></th>
    <th><span class="arithmatex">\(i=1\)</span></th>
    <th><span class="arithmatex">\(i=2\)</span></th>
    <th><span class="arithmatex">\(i=3\)</span></th>
    <th><span class="arithmatex">\(i=4\)</span></th>
    <th><span class="arithmatex">\(i=5\)</span></th>
    <th><span class="arithmatex">\(i=6\)</span></th>
    </tr>
    <tr>
    <td><span class="arithmatex">\(p_i\)</span></td>
    <td><span class="arithmatex">\(16\)</span></td>
    <td><span class="arithmatex">\(13\)</span></td>
    <td><span class="arithmatex">\(8\)</span></td>
    <td><span class="arithmatex">\(16\)</span></td>
    <td><span class="arithmatex">\(10\)</span></td>
    <td><span class="arithmatex">\(9\)</span></td>
    </tr>
    <tr>
    <td><span class="arithmatex">\(w_i\)</span></td>
    <td><span class="arithmatex">\(2\)</span></td>
    <td><span class="arithmatex">\(2\)</span></td>
    <td><span class="arithmatex">\(4\)</span></td>
    <td><span class="arithmatex">\(9\)</span></td>
    <td><span class="arithmatex">\(6\)</span></td>
    <td><span class="arithmatex">\(8\)</span></td>
    </tr>
    </table></div>

    The incumbent is the solution of the greedy algorithm $GR$ (non-increasing efficiency).

    **Questions**

    1. Compute the incumbent $\tilde x$ and the optimal solution $x^*$ of the LP relaxation.

    2. Apply RINS from $\tilde x$: which variables are fixed, and what does the sub-MIP return?

    3. Apply local branching from $\tilde x$ with $k=1$ and then, after adding the reverse constraint, with $k=2$. Write the local branching constraints explicitly.

    4. Prove that the solution found in question 3 is optimal, using the LP relaxation.

??? soluzione "Solution"

    1. $GR$ takes items $1,2,3$ (weight $8$, $rc=5$); items $4,5,6$ do not fit: $\tilde x=(1,1,1,0,0,0)$, profit $37$. The LP takes items $1,2,3$ and the fraction $\frac59$ of item $4$:

        $$
        x^*=\Big(1,1,1,\frac59,0,0\Big),\qquad z_{LP}(I)=37+\frac{80}{9}=\frac{413}{9}\approx45.89 .
        $$

    2. They agree on $x_1=x_2=x_3=1$ and $x_5=x_6=0$: only $x_4$ is free. The sub-MIP asks $8+9x_4\le13$ and $37+16x_4\ge38$: the first forces $x_4=0$, the second $x_4=1$. It is infeasible: <strong>RINS fails</strong> and returns $\tilde x$.

    3. <strong>$k=1$.</strong> The constraint is

        $$
        (1-x_1)+(1-x_2)+(1-x_3)+x_4+x_5+x_6\le 1 .
        $$

        The neighbours are the flips: adding item $4$, $5$ or $6$ gives weight $17$, $14$, $16>13$; removing an item decreases the profit. No solution of profit at least $38$: add the reverse constraint $(1-x_1)+(1-x_2)+(1-x_3)+x_4+x_5+x_6\ge 2$ and set $k=2$.

        <strong>$k=2$.</strong> Now $2\le\Delta(x,\tilde x)\le 2$. Two removals lose profit, two additions exceed the capacity; the swaps (one of $1,2,3$ out, one of $4,5,6$ in) that fit are

        <div class="tabella" markdown="0"><table>
        <tr>
        <th>out  in</th>
        <th><span class="arithmatex">\(4\)</span></th>
        <th><span class="arithmatex">\(5\)</span></th>
        <th><span class="arithmatex">\(6\)</span></th>
        </tr>
        <tr>
        <td><span class="arithmatex">\(1\)</span></td>
        <td>— (weight <span class="arithmatex">\(15\)</span>)</td>
        <td><span class="arithmatex">\(31\)</span></td>
        <td>— (weight <span class="arithmatex">\(14\)</span>)</td>
        </tr>
        <tr>
        <td><span class="arithmatex">\(2\)</span></td>
        <td>— (weight <span class="arithmatex">\(15\)</span>)</td>
        <td><span class="arithmatex">\(34\)</span></td>
        <td>— (weight <span class="arithmatex">\(14\)</span>)</td>
        </tr>
        <tr>
        <td><span class="arithmatex">\(3\)</span></td>
        <td><span class="arithmatex">\(45\)</span></td>
        <td><span class="arithmatex">\(39\)</span></td>
        <td><span class="arithmatex">\(38\)</span></td>
        </tr>
        </table></div>

        The best is to swap item $3$ with item $4$: $(1,1,0,1,0,0)$, of weight $13$ and profit $45$.

    4. The LP relaxation gives $z(I)\le\frac{413}{9}\approx45.89$, and since the profits are integer, $z(I)\le45$. The solution of local branching attains this bound: it is <strong>optimal</strong>. RINS fixed $x_3=1$, the variable that had to change; local branching (soft fixing) lets the solver choose which variables change.

<a id="esercizio-10-8"></a>

!!! esercizio "Exercise 10.8: RINS and local branching 3"

    We consider an instance of $P\,||\,C_{\max}$ with $m=2$ machines and $6$ jobs with processing times

    <div class="tabella" markdown="0"><table>
    <tr>
    <th></th>
    <th><span class="arithmatex">\(j=1\)</span></th>
    <th><span class="arithmatex">\(j=2\)</span></th>
    <th><span class="arithmatex">\(j=3\)</span></th>
    <th><span class="arithmatex">\(j=4\)</span></th>
    <th><span class="arithmatex">\(j=5\)</span></th>
    <th><span class="arithmatex">\(j=6\)</span></th>
    </tr>
    <tr>
    <td><span class="arithmatex">\(t_j\)</span></td>
    <td><span class="arithmatex">\(9\)</span></td>
    <td><span class="arithmatex">\(3\)</span></td>
    <td><span class="arithmatex">\(9\)</span></td>
    <td><span class="arithmatex">\(5\)</span></td>
    <td><span class="arithmatex">\(8\)</span></td>
    <td><span class="arithmatex">\(2\)</span></td>
    </tr>
    </table></div>

    and the MIP model with variables $x_{jh}$ and $C_{\max}$. The incumbent is the solution of list scheduling ($LIST$) in the order $1,2,\dots,6$, each job to the least loaded machine (machine $1$ in case of ties).

    **Questions**

    1. Compute the incumbent and its makespan, and the lower bound $\max\{\max_j t_j,\lceil\sum_j t_j/m\rceil\}$.

    2. Apply the first iteration of proximity search with $\theta=1$: find the solution with $C_{\max}\le f(\tilde x)-1$ at minimum distance $\Delta(x,\tilde x)$ from the incumbent. (Recall that $\Delta=2r$, where $r$ is the number of jobs that change machine.)

    3. Apply the second iteration.

    4. Apply the third iteration, and conclude.

??? soluzione "Solution"

    1. Job $1\to M_1$ ($9,0$), job $2\to M_2$ ($9,3$), job $3\to M_2$ ($9,12$), job $4\to M_1$ ($14,12$), job $5\to M_2$ ($14,20$), job $6\to M_1$ ($16,20$). The incumbent is $J_1=\{1,4,6\}$, $J_2=\{2,3,5\}$, with loads $16$ and $20$: makespan $20$. The lower bound is $\max\{9,\lceil 36/2\rceil\}=18$.

    2. The cutoff is $C_{\max}\le19$. With $r=1$ (distance $2$) a single job moves; moving a job to $M_2$ makes its load larger than $20$, so the job moves from $M_2$ to $M_1$, and needs $20-t_j\le19$ and $16+t_j\le19$, i.e., $1\le t_j\le3$: only job $2$. The new incumbent is $J_1=\{1,2,4,6\}$, $J_2=\{3,5\}$, with loads $19$, $17$: makespan $19$, at distance $2$.

    3. The cutoff is $C_{\max}\le18$: since the total is $36$, both loads must be exactly $18$, i.e., $M_1$ must lose exactly $1$. A single move would need a job with $t_j=1$: none (distance $2$ is impossible). With two jobs ($r=2$, distance $4$): two jobs leaving $M_1$ would need $t_i+t_j=1$, impossible; a swap of $i\in J_1$ with $j\in J_2$ needs $t_i-t_j=1$, and the only pair is job $1$ ($9$) with job $5$ ($8$). The new incumbent is $J_1=\{2,4,5,6\}$, $J_2=\{1,3\}$, with loads $18$, $18$: makespan $18$, at distance $4$.

    4. The cutoff is $C_{\max}\le17$, but $C_{\max}\ge 36/2=18$ for every assignment: the proximity sub-MIP is infeasible, and the method stops. By the proposition on proximity search, makespan $18$ is <strong>optimal</strong>; it also attains the lower bound of question 1.
