# Full index

## [Models and bounds](models-and-bounds/index.md)

1. [Integer linear programming](models-and-bounds/ilp.md)
    - 1.1 Why integer linear programming?
    - 1.2 A first model: the car example
    - 1.3 Definitions
    - 1.4 Feasible and optimal solutions of the car example
    - 1.5 Complete enumeration
    - 1.6 Lower bounds from feasible solutions
    - 1.7 Typical mistakes
    - 1.8 In summary
2. [LP relaxation and dual bounds](models-and-bounds/lp-relaxation.md)
    - 2.1 Why a relaxation?
    - 2.2 The LP relaxation
    - 2.3 Rounding the solution of the LP relaxation
    - 2.4 Upper bounds on the variables
    - 2.5 Dual bounds
    - 2.6 Typical mistakes
    - 2.7 In summary

## [Strengthening the formulation](formulations/index.md)

3. [Ideal formulations](formulations/ideal-formulations.md)
    - 3.1 Why ideal formulations?
    - 3.2 The convex hull
    - 3.3 Formulations and ideal formulations
    - 3.4 Comparing formulations
    - 3.5 Good news and bad news
    - 3.6 Typical mistakes
    - 3.7 In summary
4. [Valid inequalities](formulations/valid-inequalities.md)
    - 4.1 Why valid inequalities?
    - 4.2 Valid inequalities
    - 4.3 Deriving valid inequalities by rounding
    - 4.4 Improving a valid inequality
    - 4.5 What comes next
    - 4.6 Typical mistakes
    - 4.7 In summary
5. [Cover inequalities](formulations/cover.md)
    - 5.1 Knapsack constraints and their LP relaxation
    - 5.2 Covers and cover inequalities
    - 5.3 Geometric interpretation
    - 5.4 Separation of cover inequalities
    - 5.5 Transforming the CSP into a Knapsack Problem
    - 5.6 Extended cover inequalities
    - 5.7 Violation and distance from a cover inequality
    - 5.8 Typical mistakes
    - 5.9 In summary

## [Exact methods](exact-methods/index.md)

6. [The cutting plane algorithm with Gomory fractional cuts](exact-methods/gomory.md)
    - 6.1 Why a cutting plane algorithm?
    - 6.2 Gomory fractional cuts
    - 6.3 A cutting-plane algorithm based on Gomory fractional cuts
    - 6.4 The car example
    - 6.5 Typical mistakes
    - 6.6 In summary
7. [LP-based branch-and-bound](exact-methods/bb.md)
    - 7.1 Why a branch-and-bound algorithm?
    - 7.2 The idea in words and in a picture
    - 7.3 Definitions and notation
    - 7.4 The algorithm
    - 7.5 A short recap of the dual simplex algorithm
    - 7.6 Branching-variable selection strategies
    - 7.7 Branching-node selection strategies
    - 7.8 Worked examples on the car example
    - 7.9 Typical mistakes
    - 7.10 Extensions
    - 7.11 In summary
8. [Branch-and-cut](exact-methods/branch-and-cut.md)
    - 8.1 Why branch-and-cut algorithms?
    - 8.2 Global and local valid inequalities
    - 8.3 Cut-and-branch and branch-and-cut
    - 8.4 Worked example: cut-and-branch on a knapsack instance
    - 8.5 Local cuts: an example
    - 8.6 Practical aspects
    - 8.7 Typical mistakes
    - 8.8 In summary

## [MIP solvers](solvers/index.md)

9. [MIP solvers: what they do](solvers/mip-solvers.md)
    - 9.1 From the branch-and-cut algorithm to a MIP solver
    - 9.2 The components of a MIP solver
    - 9.3 How to read the log of Gurobi
    - 9.4 Typical mistakes
    - 9.5 In summary

## [MIP heuristics](mip-heuristics/index.md)

10. [MIP heuristics](mip-heuristics/mip-heuristics.md)
    - 10.1 MIP-based heuristics
    - 10.2 The problems and their MIP models
    - 10.3 Rounding
    - 10.4 Diving
    - 10.5 The feasibility pump
    - 10.6 Improving an incumbent with a sub-MIP
    - 10.7 Relax-and-fix and fix-and-optimize
    - 10.8 Heuristics inside the solver
    - 10.9 What MIP-based heuristics give
    - 10.10 <code>Python</code> implementation
    - 10.11 Typical mistakes
    - 10.12 In summary
