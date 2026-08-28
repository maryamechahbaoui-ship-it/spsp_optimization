# Multi-Objective Optimization for Software Project Scheduling Problem (SPSP)
## Research Internship Project — Model 2 Implementation using `pymoo`

### 📌 Overview
This repository contains the complete implementation and empirical benchmark suite for the **Multi-Objective Software Project Scheduling Problem (SPSP)** using Python and the `pymoo` framework.

The project models software scheduling decisions under real-world operational constraints (skill profiles, task dependencies, developer experience levels, and availability windows) and evaluates candidate schedules across three competing objectives:

1. **Minimize Makespan ($f_1$):** Total duration required to complete all project tasks.
2. **Minimize Workload Imbalance ($f_2$):** Standard deviation of assigned workloads across developers.
3. **Minimize Coordination Breakdown Risk ($f_3$):** FMEA-based metric quantifying communication hazards between interdependent tasks across team boundaries.

---

### 🧬 Key Mathematical & Algorithmic Features

* **$2N$ Decision Variable Vector Encoding:**
  $$x = [\underbrace{x_0, x_1, \dots, x_{N-1}}_{\text{Developer Assignments}}, \quad \underbrace{x_N, x_{N+1}, \dots, x_{2N-1}}_{\text{Task Start Delays}}]$$
  * First $N$ variables floor to Developer IDs ($d_i = \lfloor x_i \rfloor \in \{0, \dots, M-1\}$).
  * Second $N$ variables control continuous start time delays ($\Delta t_i \ge 0$).
* **Experience-Adjusted Effort Model:**
  $$dev_{\text{time}}(t_i, d_k) = sp(t_i) \times T_{\text{ref}} \times ad\_just(exp(d_k))$$
  * Advanced ($A$): $\alpha = 0.7$ (30% faster execution).
  * Intermediate ($I$): $1.0$ baseline.
  * Beginner ($B$): $\beta = 1.4$ (40% slower execution).
* **Explicit Skill Compatibility Constraints ($n_{\text{constr}} = N$):**
  $$g_{\text{skill}, i} = \begin{cases} 0.0 & \text{if } req\_skill(t_i) \in skills(d_{x_i}) \\ 1.0 & \text{otherwise} \end{cases} \quad \le 0$$
* **Topological Schedule Decoder:**
  $$\text{start}_i = \max(\text{dep\_ready}_i, \text{dev\_ready}_{d_i}) + x_{N+i}$$
  Guarantees zero precedence (Eq. 19) or developer availability (Eq. 18) violations by construction.
* **Algorithms Evaluated:**
  * **NSGA-II:** Non-dominated Sorting Genetic Algorithm II.
  * **MOPSO-CD:** Multi-Objective Particle Swarm Optimization with Crowding Distance.
* **Graph Visualizations:** Dependency graphs plotted with `networkx` in circular layout:
  * **Black solid single-way arrows:** Acyclic Dependencies ($E_{\text{acyc}}$).
  * **Red straight two-way arrows:** Cyclic Dependencies ($E_{\text{cyc}}$).

---

### 📂 Repository Structure
```text
research internship _ SPSP/
├── README.md                              # Complete research documentation & user guide
└── SPSP_Multi_Objective_Optimization.ipynb # Master notebook containing all solvers, plots & benchmarks
```

---

### 🚀 Getting Started

#### Prerequisites
Ensure Python 3.10+ is installed along with the required libraries:
```bash
pip install numpy pandas matplotlib networkx pymoo pypdf
```

#### Running the Notebook
Open the master Jupyter notebook in your preferred environment:
```bash
jupyter notebook SPSP_Multi_Objective_Optimization.ipynb
```

---

### 📊 Benchmark Summary Findings
1. **Convergence & Speed:** **MOPSO-CD** executes $\approx 22\%$ faster than NSGA-II by avoiding $O(MN^2)$ non-dominated sorting calls per generation.
2. **Pareto Diversity:** **NSGA-II** achieves superior spread across the 3D objective trade-off surface.
3. **Primary Trade-off:** Rushing project duration ($f_1 \approx 50$) stacks work onto senior developers ($f_2 \approx 6.0$), while balancing workloads ($f_2 \approx 1.3$) extends total project duration ($f_1 \approx 460$).
4. **Coordination Risk:** Both solvers successfully navigate inter-team overlaps to achieve zero high-risk breakdowns ($f_3 = 0.0$).
