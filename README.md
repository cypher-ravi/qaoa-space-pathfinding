# QAOA Space Pathfinding

Can a quantum algorithm (QAOA) plan a better path through an asteroid and debris field than classical path planners? This repo is the honest, reproducible answer, plus a record of how we got there.

> **Status (2026-09-29):** Research and planning. No code yet. See [the journey log](docs/JOURNEY.md) for where we are.

## The question

A spacecraft must get from a start point to a goal through a field of asteroids and debris, without hitting anything and using as little fuel (Δv) as possible. We model space as a graph of safe waypoints (a grid, extended over time when obstacles move) and look for the cheapest collision-free path. We compare:

- **Classical solvers:** Dijkstra and A* (the standard path planners), an exact ILP solver, and simulated annealing
- **Quantum solver:** QAOA in Qiskit on the same problem written as a QUBO, run on a simulator (and maybe once on real IBM hardware)

We measure path cost, how often the quantum solver returns a valid collision-free path, and runtime as the field grows (grid size, number of obstacles).

## Roadmap

| # | Phase | Output | Status |
|---|-------|--------|--------|
| 0 | Learn the basics and survey prior work | [Primer](docs/primer.md), [prior work](docs/prior-work.md) | ✅ Done |
| 1 | Problem model and classical baselines | Field generator, QUBO model, A* / Dijkstra / ILP / annealing, benchmarks | ⏳ Next |
| 2 | QAOA solver | Qiskit QAOA on the same QUBO, depth and size sweeps | ○ |
| 3 | Benchmark study and paper | Head-to-head results, 5 to 8 page write-up | ○ |
| 4 | Visualizer and polish | Side-by-side interactive visualizer, docs | ○ |

## Where to read

- **Website:** https://cypher-ravi.github.io/qaoa-space-pathfinding/ (the illustrated primer; the visualizer will live here too)
- [docs/JOURNEY.md](docs/JOURNEY.md): dated log of what we did, learned, and decided
- [docs/primer.md](docs/primer.md): every concept from scratch, for software engineers
- [docs/prior-work.md](docs/prior-work.md): what others have already done, and our gap
- [docs/decisions/](docs/decisions/): one file per important decision and why

## Stack

Python, Qiskit, OR-Tools. Details will land with phase 1.

## Website

Everything in [`site/`](site/) is published to GitHub Pages by [`.github/workflows/pages.yml`](.github/workflows/pages.yml) on every push to `main` that touches it.
