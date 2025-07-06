<p align="left">
  <a href="https://github.com/aaholmes/" target="_blank" style="text-decoration:none;"><img alt="GitHub" src="https://img.shields.io/badge/github-%23121011.svg?style=for-the-badge&logo=github&logoColor=white" /></a>
  <a href="https://www.linkedin.com/in/adamaholmes/" target="_blank" style="text-decoration:none;"><img alt="LinkedIn" src="https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white" /></a>
  <a href="https://scholar.google.com/citations?user=K0CAVroAAAAJ" target="_blank" style="text-decoration:none;"><img alt="Google Scholar" src="https://img.shields.io/badge/Google_Scholar-4285F4?style=for-the-badge&logo=google-scholar&logoColor=white" /></a>
</p>

I am a computational physicist (Ph.D. Theoretical Physics, Cornell) focused on developing and applying novel, high-performance algorithms to solve the **quantum many-body problem**.

My research centers on designing methods that can achieve near-exact accuracy for complex electronic structure problems, allowing for benchmark calculations on systems previously considered intractable. This work led to the development of **Semistochastic Heat-Bath Configuration Interaction (SHCI)**, which has become recognized as a state-of-the-art tool for high-accuracy quantum chemistry.

The core philosophy behind my work is building **hybrid computational systems**—combining deterministic algorithms with stochastic sampling, and high-performance **Rust 🦀** or **C++** with high-level **Python 🐍**—to navigate vast search spaces efficiently. I am now applying this foundational expertise in algorithm design to emerging challenges at the intersection of high-performance computing and **Artificial Intelligence**.

---
### **Foundational Research & Impact: High-Accuracy Quantum Chemistry**

My Ph.D. research introduced a new family of quantum chemistry methods—**Heat-Bath Configuration Interaction (HCI)** ([Holmes, et al., *JCTC 2016*](https://arxiv.org/pdf/1606.07453)) and its successor, **Semistochastic HCI (SHCI)** ([Sharma, Holmes, et al., *JCTC 2017*](https://arxiv.org/pdf/1610.06660))—that significantly advanced the state-of-the-art in high-accuracy electronic structure calculations. The core innovation was a paradigm shift in algorithm design, replacing inefficient "generate-and-test" approaches with a highly efficient strategy that deterministically identifies the most significant components of the quantum wavefunction.

These new methods enabled calculations on a scale previously considered intractable, allowing us to produce the first near-exact potential energy surfaces for fourteen low-lying electronic states of the **carbon dimer** ([Holmes, et al., *JCTC 2017*](https://pubs.aip.org/aip/jcp/article/147/16/164111/76673)), a key benchmark system for multireference quantum chemistry, and the ground state binding curve of the **chromium dimer** ([Li, Yao, Holmes, et al., *Phys. Rev. Res. 2020*](https://journals.aps.org/prresearch/pdf/10.1103/PhysRevResearch.2.012015)), a grand-challenge problem that had remained outstanding for decades, effectively "closing a chapter in quantum chemistry." Today, SHCI is recognized as a leading benchmark method and has been implemented in or interfaced with major quantum chemistry packages.

---
### **Exploratory Research & Broader Applications**

My expertise in designing algorithms for interacting quantum systems provides a unique perspective for modeling other complex systems. The following projects explore the application of these principles to challenges in Artificial Intelligence and Robotics.

| Project                                                          | Description                                                                                                                                                                                            | Key Concepts                                                     |
| :--------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |:-----------------------------------------------------------------|
| ♟️ **[tactical-mcts-chess](https://github.com/aaholmes/caissawary)** | A chess engine with a novel **Hierarchical Tactical MCTS**, which prioritizes deterministic mate-finding and tactical search before using a neural network for strategic evaluation.                 | `Monte Carlo Tree Search` `Heuristic Search` `Neural Networks`   |
| 🗺️ **[multiagent-pathplanning](https://github.com/aaholmes/multiagent-pathplanning)** | A complete, high-performance navigation stack combining Conflict-Based Search (CBS) for optimal global planning with Optimal Reciprocal Collision Avoidance (ORCA) for local collision avoidance. | `Conflict-Based Search` `Optimal Reciprocal Collision Avoidance` |
| 🛡️ **[multiagent-defense](https://github.com/aaholmes/multiagent-defense)** | A decentralized strategy for slower defenders to intercept a faster, intelligent intruder using Apollonian Circle geometry and game-theoretic control.                                       | `Game Theory` `Computational Geometry` `Adversarial AI`          |
| 🧭 **[multi-robot-exploration](https://github.com/aaholmes/multiagent-explore)** | A communication-aware algorithm for a team of robots to autonomously map an unknown environment using an "Iterative Boundary Trace & Coordinated Sweep" strategy.                            | `SLAM` `Frontier-Based Exploration` `Multi-Robot Coordination`   |

<br>

---
#### **Contact**

I'm always open to discussing new research ideas, projects, or opportunities. The best way to reach me is via [email](mailto:adamaholmes@gmail.com) or on [LinkedIn](https://www.linkedin.com/in/adamaholmes/).
