# PoP Clustering via Capacitated p-Median

Project for the **Combinatorial and Network Optimization Algorithms** course — M.Sc. in Computer Engineering.

## Overview

This project addresses the **Point-of-Presence (PoP) location problem** in a telecommunications network, modelled as a **capacitated p-median problem** (a special case of *capacitated facility location*).

Given a set of candidate PoP sites (service centres) and a set of demand nodes (users), the objective is to select exactly *p* centres to open and assign every user to an open centre, minimising the **total service cost** (latency + economic transport cost) subject to **capacity constraints**.

## Implemented Methods

| # | Method | Type | Description |
|---|--------|------|-------------|
| 1 | **MILP** (PuLP / CBC) | Exact | Mixed-Integer Linear Programming formulation solved with the CBC open-source solver |
| 2 | **Greedy-add** | Constructive heuristic | Sequentially selects the candidate yielding the largest cost reduction |
| 3 | **Local Search (Teitz & Bart)** | Improvement heuristic | 1-swap neighbourhood (*vertex substitution*) with *best improvement* selection |
| 4 | **Tabu Search** | Metaheuristic | Extension of Local Search with a FIFO tabu list and aspiration criterion |
| 5 | **Simulated Annealing** | Metaheuristic | Probabilistic acceptance (Metropolis criterion) with geometric cooling schedule |
| 6 | **k-Means** | Baseline | Continuous-centroid clustering (not constrained to candidate sites), used as a reference |
| 7 | **CPLEX** (docplex) | Exact (deep-dive) | Same MILP formulation solved with the commercial IBM ILOG CPLEX solver |

## Project Structure

```
├── experiments.ipynb        # Main notebook with all experiments
├── requirements.txt         # Python dependencies
├── LICENSE                  # MIT Licence
├── data/
│   ├── user_anchors.json    # Anchor cities for synthetic user generation
│   ├── pop_candidates.json  # Candidate service centres (PoPs)
│   ├── index.json           # Registry of generated datasets
│   └── generated/           # Synthetic user datasets (auto-generated)
└── results/                 # Outputs: CSV and plots (auto-generated)
    ├── results.csv
    └── plots/
```

## Data

- **`data/pop_candidates.json`** — 20 European cities as candidate PoP service centres, each with geographic coordinates (lat, lon).
- **`data/user_anchors.json`** — Anchor cities around which demand nodes are randomly generated.
- **`data/generated/`** — Synthetic user datasets, automatically created by the notebook via random jitter around anchor cities. Files follow the naming pattern `users_n<N>_seed<S>.json` and are regenerated if missing, ensuring full **reproducibility**.

## Installation

```bash
# Create virtual environment (requires Python 3.12)
python3.12 -m venv .venv
source .venv/bin/activate

# Install dependencies
pip install -r requirements.txt
```

## Usage

Open `experiments.ipynb` and run all cells sequentially. The notebook will:

1. Generate (or reload) synthetic user instances;
2. Solve the problem with every method across different *p* values and random seeds;
3. Produce comparative charts (objective, max distance, gap %, runtime);
4. Visualise solutions on interactive maps (Folium);
5. Export results to `results/results.csv`.

## Configuration

All experimental parameters are centralised in the `CONFIG` dictionary inside the notebook:

| Parameter | Default | Description |
|-----------|---------|-------------|
| `n_users` | `[120, 300, 600, 1000]` | Number of demand nodes |
| `p_values` | `[3, 5, 8, 10, 13, 15, 18, 20]` | Values of *p* (centres to open) |
| `seeds` | `[0, 1, 2]` | Seeds for reproducibility |
| `capacity.multiplier` | `1.3` | Slack over average demand per centre |
| `capacity.heterogeneous` | `True` | Heterogeneous capacities across centres |
| `cache.use_cache` | `True` | Load cached results if available |
| `cache.force_recompute` | `False` | Ignore cache and recompute |
| `cache.dir` | `results/cache` | Cache directory |

## Results

The notebook produces:
- **Comparison table** — objective value, gap % vs the MILP optimum, and computation time for each method;
- **Charts** — objective vs *p*, max distance vs *p*, heuristic gap % vs MILP, runtime (log scale);
- **Interactive Folium maps** — geospatial visualisation of the clustering;
- **Heatmap** — user × candidate PoP distance matrix;
- **Scatter plot** — side-by-side comparison of clusters obtained by different methods.

## Licence

GNU GENERAL PUBLIC LICENSE — see [LICENSE](LICENSE).
