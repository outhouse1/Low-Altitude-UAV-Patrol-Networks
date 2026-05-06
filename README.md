# Supplementary Materials for “How Organization Strategies Shape the Resilience of Low-Altitude UAV Patrol Networks for Sustainable Cities”

This repository provides the supplementary materials for the numerical experiments reported in the manuscript. The materials are released to improve transparency and reproducibility, and to allow readers to inspect the parameter settings, solution logs, and numerical results used to evaluate the proposed two-stage resilient UAV patrol network planning framework.

## Computational environment

The experiments were implemented in Python and solved with Gurobi Optimizer 13.0.1. The main computational platform used in the reported experiments was configured as follows:

| Item | Specification |
|---|---|
| CPU | AMD Ryzen AI 9 H 365 with Radeon 880M |
| Physical cores | 10 |
| Logical processors | 20 |
| Memory | 32 GB |
| Maximum solver threads | 8 |
| Programming language | Python |
| Optimization solver | Gurobi Optimizer 13.0.1 |

Runtime may vary across machines because of hardware differences, operating-system conditions, solver-version differences, and parallel-processing behavior.

## Solution modes and disruption setting

Two solution modes are reported:

- **branch-and-price exact mode**: solves the normal-state organizational problem over the admissible route domain where the case scale is computationally tractable. It is used to provide exact reference solutions for validation.
- **generated-pool mode**: constructs a candidate route pool and solves the corresponding full-pool mixed-integer programming model. 

The disruption reallocation layer considers **single activated-base failure scenarios**. For each first-stage solution, every activated base is failed separately, and the remaining activated bases are used to reconstruct patrol routes under the full-coverage requirement. Inactive bases are not reactivated during recovery.

## Parameter settings used in the numerical experiments

The table below reports the manuscript-level notation used to describe the model and algorithm parameters and the corresponding code-level variables used in the implementation. The symbols follow the manuscript formulation. 

| Category | Manuscript symbol | Code variable / implementation key | Value used in code | Description |
|---|---|---|---:|---|
| Data and cases | -- | `batch_case_names` | `S, MS, M, L` | Case sequence used in the reported batch experiments. |
| Data and cases | -- | `base_file`, `demand_file` | `baseTable_{case}.csv`, `demand_{case}.csv` | Input files for candidate bases and inspection task points. |
| UAV operation | $v$ | `speed_mps` | 20.0 m/s | Effective UAV flight speed. |
| UAV operation | $M_b$ | `mission_time_limit_min` | 53.0 min | Maximum allowable task duration of one UAV sortie at base $b$. |
| UAV operation | $U_b$ | `default_uavs_per_base` | 2 | Default physical UAV capacity at a candidate base when no base-specific UAV-count field is provided. |
| UAV operation | $\bar{U}_b$ | `normal_stage_uavs_per_base` | 2 | UAV capacity allowed in the normal-state organizational layer. |
| UAV operation | $s_i$ | `dwell_time_normal_min` | 1.0 min | Dwell service time at an ordinary inspection task point. |
| UAV operation | $s_i,\ i\in\mathcal{I}^K$ | `dwell_time_key_min` | 1.5 min | Dwell service time at a critical task point. |
| Critical-task definition | $\mathcal{I}^K$ | `key_risk_threshold` | risk level $\geq$ 1 | Rule used to identify critical task points from the input demand data. |
| Task importance | $W_i$ | `weight_risk` | 0.5 | Weight of risk level in the composite task-importance score. |
| Task importance | $W_i$ | `weight_affected` | 0.3 | Weight of affected population in the composite task-importance score. |
| Task importance | $W_i$ | `weight_population_density` | 0.2 | Weight of population density in the composite task-importance score. |
| Normal-state objective | $\lambda$ | `lambda_1` | 1.0 | Unit route execution-duration coefficient in the normal-state objective. |
| Normal-state objective | $F_b^{base}$ | `lambda_2` / `Q_b` | 500.0 | Fixed cost of activating base $b$. In the code, `Q_b` is initialized from `lambda_2`. |
| Normal-state objective | $F^{path}$ | `c_path` | 200.0 | Fixed organizational cost of selecting a patrol route. |
| SES backup configuration | $C_{ib}^{bk}$ | `ses_backup_execution_cost()` | $\lambda(2d_{bi}/v+s_i)$ | Secondary responsibility configuration cost for assigning base $b$ to critical task point $i$. |
| SRS setting | $G_b$ | `srs_utilization_ratio` | 0.65 | Maximum usable fraction of the sortie duration under SRS; a route is retained only if $t_r\leq G_bM_b$. |
| Route generation | $L^{\max}$ | `max_stops_per_route` | 7 | Maximum number of task points that can be visited by one closed patrol route. |
| Route generation | $\beta$ | `key_bonus_beta` | 3.0 | Additional weight used when generating key-task-oriented candidate routes. |
| Route generation | -- | `route_generation_mode` | `auto` | Implementation option controlling route-generation mode. |
| Exact mode | $\varepsilon^{rc}$ | `cg_rc_epsilon` | 1e-06 | Reduced-cost tolerance used in column generation. |
| Exact mode | -- | `cg_max_iterations` | 500 | Maximum column-generation iterations at a node. |
| Exact mode | -- | `bp_node_limit` | 5000 | Maximum number of branch-and-price tree nodes. |
| Exact mode | -- | `target_relative_gap` | 0.0001 | Relative-gap threshold for early termination. |
| Exact mode | $\alpha$ | `cg_dual_stabilization_alpha` | 0.65 | Weight of the current dual solution in dual stabilization. |
| Exact mode | $\theta$ | `cg_dual_stabilization_center_weight` | 0.75 | Exponential-smoothing weight used to update the dual center. |
| Gurobi settings | -- | `gurobi_time_limit_sec` | 6000 s | Time limit used in Gurobi calls. |
| Gurobi settings | -- | `gurobi_mip_gap` | 0.001 | MIP relative-gap tolerance used by Gurobi. |
| Gurobi settings | -- | `gurobi_threads` | 8 | Maximum number of Gurobi threads. |
| generated-pool mode | -- | `ml_solver_mode` | `pool_mip_first` | Generated-pool solution mode used for scale extension. |
| generated-pool mode | -- | `ml_pool_quick_mip_enabled` | True | Enables full-pool MIP optimization over the generated route pool. |
| generated-pool mode | -- | `ml_pool_enhancement_enabled` | True | Enables generated-pool enhancement rules. |
| generated-pool mode | -- | `ml_pool_bridge_seed_per_base` | 6 | Number of seed routes per base used in bridge-merge enhancement. |
| generated-pool mode | -- | `ml_pool_component_max_size` | 5 | Maximum size of compact local task components used for component-seed route generation. |
| generated-pool mode | -- | `ml_pool_swap_closure_enabled` | True | Enables local exchange closure around generated candidate routes. |
| Recovery evaluation | $\omega$ | `evaluate_only_active_base_failures` | True | Disruption scenarios are constructed by failing activated bases one at a time. |
| Recovery evaluation | -- | `scenario_allow_reactivation` | False | Indicates whether inactive bases can be reactivated during recovery; disabled in the reported experiments. |
| Recovery evaluation | $U_b^\omega$ | `U_b` after base failure | inherited from $U_b$ | Available UAV capacity at each surviving activated base under scenario $\omega$. |
| Recovery evaluation | $\bar{N}_b^\omega$ | `recovery_unbounded_extra_uav`, `recovery_extra_uav_cap_ratio` | unbounded | Upper bound on additional UAVs. In the reported configuration, additional UAVs are not explicitly capped. |
| Recovery evaluation | $C_b^{\mathrm{add}}$ | `recovery_extra_uav_cost_multiplier` × `Q_b` | 750.0 | Unit cost of adding one UAV at base $b$ during recovery, computed as 1.5 × 500.0. |
| Recovery evaluation | $C^{tr}$ | `recovery_external_transfer_penalty_multiplier` × `c_path` | 300.0 | Unit penalty for responsibility transfer outside the responsibility-inheritance set, computed as 1.5 × 200.0. |
| Recovery evaluation | $\Pi_i^\omega$ | `recovery_fallback_penalty_multiplier` × `c_path` | 4000.0 | Emergency-compensation coverage penalty, computed as 20.0 × 200.0. |
| Recovery evaluation | $\eta$ | `lambda_3` | 0.02 | Route execution-duration coefficient in the disruption reallocation objective. |
| Recovery evaluation | -- | `recovery_preset_substitute_top_k` | 3 | Number of nearest substitute bases recorded for recovery diagnostics. |
| Recovery evaluation | -- | `gurobi_mip_gap` | 0.001 | MIP relative-gap tolerance used when solving recovery models. |

## Drone model used for low-altitude patrol: DJI Matrice 400

The numerical experiments are parameterized for a low-altitude UAV patrol setting using the DJI Matrice 400 as the reference platform. Related specifications can be found at:

https://enterprise.dji.com/matrice-400?site=enterprise&from=solutions

## Notes on reproducibility

The raw solver logs and result files are provided for transparency. The generated-pool mode is designed as a scale-extension mode: its full-pool MIP optimality certificate applies to the generated route pool, while the branch-and-price exact mode is used to provide exact references where computationally tractable. Small runtime variations may occur when the experiments are rerun on different machines or with different solver versions.

Some code-level configuration options support development diagnostics, legacy experimental variants, or inactive branches. The parameter table above reports the values that are directly relevant to the manuscript-level model, the two reported solution modes, and the recovery evaluation logic.
