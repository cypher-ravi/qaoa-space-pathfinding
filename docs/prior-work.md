# Prior work

What already exists, and where this project can add something. Our problem is finding a fuel-efficient **trajectory** that **navigates** a moving asteroid and debris field. The shortest-path and grid-planning rows cover the navigation side, the quantum trajectory-optimization papers (Carbone et al., De Grossi et al.) cover the trajectory side, and the TSP and routing rows are useful background. Summaries come from each paper's abstract or publisher page; check exact numbers in the paper before citing.

## Quantum approaches to TSP, routing and path planning

| Work | What they did | Takeaway |
|------|---------------|----------|
| [Lucas 2014](https://arxiv.org/abs/1302.5843) | QUBO/Ising formulations of many NP-hard problems, including TSP | The standard n²-qubit TSP encoding |
| [Farhi, Goldstone, Gutmann 2014](https://arxiv.org/abs/1411.4028) | Introduced QAOA | The algorithm we use |
| [Qiskit Optimization tutorial](https://qiskit-community.github.io/qiskit-optimization/tutorials/06_examples_max_cut_and_tsp.html) | Max-Cut and TSP with QAOA/VQE on a simulator | Starter code, 3 to 4 cities |
| [TSP classical vs quantum framework (2026)](https://arxiv.org/html/2607.24581v1) | Brute force, MST, simulated annealing and QAOA compared | Closest to our study. QAOA not competitive, capped at 7 cities |
| [Quantum methods for Generalized TSP (2026)](https://arxiv.org/html/2604.25531) | Variant that picks one city per group | Relevant if the tour chooses among asteroids |
| [Shortest path with QAOA (2023)](https://ui.adsabs.harvard.edu/abs/2023Spin...1350002F/abstract), [Parallel QAOA grid planning (2025)](https://arxiv.org/abs/2510.07413) | QAOA for point-to-point paths | Toy sizes, relies on classical filtering |
| [Multi-robot path planning via QUBO (2026)](https://arxiv.org/html/2602.14799) | QUBO for coordinating robots | QUBO modelling style for robotics |
| [VRP with annealing (2019)](https://arxiv.org/abs/1903.06322v1), [weighted-segment VRP (2022)](https://arxiv.org/abs/2203.13469), [VRP with time windows (2025)](https://arxiv.org/html/2503.24285) | Vehicle routing on D-Wave | Hybrid quantum-classical is the norm |

## Quantum optimization for space

| Work | What they did | Takeaway |
|------|---------------|----------|
| [Multi-target debris removal (EPJ Quantum Tech, 2025)](https://link.springer.com/article/10.1140/epjqt/s40507-025-00409-3) | Order debris captures (Δv, time of flight, priority) as QUBO on D-Wave vs simulated annealing, tabu, GA | Most similar to our idea. Annealing, not QAOA |
| [Carbone, De Grossi, Spiller 2023](https://www.mdpi.com/2076-3417/13/23/12853) | Lunar landing and rendezvous trajectories as QUBO on D-Wave | Continuous trajectories can be squeezed into QUBO |
| [De Grossi et al. 2025](https://link.springer.com/article/10.1007/s42064-024-0216-6) | Earth-to-Mars low-thrust transfer via quantum annealing | Hybrid works, full quantum limited by hardware |
| [Earth-observation satellite planning (2020)](https://arxiv.org/abs/2006.09724) | Image-capture scheduling on an annealer | Space scheduling as QUBO |

## Classical state of the art for asteroid tours

- [ESA GTOC portal](https://sophia.estec.esa.int/gtoc_portal/): competitions with multi-asteroid tours, and a source of realistic asteroid data
- [Beam ant-colony search (2017)](https://arxiv.org/pdf/1704.00702)
- [Hybrid dynamic programming and beam search (JGCD)](https://arc.aiaa.org/doi/10.2514/1.G009214)

## Is quantum advantage real yet?

- [Shaydulin et al., Science Advances 2024](https://arxiv.org/abs/2308.02342): QAOA scaled better than branch-and-bound on the LABS problem, in noiseless simulation up to 40 qubits
- [Bravyi et al., PRL 2020](https://arxiv.org/abs/1910.08980): low-depth QAOA provably underperforms classical on some problem families

## Our gap

1. Quantum path-planning papers use toy grids with static obstacles and no momentum. None we found models a space hazard field (moving asteroids and debris, Δv costs).
2. Quantum trajectory papers optimize fuel with D-Wave annealing but don't include obstacle avoidance. We found no work combining trajectory and hazard avoidance, and none using gate-based QAOA.
3. Most papers ship no runnable code. A reproducible benchmark against A*, Dijkstra and simulated annealing, with a visualizer, is itself a contribution.
4. Comparing QAOA variants (standard vs warm start or classical edge pruning) on this problem is a small, real research question.
