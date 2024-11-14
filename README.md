# Lottery Number Prediction Model

This repository contains an AMPL model for predicting lottery numbers based on historical data.

## Mathematical Formulation

### Sets and Parameters
- DRAWS: Set of historical draws
- Hovedtall[i,j]: Historical main numbers, where i ∈ DRAWS, j ∈ {1,2,3,4,5}
- Stjernetall[i,k]: Historical star numbers, where i ∈ DRAWS, k ∈ {1,2}

### Decision Variables
- Next_Hovedtall[j]: Integer variables for the next main numbers, j ∈ {1,2,3,4,5}
- Next_Stjernetall[k]: Integer variables for the next star numbers, k ∈ {1,2}
- z[i,j]: Binary variables for enforcing uniqueness of main numbers
- w: Binary variable for enforcing uniqueness of star numbers

### Objective Function
Minimize the sum of squared differences between predicted numbers and historical draws:

$$

\min \sum_{i \in \text{DRAWS}} \left[ \sum_{j=1}^5 (\text{Next\_Hovedtall}[j] - \text{Hovedtall}[i,j])^2 + \sum_{k=1}^2 (\text{Next\_Stjernetall}[k] - \text{Stjernetall}[i,k])^2 \right]
$$

### Constraints

1. Domain constraints:
   $$1 \leq \text{Next\_Hovedtall}[j] \leq 50, \quad \forall j \in \{1,2,3,4,5\}$$
   $$1 \leq \text{Next\_Stjernetall}[k] \leq 12, \quad \forall k \in \{1,2\}$$

2. Uniqueness constraints for main numbers:
   For $i \in \{1,2,3,4\}, j \in \{i+1,\ldots,5\}$:
   $$\text{Next\_Hovedtall}[i] - \text{Next\_Hovedtall}[j] \leq -1 + 100z[i,j]$$
   $$\text{Next\_Hovedtall}[j] - \text{Next\_Hovedtall}[i] \leq -1 + 100(1-z[i,j])$$

3. Uniqueness constraints for star numbers:
   $$\text{Next\_Stjernetall}[1] - \text{Next\_Stjernetall}[2] \leq -1 + 100w$$
   $$\text{Next\_Stjernetall}[2] - \text{Next\_Stjernetall}[1] \leq -1 + 100(1-w)$$

4. Ascending order constraints:
   For $i \in \{1,2,3,4\}$:
   $$\text{Next\_Hovedtall}[i] + \varepsilon \leq \text{Next\_Hovedtall}[i+1]$$
   $$\text{Next\_Stjernetall}[1] + \varepsilon \leq \text{Next\_Stjernetall}[2]$$

   where $\varepsilon$ is a small positive number.

## Solution Approach

This formulation results in a Mixed-Integer Quadratic Programming (MIQP) problem. It is solved using advanced optimization solvers such as Gurobi or CPLEX, which employ techniques like branch-and-bound, cutting planes, and heuristics to find the optimal solution.
