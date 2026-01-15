<p align="left">
  <a href="https://github.com/aaholmes/" target="_blank" style="text-decoration:none;"><img alt="GitHub" src="https://img.shields.io/badge/github-%23121011.svg?style=for-the-badge&logo=github&logoColor=white" /></a>
  <a href="https://www.linkedin.com/in/adamaholmes/" target="_blank" style="text-decoration:none;"><img alt="LinkedIn" src="https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white" /></a>
  <a href="https://scholar.google.com/citations?user=K0CAVroAAAAJ" target="_blank" style="text-decoration:none;"><img alt="Google Scholar" src="https://img.shields.io/badge/Google_Scholar-4285F4?style=for-the-badge&logo=google-scholar&logoColor=white" /></a>
</p>

I am a **Research Engineer & Computational Physicist** (Ph.D. Theoretical Physics, Cornell) focused on **efficient AI systems** and **neurosymbolic architectures**.

I build high-performance systems that navigate vast search spaces efficiently—whether sampling from a diffusion model, finding the ground state of a molecule, or computing the optimal move in a game. I deployed Transformer-based semantic search in production in 2018 (pre-BERT) and invented SHCI, a quantum chemistry method with 2,100+ citations.

---
### **AI Systems & Research**

Current projects exploring efficient inference, neurosymbolic architectures, and multi-agent systems.

| Project | Description | Tech Stack |
| :--- | :--- | :--- |
| 🌀 **[Diffusion LLM](https://github.com/aaholmes/diffusion-llm)** | Diffusion language model from scratch, exploring efficient inference for discrete sequence generation on edge hardware (Jetson Orin Nano). | `Python` • `PyTorch` • `Diffusion` |
| ♟️ **[Neurosymbolic Chess](https://github.com/aaholmes/hybrid-chess-engine)** | A neurosymbolic chess engine (Rust) that integrates MCTS with a state-dependent alpha-beta portfolio to solve the "cold start" inefficiency of pure RL. | `Rust` • `MCTS` • `PyTorch` |
| ⚛️ **[Arrow (SHCI)](https://github.com/aaholmes/shci)** | **High-Performance Quantum Chemistry Engine.** The reference C++/MPI implementation of Semistochastic Heat-Bath CI. I currently lead maintenance and architectural extensions. | `C++` • `MPI` • `HPC` |
| 🗺️  **[Multi-Agent Pathfinding](https://github.com/aaholmes/multiagent-pathplanning)** | A navigation stack combining Conflict-Based Search (CBS) for global optimality with ORCA for local avoidance. | `CBS` • `ORCA` |
| 🛡️  **[Adversarial Defense](https://github.com/aaholmes/multiagent-defense)** | A decentralized strategy for slower defenders to intercept a faster, intelligent intruder using Apollonian Circle geometry and game-theoretic control. | `Game Theory` • `Computational Geometry` |
| 🧭 **[Multi-Robot Exploration](https://github.com/aaholmes/multiagent-explore)** | A communication-aware algorithm for a team of robots to autonomously map an unknown environment using an "Iterative Boundary Trace & Coordinated Sweep" strategy. | `SLAM` • `Frontier-Based Exploration` |

---
### **Foundational Research & Impact: High-Accuracy Quantum Chemistry**

My Ph.D. research introduced a new family of quantum chemistry methods—**Heat-Bath Configuration Interaction (HCI)** ([Holmes, et al., *JCTC 2016*](https://arxiv.org/pdf/1606.07453)) and its successor, **Semistochastic HCI (SHCI)** ([Sharma, Holmes, et al., *JCTC 2017*](https://arxiv.org/pdf/1610.06660))—that significantly advanced the state-of-the-art in high-accuracy electronic structure calculations. The core innovation was a paradigm shift in algorithm design, replacing inefficient "generate-and-test" approaches with a highly efficient strategy that deterministically identifies the most significant components of the quantum wavefunction.

These new methods enabled calculations on a scale previously considered intractable, allowing us to produce the first near-exact potential energy surfaces for fourteen low-lying electronic states of the **carbon dimer** ([Holmes, et al., *JCP 2017*](https://pubs.aip.org/aip/jcp/article/147/16/164111/76673)), a key benchmark system for multireference quantum chemistry, and the ground state binding curve of the **chromium dimer** ([Li, Yao, Holmes, et al., *Phys. Rev. Res. 2020*](https://journals.aps.org/prresearch/pdf/10.1103/PhysRevResearch.2.012015)), a grand-challenge problem that had remained outstanding for decades, effectively "closing a chapter in quantum chemistry." Today, SHCI is recognized as a leading benchmark method and has been implemented in or interfaced with major quantum chemistry packages.

<br>

---
#### **Contact**

I'm always open to discussing new research ideas, projects, or opportunities. The best way to reach me is via [email](mailto:adamaholmes@gmail.com) or on [LinkedIn](https://www.linkedin.com/in/adamaholmes/).
