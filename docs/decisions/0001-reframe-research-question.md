# 0001 · Define the problem and reframe the research question

- **Date:** 2026-09-29
- **Status:** Proposed

## Context

The project is about **finding a spacecraft trajectory** from start to goal that is fuel-efficient **and navigates safely** through moving asteroids and debris. An earlier draft of this record treated it as choosing the order to visit asteroids (a TSP), and a second draft as a plain grid path that ignored momentum. Both were replaced.

Prior work ([prior-work.md](../prior-work.md)) shows QAOA does not beat good classical methods on routing or shortest-path problems at sizes a simulator can handle.

## Decision

1. **Problem:** minimum-Δv, collision-free trajectory from start to goal. We use a *state lattice*: a state is (position cell, velocity) at a time step. From each state the spacecraft either coasts (free) or fires a small burn that changes velocity (costs Δv), which fixes the next state. States that overlap an asteroid or debris at that time step are removed. This captures momentum (you can't turn instantly) and moving hazards in one graph.
2. **Research question:** "How do QAOA's trajectory quality, valid-trajectory rate, and cost scale with grid size, time horizon, hazard density and circuit depth, and how big is the gap to classical?" We expect no crossover in reach and will report that honestly.
3. **Classical baselines:** Dijkstra and A* over the state lattice (the standard "kinodynamic" planning approach), an exact ILP formulation, and simulated annealing on the exact same QUBO as QAOA.
4. **QAOA encoding:** one qubit per (time step, lattice state), "the spacecraft is in this state at this time". Penalties force exactly one state per time step and only allow transitions a coast or burn can make; the Δv of each transition is a pairwise cost term. All terms are quadratic, so this is a valid QUBO. Qubits = time steps × usable states, so the simulator limit (about 25 to 30 qubits) means tiny scenarios: a few time steps on a small 2D grid with 2 to 3 velocity options. A simpler static-grid path (one qubit per edge) is the warm-up. We sweep depth p = 1 to 5 and try one improved variant (warm start or pruning edges classically first). One small real-hardware run is a stretch goal.
5. **Realism:** start with synthetic 2D fields, then add a scenario built from real asteroid or debris positions (NASA JPL small-body database or public debris catalogs) scaled to a small grid.

## Consequences

The paper becomes a careful scaling study of quantum path planning in a hazard field. The problem sizes QAOA can reach are small, so a clear visualization of each solver's path over the same field matters for the portfolio.
