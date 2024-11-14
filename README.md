# Lottery Draw Optimization Model

This model is designed to predict two lottery combinations by minimizing the total squared error from historical lottery draws while enforcing uniqueness and diversity constraints.

## Sets

- \( DRAWS \): Set of historical lottery draws.
- \( COMBINATIONS = \{1, 2\} \): Set representing two lottery combinations to be predicted.

## Parameters

- \( Hovedtall_{i,j} \): Main numbers from historical draws, where \( i \in DRAWS \) and \( j \in \{1, \dots, 5\} \) for each draw.
- \( Stjernetall_{i,k} \): Star numbers from historical draws, where \( i \in DRAWS \) and \( k \in \{1, 2\} \).
- \( \epsilon = 10^{-6} \): Small positive number for enforcing strict inequality in ordering constraints.

## Variables

- \( \text{Next\_Hovedtall}_{c,j} \): The main numbers for the \( c \)-th combination, where \( c \in COMBINATIONS \) and \( j \in \{1, \dots, 5\} \). These are integer variables constrained to the range \( [1, 50] \).
- \( \text{Next\_Stjernetall}_{c,k} \): The star numbers for the \( c \)-th combination, where \( k \in \{1, 2\} \). These are integer variables constrained to the range \( [1, 12] \).
- \( z_{c,i,j} \): Binary variables to ensure uniqueness of main numbers within each combination.
- \( w_{c} \): Binary variables to ensure uniqueness of star numbers within each combination.

## Objective Function

The objective function minimizes the total squared difference between the predicted and historical main and star numbers for each combination:

\[
\text{Minimize } \quad \text{Total\_Error} = \sum_{c \in COMBINATIONS} \sum_{i \in DRAWS} \left( \sum_{j=1}^5 (\text{Next\_Hovedtall}_{c,j} - Hovedtall_{i,j})^2 + \sum_{k=1}^2 (\text{Next\_Stjernetall}_{c,k} - Stjernetall_{i,k})^2 \right)
\]

## Constraints

### 1. **Uniqueness of Main Numbers**

Each main number within a combination must be unique. This is enforced by the binary variables \( z_{c,i,j} \):

\[
\text{Next\_Hovedtall}_{c,i} - \text{Next\_Hovedtall}_{c,j} \leq -1 + 100 \cdot z_{c,i,j} \quad \forall c \in COMBINATIONS, \, i \neq j
\]
\[
\text{Next\_Hovedtall}_{c,j} - \text{Next\_Hovedtall}_{c,i} \leq -1 + 100 \cdot (1 - z_{c,i,j}) \quad \forall c \in COMBINATIONS, \, i \neq j
\]

### 2. **Uniqueness of Star Numbers**

Each star number within a combination must also be unique:

\[
\text{Next\_Stjernetall}_{c,1} - \text{Next\_Stjernetall}_{c,2} \leq -1 + 100 \cdot w_{c} \quad \forall c \in COMBINATIONS
\]
\[
\text{Next\_Stjernetall}_{c,2} - \text{Next\_Stjernetall}_{c,1} \leq -1 + 100 \cdot (1 - w_{c}) \quad \forall c \in COMBINATIONS
\]

### 3. **Ascending Order**

The numbers within each combination must be in ascending order to ensure a consistent prediction format:

\[
\text{Next\_Hovedtall}_{c,i} + \epsilon \leq \text{Next\_Hovedtall}_{c,i+1} \quad \forall c \in COMBINATIONS, \, i = 1, \dots, 4
\]
\[
\text{Next\_Stjernetall}_{c,1} + \epsilon \leq \text{Next\_Stjernetall}_{c,2} \quad \forall c \in COMBINATIONS
\]

### 4. **Diversity Between Combinations**

The two combinations should differ sufficiently to avoid near-identical predictions:

\[
\sum_{j=1}^5 \left| \text{Next\_Hovedtall}_{1,j} - \text{Next\_Hovedtall}_{2,j} \right| + \sum_{k=1}^2 \left| \text{Next\_Stjernetall}_{1,k} - \text{Next\_Stjernetall}_{2,k} \right| \geq 10
\]

### 5. **Diversity in Main and Star Numbers Between Combinations**

To further ensure diversity, each main and star number in one combination must differ by at least 2 or 1 from the corresponding number in the other combination, respectively:

\[
\left| \text{Next\_Hovedtall}_{1,j} - \text{Next\_Hovedtall}_{2,j} \right| \geq 2 \quad \forall j = 1, \dots, 5
\]
\[
\left| \text{Next\_Stjernetall}_{1,k} - \text{Next\_Stjernetall}_{2,k} \right| \geq 1 \quad \forall k = 1, 2
\]

This model is implemented in AMPL and can be used to generate unique, diverse, and optimized lottery predictions based on historical data.
