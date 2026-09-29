# Primer: quantum navigation through asteroids from scratch

Written for software engineers with no background in quantum computing or path planning. An illustrated version is on the [project website](https://cypher-ravi.github.io/qaoa-space-pathfinding/).

## 1. Path planning is an optimization problem

Path planning covers two questions:

- **Point-to-point:** how do I get from A to B while avoiding obstacles?
- **Ordering, or routing:** in what order do I visit many places? (the Traveling Salesman Problem)

**Our project is the first kind, with physics.** A spacecraft must reach a goal through a field of asteroids and debris. We want a **trajectory** (its route through space over time) that uses the least fuel (Δv) and **never collides**.

Space differs from a road map in one key way: **momentum**. A spacecraft keeps drifting at its current velocity for free, and every change of speed or direction needs a burn that costs fuel. So we plan over *states*, not just positions: a state is (position, velocity) at a moment in time. Each step, the spacecraft either coasts or fires a small burn, which decides the next state. States that overlap an asteroid or debris at that moment are removed. This graph of states is called a **state lattice**.

> **SDE analogy:** a state machine where each transition has a cost. Nodes are (position, velocity, time), edges are "coast" or "burn", and some nodes are forbidden because a rock is there at that moment. Find the cheapest run from the start state to any goal state.

The search space grows fast: even a 10×10 grid has an enormous number of possible paths, so you need a clever search, not enumeration.

## 2. Classical solvers

- **Brute force:** try every order, O(n!). Practical to about 10 to 12 asteroids.
- **Held–Karp:** dynamic programming that memoizes "best cost having visited this set and standing at asteroid k". O(n²·2ⁿ), practical to about 20 to 25. Gives the true optimum, our ground truth.
- **Dijkstra:** explores the graph outward from the start in order of cost using a priority queue; guaranteed shortest path.
- **A\*:** Dijkstra plus a heuristic estimate of the remaining cost, so it explores promising directions first. Run over a state lattice it's called *kinodynamic* planning. **The standard tool for our problem**, and the main baseline.
- **Continuous trajectory optimization:** real missions optimize smooth trajectories with calculus-based solvers (e.g. direct collocation). We use a discrete lattice instead so that classical and quantum solvers attack exactly the same problem.
- **Integer Linear Programming (ILP):** describe variables, constraints and objective; a solver (OR-Tools, HiGHS, Gurobi) prunes the search with branch-and-bound. *Analogy: SQL for optimization. You say what, the engine figures out how.*
- **Simulated annealing:** random swaps; keep improvements, sometimes accept worse moves with a probability that shrinks over time. Fast and simple, and usually what QAOA loses to.

## 3. QUBO

Quantum optimizers accept one problem shape: **Quadratic Unconstrained Binary Optimization**.

- Every decision is a bit.
- Cost is a sum of terms with at most two bits: `cost(x) = Σ Q_ij · x_i · x_j`.
- No separate constraints. Rules become **penalty terms** added to the cost.

Warm-up version, a static path: `x[e] = 1` means "edge e is part of the path", one bit per usable edge. The full trajectory version uses one bit per (time step, state), with a pairwise term for the Δv of each allowed transition and penalties for impossible ones. Same idea, more bits.

```
cost(x) =  Σ_e dv[e] * x[e]                            # fuel for each step taken
        + P * (1 - Σ_{e leaving start} x[e])²          # leave the start exactly once
        + P * (1 - Σ_{e entering goal} x[e])²          # reach the goal exactly once
        + P * Σ_{other nodes v} (Σ_in x[e] - Σ_out x[e])²   # whatever enters a node leaves it
# edges touching asteroids/debris are simply left out of the graph
```

> **SDE analogy:** replacing `throw` on invalid input with a huge score deduction. Invalid answers still get proposed, so we must check and count them.

## 4. Qubits and circuits

- A **qubit** holds two amplitudes (for 0 and 1). **Measuring** gives 0 or 1 at random, weighted by the amplitudes squared.
- *n* qubits hold one amplitude for every one of the 2ⁿ bitstrings.
- **Gates** are operations; a **circuit** is the program; you run it many times (**shots**) and get a histogram.
- **Entanglement:** correlated outcomes that can't be described qubit by qubit.
- **Interference:** amplitudes can cancel or add up. Algorithms use this to boost good answers.

> **SDE analogy:** a probability distribution over all 2ⁿ bitstrings that you can reshape but only sample from. It does **not** try every answer in parallel and return the best.

## 5. QAOA

The Quantum Approximate Optimization Algorithm alternates two layers, repeated *p* times (the depth):

- **Cost layer (angle γ):** marks each bitstring according to its QUBO cost.
- **Mixer layer (angle β):** lets amplitude flow toward the marked good answers.

A classical optimizer (COBYLA, SPSA) tunes the 2p angles to minimize the average sampled cost. Take the best valid bitstring and decode it into a path.

> **SDE analogy:** hyperparameter tuning. The circuit is a model with 2p knobs, and the loss is the average cost of its samples.

Known issues on path and routing QUBOs: many invalid samples (broken or looping paths), flat optimization landscapes as size grows, and n² qubits. Helpful variants: warm starting from a classical solution, and a constraint-preserving **XY mixer**.

## 6. Simulators vs hardware

A statevector simulator stores all 2ⁿ amplitudes (16 bytes each):

| Static grid | Edges = qubits | RAM |
|-------------|----------------|-----|
| 3×3 | 12 | 64 KB |
| 4×4 | 24 | 256 MB |
| 5×5 | 40 | 16 TB (needs pruning) |

Obstacles remove edges, and edges far from any sensible path can be pruned classically, so real counts are lower. A space-time grid multiplies the count by the number of time steps.

Real hardware (e.g. IBM) has 100+ qubits but is noisy, has limited qubit connectivity, and queues jobs. Deep circuits on 25+ qubits come out mostly as noise today.

> **SDE analogy:** the simulator is a perfect local dev environment that can't scale; hardware is production on flaky infrastructure.

## 7. Quantum advantage, honestly

- **Provable:** a proof that quantum needs fewer steps (e.g. Shor's factoring). None exists for QAOA on path planning.
- **Empirical scaling:** runtime grows more slowly than the best classical method. Best QAOA evidence: [Shaydulin et al. 2024](https://arxiv.org/abs/2308.02342), on a problem chosen to be classically hard.
- **Practical:** a real task done better on real hardware. Not shown for routing.

Our expectation: QAOA will not beat good classical methods at sizes we can simulate. The valuable output is a careful measurement of the gap and how it scales.
