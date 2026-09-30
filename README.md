<p class="links"><a href="mailto:adamaholmes@gmail.com">adamaholmes@gmail.com</a> · <a href="https://scholar.google.com/citations?user=K0CAVroAAAAJ">Google Scholar</a> · <a href="https://github.com/aaholmes/">GitHub</a> · <a href="https://www.linkedin.com/in/adamaholmes/">LinkedIn</a></p>

<img src="profile_pic.png" alt="Adam Holmes" width="160" align="right" style="margin-left: 16px; border-radius: 50%;" />

Hi! I'm a computational physicist and AI researcher (Ph.D. Theoretical Physics, Cornell). I work on hard search problems — where the space of possibilities is far too large to enumerate, and the whole game is deciding what to look at next.

A common thread in my work has always been an approach to such problems: **solve as much of it as you can with efficient exact methods, and fall back on learned or statistical ones for the remainder.** The exact layer reduces what has to be learned, and improves the training signal for what's left.

I started in quantum many-body physics, inventing new deterministic, stochastic, and semistochastic algorithms for high-precision first-principles calculations, using only the fundamental laws of quantum mechanics (2,400+ citations). Since then I've worked on large language model (LLM) efficiency, game playing, theorem proving, and chip design. Along the way I've built production AI systems since 2018 (Transformer-based semantic search, before Google's BERT model made the approach standard), run my algorithms on some of the largest supercomputers in the world at Lawrence Livermore, and built quantitative models for systematic trading at Citadel.

<br clear="all" />

---

## Large Language Models

Inference is bottlenecked by memory movement: for every token it generates, the model re-reads its key–value (KV) cache, the stored attention inputs for all earlier tokens. Every way of shrinking that cache is lossy, so the question is how much quality you buy back.

### [Sampling-Based Attention](https://github.com/aaholmes/stochastic-attention)

Attention is a weighted average, so it can be estimated by importance sampling instead of reading the whole cache. With the unbiased estimators I implemented, systematic sampling matches full-model quality in a real model while reading **~3.5% of cached values**, and the fraction shrinks as context grows.

The semistochastic version from my Ph.D. work (largest weights exact, the rest sampled) cuts variance per sample 8–32× but reads no fewer bytes, because the graphics processor (GPU) cache already serves repeat samples of the heavily weighted tokens.

*PyTorch · Monte Carlo · Importance Sampling*

### [Inference Engine + Post-hoc MLA](https://github.com/aaholmes/llms)

<div class="proj" markdown="1">
<div class="txt" markdown="1">

I studied converting a trained model's attention to a compressed form, multi-head latent attention (MLA), *after* training, using a from-scratch single-GPU inference engine I wrote for Qwen3, Alibaba's open-weight model family.

To recover the quality lost to compression, I train a small adapter that pulls the model's output distribution back toward the original's. Targeting the **total-variation distance** between the exact and approximate token distributions beats the standard Kullback–Leibler (KL) objective on every fidelity measure. The adapter merges into the weights, so it costs nothing at inference.

*PyTorch · Triton*

</div>
<figure markdown="1">

<img loading="lazy" src="https://raw.githubusercontent.com/aaholmes/llms/main/experiments/stage_b/frontier_plot.png" />

</figure>
</div>

### [NanoGPT Single-GPU Harness](https://github.com/aaholmes/nanogpt-1gpu)

I adapted the [modded-nanogpt speedrun](https://github.com/KellerJordan/modded-nanogpt), a community race to train a small GPT model fastest, to a single 16 GB GPU, for screening architecture changes cheaply. It is a research **harness, not a benchmark**: paired same-seed comparisons, per-variant learning-rate matching, and a check that every weight actually trains are all enforced by continuous integration (CI).

*PyTorch · Muon · NorMuon*

---

## Neurosymbolic AI

### [Neurosymbolic Chess Engine](https://github.com/aaholmes/neurosymbolic-mcts)

<div class="proj" markdown="1">
<div class="txt" markdown="1">

Self-play engines like AlphaZero learn everything from scratch, including positions a classical solver settles in microseconds. I developed an engine that searches classically first, and rewards any position an exact method can settle, such as a forced mate in N moves, rather than only checkmate. The training signal is denser, and unlike a learned reward model it cannot be gamed. It reaches **~600 Elo above an identically-trained purely neural run, in 18 generations rather than 28.**

*Rust · Monte Carlo Tree Search · PyTorch*

</div>
<figure markdown="1">

<img loading="lazy" src="https://raw.githubusercontent.com/aaholmes/neurosymbolic-mcts/main/tournament_results_800eval_elo_plot.png" />

*Elo across self-play generations for both runs, evaluated at 800 rollouts per move.*

</figure>
</div>

### [Geometry Theorem Prover](https://github.com/aaholmes/geoprover)

I built a prover in which a deterministic engine applies 49 deduction rules until nothing new follows, and a 4M-parameter transformer proposes the step deduction can't find: an auxiliary construction, such as a new point or line. Deduction alone solves 179 of the 231 problems in JGEX, a benchmark used to evaluate DeepMind's AlphaGeometry. The network's proposals raise that to **189/231**, including Morley's theorem and the nine-point circle.

*Rust · PyO3 · PyTorch*

---

## Search & Optimization

### [GPU Macro Placement](https://github.com/aaholmes/macro-placer)

<div class="proj" markdown="1">
<div class="txt" markdown="1">

Where a chip's large memory blocks (macros) sit largely sets the speed, power, and routability of everything placed after them. I built a GPU placer that runs a smooth global optimization of a differentiable proxy, legalizes the result, then refines it by simulated annealing using the full, non-differentiable score. Both stages use a from-scratch reimplementation of the scorer that matches the official metric exactly and runs 50–3600× faster.

Congestion was the binding constraint, and no differentiable congestion model helped, even one correlating 0.995 with the true metric. What worked was aiming the annealing *proposals* at congestion instead, e.g. moving one block a single grid cell so a whole wire route leaves a congested line. **The heuristics only decide where to look; acceptance always uses the real score.**

I entered it in an open challenge (17 benchmarks, one hour of compute each), where it scored **34% below the reference placements**, at least 4th place, with zero overlaps and on slower hardware than the rules allowed. [Full write-up](/projects/macro-placement/).

*PyTorch · GPU · Simulated Annealing*

</div>
<figure markdown="1">

<img loading="lazy" src="macro_placement.gif" />

*One complete run on benchmark ibm18: the layout as it spreads, legalizes and improves (left), and the score per frame, with the reference placement marked (right).*

</figure>
</div>

### [MMR-Elites](https://github.com/aaholmes/mmr-elites)

Often you want a diverse set of good solutions rather than the single best, but selecting on quality alone gives redundancy, because the best candidates cluster together. I developed MMR-Elites, which treats keeping such a set as **submodular maximization**, where each added item is worth less the more you already have, which makes greedy selection near-optimal. Borrowing Maximum Marginal Relevance (MMR) from information retrieval, it uses fixed O(K) memory and O(K log K) selection, and gives 12× better uniformity in 20-dimensional behavior spaces than MAP-Elites, the standard method, which keeps the best solution in each cell of a grid. Choosing a varied, high-quality subset of LLM samples is an analogous problem.

*Rust · PyO3 · Python*

### [Multi-Agent Path Planning](https://github.com/aaholmes/multiagent-pathplanning)

<div class="proj" markdown="1">
<div class="txt" markdown="1">

I built a two-layer system for optimal multi-robot navigation. A global planner (Conflict-Based Search, CBS) computes provably optimal, collision-free routes for every robot before anything moves; a local controller (Optimal Reciprocal Collision Avoidance, ORCA) adjusts each robot's velocity moment to moment for whatever the plan couldn't anticipate.

*Rust · PyO3 · CBS · ORCA*

</div>
<figure markdown="1">

<img loading="lazy" src="orca_circle.gif" />

*The test case from the ORCA paper, with local avoidance only: twelve agents on a circle each head for the opposite point. A red ring marks an agent being deflected.*

</figure>
</div>

---

## Quantum Many-Body Algorithms

<div class="proj" markdown="1">
<div class="txt" markdown="1">

I like to think of quantum many-body physics as a graph search problem, but an unusually challenging one because it is a graph too large to even store! The nodes are electron configurations, and a molecule's state is a weighted combination of them. Earlier methods generated enormous numbers of candidate configurations and tested each one. During my Ph.D. I developed a physics-informed heuristic that jumps straight to the ones that matter, called Heat-Bath Configuration Interaction, or HCI ([Holmes et al., *JCTC* 2016](https://arxiv.org/pdf/1606.07453)). "Configuration interaction" is the field's term for representing a state this way; "heat-bath" names the sampling algorithm I had invented earlier, which the heuristic comes from.

With my colleagues I then removed the memory bottleneck in perturbation theory, the step that accounts for configurations left out, by pairing a deterministic approximation built on that heuristic with stochastic sampling that corrects it ([Sharma, Holmes et al., *JCTC* 2017](https://arxiv.org/pdf/1610.06660)). Together these became Semistochastic HCI (SHCI), now a benchmark algorithm in electronic structure theory.

For the carbon dimer we mapped fourteen low-lying electronic states across their full range of bond lengths, in a space of ~**10²¹** configurations, landing 30–50× closer to the exact answer for that basis than chemical accuracy (1 kcal/mol, the error below which a calculation predicts chemistry reliably) requires ([Holmes et al., *JCP* 2017](https://pubs.aip.org/aip/jcp/article/147/16/164111/76673)). It has become a reference calculation for quantum computing and neural-network methods.

The chromium dimer is harder: many configurations contribute meaningfully simultaneously, so the set that matters is far larger and harder to find, and most methods fail badly. We computed a near-exact binding curve for it near the basis-set limit, in a space of ~**10⁴²** configurations ([Li, Yao, Holmes et al., *Phys. Rev. Res.* 2020](https://journals.aps.org/prresearch/pdf/10.1103/PhysRevResearch.2.012015)). SHCI is implemented in major quantum chemistry packages.

</div>
<figure markdown="1">

<img loading="lazy" src="hci_screening.png" />

*Matrix elements are precomputed and sorted by magnitude, so for each candidate the algorithm walks the list only until it drops below a threshold set by the current coefficient. Blue is generated; green is never touched. Figure from [Smith, Mussard, Holmes & Sharma, *JCTC* 2017](https://doi.org/10.1021/acs.jctc.7b00900) (open access).*

</figure>
</div>

<br>

---

## Tools

Small tools I built for my own workflow; both are open source.

### [butwhy.nvim](https://github.com/aaholmes/butwhy.nvim)

<div class="proj" markdown="1">
<div class="txt" markdown="1">

One of my favorite uses of LLMs is helping me understand things. I built a plugin for the Neovim text editor that explains any text I highlight, whether prose, code, or a LaTeX equation, in a pop-up beneath it, pitched to a short description of my background. If that's still unclear, asking "but why?" re-explains it one level simpler, as many times as needed. It works with hosted or local models.

</div>
<figure markdown="1">

<img loading="lazy" src="butwhy_demo.gif" alt="Selecting a line of NumPy code, explaining it, then asking for simpler explanations twice" />

</figure>
</div>

### [picat](https://github.com/aaholmes/picat)

<div class="proj" markdown="1">
<div class="txt" markdown="1">

I often do remote development work, sometimes over slow coffee-shop Wi-Fi, and like to view images on the remote machine without copying them locally first. So I built picat (progressive icat, after the image viewer built into the kitty terminal), which shows a blurry preview immediately and then sharpens it, sending the most detailed parts first. Over a 10 Mbit/s link, a 3024×4032 photo, scaled to fit a 1000×1400-pixel window, is sharp in 0.8 s, where kitty's own viewer shows nothing until 4.1 s. The command prompt becomes available again as soon as the first preview appears, and the rest of the image loads in the background without interfering with typing responsiveness.

</div>
<figure markdown="1">

<img loading="lazy" src="picat_vs_icat.gif" alt="kitten icat and picat side by side, showing the same photo over a 10 Mbit/s link" />

</figure>
</div>

<br>

---

#### Contact

I'm always happy to chat about research, projects, or opportunities. Reach me via [email](mailto:adamaholmes@gmail.com) or on [LinkedIn](https://www.linkedin.com/in/adamaholmes/).