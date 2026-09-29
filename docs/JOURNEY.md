# Journey log

A dated record of where this project started and how it changed. Newest entries at the bottom.

## 2026-09-29 · Starting point

**Idea.** Quantum navigation and path prediction, especially in space. Narrowed to one research question: under what conditions does QAOA beat classical path planning for asteroid navigation?

**Goals set.** Four deliverables: working code (classical vs QAOA with benchmarks), an interactive visualizer, a 5 to 8 page paper, and a clean GitHub repo.

**Background.** Ravi is a software engineer new to quantum computing and path planning, so the first step was learning the concepts.

**Initial plan (four phases).**
1. Problem model (QUBO) and classical baseline (A*, brute force / ILP)
2. QAOA solver in Qiskit
3. Benchmark study and paper
4. Visualizer and portfolio repo

## 2026-09-29 · Learned the basics, surveyed prior work

- Wrote the [primer](primer.md) covering routing as optimization, classical solvers, QUBO, qubits, QAOA, simulators vs hardware, and quantum advantage.
- Surveyed [prior work](prior-work.md). Key findings:
  - QAOA on small TSPs is well studied. A 2026 comparison found QAOA topped out at 7 cities and lost to simulated annealing.
  - Space applications so far use D-Wave quantum annealing, not gate-based QAOA. The closest work is multi-target space-debris removal.
  - Nobody has published an open, reproducible QAOA benchmark on asteroid tours with realistic Δv costs. That is our gap.
- Proposed plan changes: [decision 0001](decisions/0001-reframe-research-question.md) (status: proposed).

**Where we are:** ready to start phase 1.

## 2026-09-29 · Clarified the problem

The project is about **navigating through** asteroids and debris: a safe, fuel-efficient path from start to goal. It is not about choosing the order to visit asteroids.

What changed:
- The model becomes a shortest collision-free path on a grid (space-time grid for moving obstacles), not a TSP.
- A* and Dijkstra are back as the main classical baselines, since this is exactly what they're built for.
- The QAOA encoding uses one qubit per grid edge, so simulator limits mean small grids (about 4×4 to 5×5).
- The most relevant prior work is now QAOA for shortest path and grid path planning. The TSP and debris-removal studies remain useful background.
- [Decision 0001](decisions/0001-reframe-research-question.md) was rewritten to match (status: proposed).

## 2026-09-29 · Repo created

Created this repo, [qaoa-space-pathfinding](https://github.com/cypher-ravi/qaoa-space-pathfinding), to document the project from the start. Everything above this entry was written before the repo existed.
