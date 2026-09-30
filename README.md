<p align="left">
  <a href="https://github.com/aaholmes/" target="_blank" style="text-decoration:none;"><img alt="GitHub" src="https://img.shields.io/badge/github-%23121011.svg?style=for-the-badge&logo=github&logoColor=white" /></a>
  <a href="https://www.linkedin.com/in/adamaholmes/" target="_blank" style="text-decoration:none;"><img alt="LinkedIn" src="https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white" /></a>
  <a href="https://scholar.google.com/citations?user=K0CAVroAAAAJ" target="_blank" style="text-decoration:none;"><img alt="Google Scholar" src="https://img.shields.io/badge/Google_Scholar-4285F4?style=for-the-badge&logo=google-scholar&logoColor=white" /></a>
</p>

<img src="profile_pic.png" width="220" align="right" style="margin-left: 16px; border-radius: 50%;" />

Hi! I'm a computational physicist and AI researcher (Ph.D. Theoretical Physics, Cornell). I work on hard search problems — where the space of possibilities is far too large to enumerate, and the whole game is deciding what to look at next.

A common thread in my work has always been an approach to such problems: **solve as much of it as you can with efficient exact methods, and fall back on learned or statistical ones for the remainder.** The exact layer reduces what has to be learned, and improves the training signal for what's left.

I first took this approach in quantum chemistry. A molecule's electron configurations form a graph that grows exponentially with its size, far too large to store for anything but toy problems. The method I developed during my Ph.D. (SHCI, 2,300+ citations) uses a cheap heuristic to find the small fraction of that graph that matters, searches that part exactly, and estimates the rest by sampling. It's now a standard method in the field, and the calculations I ran with it are reference benchmarks that newer neural network and quantum computing methods are compared against.

Since then I've worked on large language model efficiency, game playing, theorem proving, and chip design. Along the way I've built production AI systems since 2018 (Transformer-based semantic search, pre-BERT), run my algorithms on some of the largest supercomputers in the world at Lawrence Livermore, and built quantitative models for systematic trading at Citadel.

<br clear="all" />

---

## Large Language Models

Inference is bottlenecked by memory movement: for every token it generates, the model re-reads its key–value (KV) cache, the stored attention inputs for all earlier tokens. Every way of shrinking that cache is lossy, so the question is how much quality you buy back.

### [Sampling-Based Attention](https://github.com/aaholmes/stochastic-attention)

Attention is a weighted average, so it can be estimated by importance sampling instead of reading the whole cache. With the unbiased estimators I implemented, systematic sampling matches full-model quality in a real model while reading **~3.5% of cached values**, and the fraction shrinks as context grows.

The semistochastic version from my Ph.D. work (largest weights exact, the rest sampled) cuts variance per sample 8–32× but reads no fewer bytes, because the GPU cache already serves repeat samples of the heavily weighted tokens.

*PyTorch · Monte Carlo · Importance Sampling*

### [Inference Engine + Post-hoc MLA](https://github.com/aaholmes/llms)

I wrote a from-scratch single-GPU inference engine for Qwen3, Alibaba's open-weight model family, and studied converting a trained model's attention to a compressed form, multi-head latent attention (MLA), *after* training.

To recover the quality lost to compression, I train a small adapter that pulls the model's output distribution back toward the original's. Targeting the **total-variation distance** between the exact and approximate token distributions beats the standard Kullback–Leibler (KL) objective on every fidelity measure. The adapter merges into the weights, so it costs nothing at inference.

<img loading="lazy" src="https://raw.githubusercontent.com/aaholmes/llms/main/experiments/stage_b/frontier_plot.png" width="620" style="max-width:100%;" />

*PyTorch · Triton*

### [NanoGPT Single-GPU Harness](https://github.com/aaholmes/nanogpt-1gpu)

A single-GPU (16 GB) adaptation of the [modded-nanogpt speedrun](https://github.com/KellerJordan/modded-nanogpt), a community race to train a small GPT model fastest, for screening architecture changes cheaply. It is a research **harness, not a benchmark**: paired same-seed comparisons, per-variant learning-rate matching, and a check that every weight actually trains are all enforced by continuous integration (CI).

*PyTorch · Muon · NorMuon*

---

## Neurosymbolic AI

### [Neurosymbolic Chess Engine](https://github.com/aaholmes/neurosymbolic-mcts)

Self-play engines like AlphaZero learn everything from scratch, including positions a classical solver settles in microseconds. This engine searches classically first, and rewards any position an exact method can settle, such as a forced mate in N moves, rather than only checkmate. The training signal is denser, and unlike a learned reward model it cannot be gamed. It reaches **~600 Elo above an identically-trained purely neural run, in 18 generations rather than 28.**

<img loading="lazy" src="https://raw.githubusercontent.com/aaholmes/neurosymbolic-mcts/main/tournament_results_800eval_elo_plot.png" width="620" style="max-width:100%;" />

*Elo across self-play generations for both runs, evaluated at 800 rollouts per move.*

*Rust · MCTS · PyTorch*

### [Geometry Theorem Prover](https://github.com/aaholmes/geoprover)

A deterministic engine applies 49 deduction rules until nothing new follows, and a 4M-parameter transformer proposes the step deduction can't find: an auxiliary construction, such as a new point or line. Deduction alone solves 179 of the 231 problems in JGEX, a benchmark used to evaluate DeepMind's AlphaGeometry. The network's proposals raise that to **189/231**, including Morley's theorem and the nine-point circle.

*Rust · PyO3 · PyTorch*

---

## Search & Optimization

### [GPU Macro Placement](https://github.com/aaholmes/macro-placer)

Where a chip's large memory blocks sit largely sets the speed, power, and routability of everything placed after them. The placer runs a smooth global optimization of a differentiable proxy, legalizes the result, then refines it by simulated annealing using the full, non-differentiable score. Both stages use a from-scratch reimplementation of the scorer that matches the official metric exactly and runs 50–3600× faster.

Congestion was the binding constraint, and no differentiable congestion model helped, even one correlating 0.995 with the true metric. What worked was aiming the annealing *proposals* at congestion instead, e.g. moving one block a single grid cell so a whole wire route leaves a congested line. **The heuristics only decide where to look; acceptance always uses the real score.**

In an open challenge (17 benchmarks, one hour of compute each) it scored **34% below the reference placements**, at least 4th place, with zero overlaps and on slower hardware than the rules allowed. [Full write-up](/projects/macro-placement/).

<img loading="lazy" src="macro_placement.gif" width="1001" style="max-width:100%;" />

*One complete run on benchmark ibm18: the layout as it spreads, legalizes and improves (left), and the score per frame, with the reference placement marked (right).*

*PyTorch · GPU · Simulated Annealing*

### [MMR-Elites](https://github.com/aaholmes/mmr-elites)

Often you want a diverse set of good solutions rather than the single best, but selecting on quality alone gives redundancy, because the best candidates cluster together. MMR-Elites treats keeping such a set as **submodular maximization**, where each added item is worth less the more you already have, which makes greedy selection near-optimal. Borrowing Maximum Marginal Relevance (MMR) from information retrieval, it uses fixed O(K) memory and O(K log K) selection, and gives 12× better uniformity in 20-dimensional behavior spaces than MAP-Elites, the standard method, which keeps the best solution in each cell of a grid. Choosing a varied, high-quality subset of LLM samples is an analogous problem.

*Rust · PyO3 · Python*

### [Multi-Agent Path Planning](https://github.com/aaholmes/multiagent-pathplanning)

Optimal multi-robot navigation in two layers. A global planner (Conflict-Based Search) computes provably optimal, collision-free routes for every robot before anything moves; a local controller (Optimal Reciprocal Collision Avoidance, ORCA) adjusts each robot's velocity moment to moment for whatever the plan couldn't anticipate.

<img loading="lazy" src="orca_circle.gif" width="830" style="max-width:100%;" />

*The test case from the ORCA paper, with local avoidance only: twelve agents on a circle each head for the opposite point. A red ring marks an agent being deflected.*

*Rust · PyO3 · CBS · ORCA*

---

## Quantum Chemistry Research

I like to think of quantum many-body physics as a graph search problem, but an unusually challenging one because it is a graph too large to even store! The nodes are electron configurations, and a molecule's state is a weighted combination of them. Earlier methods generated enormous numbers of candidate configurations and tested each one. During my Ph.D. I developed a physics-informed heuristic that jumps straight to the ones that matter, called Heat-Bath Configuration Interaction ([Holmes et al., *JCTC* 2016](https://arxiv.org/pdf/1606.07453)). "Configuration interaction" is the field's term for representing a state this way; "heat-bath" names the sampling algorithm I had invented earlier, which the heuristic comes from.

With my colleagues I then removed the memory bottleneck in perturbation theory, the step that accounts for configurations left out, by pairing a deterministic approximation built on that heuristic with stochastic sampling that corrects it ([Sharma, Holmes et al., *JCTC* 2017](https://arxiv.org/pdf/1610.06660)). Together these became Semistochastic HCI (SHCI), now a benchmark algorithm in electronic structure theory.

<img loading="lazy" src="hci_screening.png" width="600" style="max-width:100%;" />

*Matrix elements are precomputed and sorted by magnitude, so for each candidate the algorithm walks the list only until it drops below a threshold set by the current coefficient. Blue is generated; green is never touched. Figure from [Smith, Mussard, Holmes & Sharma, *JCTC* 2017](https://doi.org/10.1021/acs.jctc.7b00900) (open access).*

For the carbon dimer we mapped fourteen low-lying electronic states across their full range of bond lengths, in a space of ~**10²¹** configurations, landing 30–50× closer to the exact answer for that basis than chemical accuracy (1 kcal/mol, the error below which a calculation predicts chemistry reliably) requires ([Holmes et al., *JCP* 2017](https://pubs.aip.org/aip/jcp/article/147/16/164111/76673)). It has become a reference calculation for quantum computing and neural-network methods.

The chromium dimer is harder: many configurations contribute meaningfully simultaneously, so the set that matters is far larger and harder to find, and most methods fail badly. We computed a near-exact binding curve for it near the basis-set limit, in a space of ~**10⁴²** configurations ([Li, Yao, Holmes et al., *Phys. Rev. Res.* 2020](https://journals.aps.org/prresearch/pdf/10.1103/PhysRevResearch.2.012015)). SHCI is implemented in major quantum chemistry packages.

<br>

---

## Tools

Small tools I built for my own workflow; both are open source.

### [butwhy.nvim](https://github.com/aaholmes/butwhy.nvim)

One of my favorite uses of large language models (LLMs) is helping me understand things. I built a plugin for the Neovim text editor that explains any text I highlight, whether prose, code, or a LaTeX equation, in a pop-up beneath it, pitched to a short description of my background. If that's still unclear, asking "but why?" re-explains it one level simpler, as many times as needed. It works with hosted or local models.

<img loading="lazy" src="butwhy_demo.gif" alt="Selecting a line of NumPy code, explaining it, then asking for simpler explanations twice" width="830" style="max-width:100%;" />

### [picat](https://github.com/aaholmes/picat)

I often do remote development work, sometimes over slow coffee-shop Wi-Fi, and like to view images on the remote machine without copying them locally first. So I built picat (progressive icat, after the image viewer built into the kitty terminal), which shows a blurry preview immediately and then sharpens it, sending the most detailed parts first. Over a 10 Mbit/s link, a 3024×4032 photo, scaled to fit a 1000×1400-pixel window, is sharp in 0.8 s, where kitty's own viewer shows nothing until 4.1 s. The command prompt becomes available again as soon as the first preview appears, and the rest of the image loads in the background without interfering with typing responsiveness.

<img loading="lazy" src="picat_vs_icat.gif" alt="kitten icat and picat side by side, showing the same photo over a 10 Mbit/s link" width="830" style="max-width:100%;" />

<br>

---

#### Contact

I'm always happy to chat about research, projects, or opportunities. Reach me via [email](mailto:adamaholmes@gmail.com) or on [LinkedIn](https://www.linkedin.com/in/adamaholmes/).
