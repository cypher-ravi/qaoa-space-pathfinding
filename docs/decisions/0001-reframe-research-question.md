# 0001 · Define the problem and reframe the research question

- **Date:** 2026-09-29
- **Status:** Proposed

## Context

The project is about navigating a spacecraft **through** a field of asteroids and debris: get from start to goal safely and cheaply. An earlier draft of this record treated it as choosing the order to visit asteroids (a TSP). That was the wrong problem and has been replaced.

Prior work ([prior-work.md](../prior-work.md)) shows QAOA does not beat good classical methods on routing or shortest-path problems at sizes a simulator can handle.

## Decision

1. **Problem:** collision-free, minimum-cost path from start to goal. Space is discretized into a grid graph; cells blocked by asteroids or debris are removed. Moving obstacles are handled with a space-time grid (a node is a cell at a time step). Edge costs approximate Δv (a turn or speed change costs more than coasting).
2. **Research question:** "How do QAOA's path quality, valid-path rate, and cost scale with field size, obstacle density and circuit depth, and how big is the gap to classical?" We expect no crossover in reach and will report that honestly.
3. **Classical baselines:** Dijkstra and A* (the natural fit for this problem), an exact ILP formulation, and simulated annealing on the exact same QUBO as QAOA.
4. **QAOA encoding:** one qubit per edge ("is this edge on the path?"), with penalty terms that force a single connected path from start to goal. Qubits = number of usable edges, so the simulator limit (about 25 to 30 qubits) means roughly a 4×4 to 5×5 static grid. We sweep depth p = 1 to 5 and try one improved variant (warm start or pruning edges classically first). One small real-hardware run is a stretch goal.
5. **Realism:** start with synthetic fields, then add a scenario built from real asteroid or debris positions (NASA JPL small-body database or public debris catalogs) scaled to a small grid.

## Consequences

The paper becomes a careful scaling study of quantum path planning in a hazard field. The problem sizes QAOA can reach are small, so a clear visualization of each solver's path over the same field matters for the portfolio.
