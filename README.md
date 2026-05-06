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
| Optimisation solver | Gurobi Optimizer 13.0.1 |

Runtime may vary across machines because of hardware differences, operating-system conditions, solver-version differences, and parallel-processing behaviour.


## Solution modes and disruption setting

Two solution modes are reported:

- **Branch-and-Price exact mode**: solves the normal-state organisational problem in the original route space where the case scale is computationally tractable. It is used to provide exact reference solutions for validation.
- **generated-pool mode**: constructs a candidate route pool and solves the corresponding full-pool MIP. Its optimality certificate applies to the generated route pool rather than to the original route space.

The disruption reassignment layer considers **single activated-base failure scenarios**. For each first-stage solution, every activated base is failed separately, and the remaining activated bases are used to reconstruct patrol routes under the full-coverage requirement.

## Parameter settings used in the numerical experiments

The table below reports the paper-level notation used to describe the parameters and the corresponding code-level variable names used in the implementation. The code-level names are provided to make the computational results traceable to the implementation files.

| Category | Paper-level symbol | Code variable / implementation key | Value | Description |
|---|---|---|---:|---|
| UAV operation | $v$ | `speed_mps` | 20.0 m/s | UAV cruising speed. |
| UAV operation | $T^{\max}$ | `mission_time_limit_min` | 50.0 min | Maximum flight duration of one UAV sortie. |
| UAV operation | $\bar{K}_b$ | `default_uavs_per_base` | 1 | Default number of UAVs available at an activated base. |
| UAV operation | $\bar{K}^{0}_b$ | `normal_stage_uavs_per_base` | 1 | UAV capacity used in the normal-state organisational layer. |
| UAV operation | $\tau^{\mathrm{buf}}$ | `safety_buffer_min` | 1.5 min | Safety time buffer reserved for flight operations. |
| UAV operation | $s_i$ | `dwell_time_normal_min` | 1.0 min | Dwell time at an ordinary patrol task. |
| UAV operation | $s_i$ for $i\in\mathcal{I}^{K}$ | `dwell_time_key_min` | 1.5 min | Dwell time at a key patrol task. |
| Demand weight | $\alpha_{r}$ | `weight_risk` | 0.50 | Weight of risk level in task-importance calculation. |
| Demand weight | $\alpha_{a}$ | `weight_affected` | 0.30 | Weight of affected population in task-importance calculation. |
| Demand weight | $\alpha_{p}$ | `weight_population_density` | 0.20 | Weight of population density in task-importance calculation. |
| Objective function | $\lambda_{1}$ | `lambda_1` | 1.0 | Coefficient of route execution cost in the normal-state objective. |
| Objective function | $\lambda_{2}$ | `lambda_2` | 1000.0 | Fixed cost coefficient for activating a base. |
| Objective function | $c^{\mathrm{path}}$ | `c_path` | 200.0 | Fixed cost associated with selecting a patrol route. |
| Objective function | $\lambda_{3}$ | `lambda_3` | 0.02 | Additional route-execution cost coefficient used in the disruption reassignment layer. |
| Objective function | $c^{\mathrm{SES}}$ | `ses_redundancy_unit_cost` | 50.0 | Unit cost for configuring substitute support under SES. |
| SRS setting | $\eta^{\mathrm{SRS}}$ | `srs_utilization_ratio` | 0.7 | Maximum usable mission-time ratio under SRS. |
| Route generation | $H^{\max}$ | `max_stops_per_route` | 7 | Maximum number of task points served by one generated route. |
| Route generation | $d^{\mathrm{N}}$ | `neighbor_distance_km` | 6.0 km | Distance threshold used for neighbourhood construction. |
| Route generation | $k^{\mathrm{N}}$ | `k_nearest_neighbors` | 10 | Maximum number of nearest-neighbour candidates considered for each task. |
| Route generation | -- | `route_generation_mode` | `auto` | Implementation option controlling route-generation mode. |
| Exact mode | -- | `normal_solver_mode` | `branch_and_price` | Exact solution mode used where exact reference solutions are tractable. |
| Exact mode | $\varepsilon^{\mathrm{BP}}$ | `target_relative_gap` | 0.0001 | Target relative gap for Branch-and-Price termination. |
| Exact mode | $T^{\mathrm{TL}}$ | `gurobi_time_limit_sec` | 6000 s | Time limit used in Gurobi calls. |
| Exact mode | $\varepsilon^{\mathrm{MIP}}$ | `gurobi_mip_gap` | 0.001 | MIP gap tolerance used by Gurobi. |
| Exact mode | -- | `gurobi_threads` | 8 | Maximum number of Gurobi threads. |
| generated-pool mode | -- | `ml_solver_mode` | `pool_mip_first` | Generated-pool solution mode used for scale extension. |
| generated-pool mode | -- | `full_pool_mip` | enabled | Solves the MIP induced by the generated route pool. |
| generated-pool mode | -- | `exact_overlay` | enabled | Adds local exact routes to enhance the generated pool where applicable. |
| generated-pool mode | -- | `incumbent_closure_repair` | enabled | Repairs and enriches routes around incumbent solutions where applicable. |
| Recovery evaluation | $\omega_b$ | `disruption_scenario` | single activated-base failure | Failure scenario in which one activated base is removed at a time. |
| Recovery evaluation | $\mathcal{B}^{0}_{\mathrm{act}}$ | `evaluate_only_active_base_failures` | `True` | Only bases activated in the normal-state solution are considered as failed bases. |
| Recovery evaluation | $\rho^{\mathrm{react}}$ | `scenario_allow_reactivation` | `False` | Indicates whether inactive bases can be reactivated during recovery. |
| Recovery evaluation | $\gamma^{\mathrm{react}}$ | `scenario_reactivation_capacity_ratio` | 0.0 | Capacity ratio assigned to inactive bases when reactivation is disabled. |
| Recovery evaluation | $\kappa^{\mathrm{sub}}$ | `recovery_preset_substitute_top_k` | 3 | Number of preset substitute bases considered in recovery. |
| Recovery evaluation | $K^{+}$ | `recovery_unbounded_extra_uav` | allowed | Indicates whether additional UAVs may be used as recovery reinforcement. |
| Recovery evaluation | $\gamma^{+}$ | `recovery_extra_uav_cap_ratio` | 4.0 | Relative upper bound for extra UAV reinforcement. |
| Recovery evaluation | $\mu^{+}$ | `recovery_extra_uav_cost_multiplier` | 2.0 | Cost multiplier for using extra UAVs in recovery. |
| Recovery evaluation | $\mu^{\mathrm{tr}}$ | `recovery_external_transfer_penalty_multiplier` | 4.0 | Penalty multiplier for reassignment outside the predefined substitute structure. |
| Recovery evaluation | $\mu^{\mathrm{fb}}$ | `recovery_fallback_penalty_multiplier` | 20.0 | Penalty multiplier for emergency fallback service. |


## Drone Model Selected for Low-Altitude : DJI Matrice 400

Related parameters can be found at:
https://enterprise.dji.com/matrice-400?site=enterprise&from=solutions


## Notes on reproducibility

The raw solver logs and result files are provided for transparency. The generated-pool mode is designed as a scale-extension mode: its full-pool MIP optimality certificate applies to the generated route pool, while the Branch-and-Price exact mode is used to provide exact references where computationally tractable. Small runtime variations may occur when the experiments are rerun on different machines or with different solver versions.
