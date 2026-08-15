# Linear-Programming

## Case Study: Optimizing Production and Shipping Costs for XYZ Manufacturing Ltd.

XYZ Manufacturing Ltd. is a consumer goods manufacturer that operates **three production plants** supplying products to a central warehouse. Since the products have a **very short shelf life**, anything produced during a quarter must be shipped immediately to the warehouse—inventory cannot be carried over to future quarters.

The company plans production over **four quarters** of the year. Warehouse demand has already been forecasted by the sales team as follows:

| Quarter | Warehouse Demand (Units) |
|---|---:|
| Q1 | 180 |
| Q2 | 80 |
| Q3 | 190 |
| Q4 | 160 |

Each plant has different transportation costs because they are located at different distances from the warehouse.

| Plant | Shipping Cost per Unit |
|---|---:|
| Plant 1 | $1.25 |
| Plant 2 | $1.84 |
| Plant 3 | $1.45 |

Management wants to determine **how many units each plant should produce in each quarter** so that the **total annual shipping cost is as low as possible**, while satisfying all operational constraints.

### Business Constraints

The production plan must satisfy the following conditions:

**1. Minimum Production Requirement**

To justify remaining operational, each plant must produce **at least 20 units every quarter**.

---

**2. Maximum Production Capacity**

Each plant has a limited production capacity.

| Plant | Maximum Units per Quarter |
|---|---:|
| Plant 1 | 92 |
| Plant 2 | 45 |
| Plant 3 | 55 |

No plant can produce more than its quarterly capacity.

---

**3. Demand Satisfaction**

Because the products are highly perishable, production must exactly match warehouse demand every quarter.

In other words,

**Total Production = Warehouse Demand**

for each quarter.

---

**4. Shipping Costs**

Every unit produced must be transported to the warehouse.

Since shipping costs differ by plant location, management wants to allocate production intelligently so that the **overall shipping cost is minimized**.

---

## Decision Variables

For each quarter, determine the number of units produced by:

- Plant 1
- Plant 2
- Plant 3

For example:

- $P_{1,Q1}$ = Units produced by Plant 1 during Quarter 1
- $P_{2,Q3}$ = Units produced by Plant 2 during Quarter 3
- $P_{3,Q4}$ = Units produced by Plant 3 during Quarter 4

These are the unknown values that the optimization model will determine.

---

## Objective

Determine the quarterly production schedule for all three plants that **minimizes the total annual shipping cost** while:

- meeting warehouse demand exactly,
- respecting each plant's minimum production requirement,
- staying within each plant's production capacity.

---

### Business Question

> **As the Operations Research Analyst at XYZ Manufacturing Ltd., develop a Linear Programming model that determines the optimal quarterly production quantities for each of the company's three plants in order to minimize total shipping costs while satisfying all production capacity and warehouse demand constraints.**

--- 
### Python Implementation

```python
# -----------------------------
# Import the necessary library
# -----------------------------

import pulp as lp

# -----------------------------
# Data
# -----------------------------

plants = ["Plant 1", "Plant 2", "Plant 3"]
quarters = ["Q1", "Q2", "Q3", "Q4"]

shipping_cost = {
    "Plant 1": 1.25,
    "Plant 2": 1.84,
    "Plant 3": 1.45
}

capacity = {
    "Plant 1": 92,
    "Plant 2": 45,
    "Plant 3": 55
}

minimum_production = 20

demand = {
    "Q1": 180,
    "Q2": 80,
    "Q3": 190,
    "Q4": 160
}

# -----------------------------
# Model
# -----------------------------

model = lp.LpProblem(
    "XYZ_Manufacturing_Shipping_Optimization",
    lp.LpMinimize
)

# -----------------------------
# Decision Variables
# -----------------------------

production = lp.LpVariable.dicts(
    "Production",
    [(plant, quarter)
     for plant in plants
     for quarter in quarters],
    lowBound=0
)

# -----------------------------
# Objective Function
# -----------------------------

model += lp.lpSum(
    shipping_cost[plant] * production[(plant, quarter)]
    for plant in plants
    for quarter in quarters
)

# -----------------------------
# Demand Constraints
# -----------------------------

for quarter in quarters:

    model += (
        lp.lpSum(
            production[(plant, quarter)]
            for plant in plants
        )
        == demand[quarter]
    )

# -----------------------------
# Capacity Constraints
# -----------------------------

for plant in plants:
    for quarter in quarters:

        model += (
            production[(plant, quarter)]
            <= capacity[plant]
        )

# -----------------------------
# Minimum Production
# -----------------------------

for plant in plants:
    for quarter in quarters:

        model += (
            production[(plant, quarter)]
            >= minimum_production
        )

# -----------------------------
# Solve
# -----------------------------

model.solve()

print("Status:", lp.LpStatus[model.status])

print("\nOptimal Production Plan\n")

for quarter in quarters:

    print(quarter)

    for plant in plants:

        print(
            f"{plant}: "
            f"{production[(plant, quarter)].varValue:.0f}"
        )

    print()

```
---

### 📊 Optimal Production Plan

**Status:** `Optimal`

| Quarter | 🏭 Plant 1 | 🏭 Plant 2 | 🏭 Plant 3 |
|:---:|---:|---:|---:|
| **Q1** | 92 | 33 | 55 |
| **Q2** | 40 | 20 | 20 |
| **Q3** | 92 | 43 | 55 |
| **Q4** | 92 | 20 | 48 |

### 💰 Minimum Shipping Cost

> **Minimum Annual Shipping Cost: $866.54**
