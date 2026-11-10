# Two-Soma Neuron Lab

**An interactive, browser-based experiment for dual-soma synthetic neurons and axonal feedback grafts.** This repository contains the **Sonapse-LAB DSAG Experiment 01 · V2.0 Live** prototype, including the original seeded benchmark and the new live neural chamber.

> **Research status:** exploratory computational simulation, **not** a biological neuron reconstruction, demonstration of sentience, or evidence that dual-soma neurons outperform established neural network architectures.

## Quick start

1. Download or clone this repository.
2. Open **`index.html`** in a modern desktop browser (Chrome, Edge, or Firefox).
3. Choose a cognitive task, delay, noise level, state budget, training budget, and number of seeds.
4. Select **Run benchmark** to train readouts and compare four architectures.
5. Open **Live neural chamber**, select **Start live** or **One step**, and interact with the model.

**No installation, package manager, backend, account, API key, or network connection is required.** The complete HTML, CSS, and JavaScript application is contained in `index.html`. It can also be served with GitHub Pages by enabling **Settings → Pages** and deploying from the root of the `main` branch.

## Research question

Does a fixed-weight synthetic recurrent reservoir with distinct primary and memory compartments, optionally receiving an axonal feedback signal, improve delayed recall and contextual signal integration over a simpler point-neuron reservoir under a fixed hidden-state budget?

The *dual-soma axonal-graft (DSAG)* design is a **software abstraction** motivated by multi-compartment computation. Its illustrated morphology is conceptual and should not be interpreted as a biologically observed cell.

## Architecture ablations

| Variant | Description |
| --- | --- |
| **Point neuron** | One recurrent state compartment with uniform decay (`ρ = 0.82`). |
| **Dual soma** | Fast primary and slow memory compartments (`ρₚ = 0.62`, `ρₘ = 0.96`), with no axonal feedback. |
| **Ungated graft** | Dual compartments with an always-active axonal return (`g = 1`). |
| **Full DSAG** | Dual compartments and a nonlinear, context-dependent feedback gate. |

The models share the same **total state-vector dimensionality** and trained linear-readout size. This is **not** a matched-compute, matched-connectivity, or matched-end-to-end-trainable comparison.

### Simplified model

For dual-compartment variants, the conceptual update is:

```text
p(t+1) = tanh(ρp p(t) + Wp h(t) + Up x(t) + bp)
m(t+1) = tanh(ρm m(t) + Wm h(t) + Um x(t) + bm + γ g(t) ⊙ r(t))
r(t)   = tanh(A(p(t), m(t), x(t)))
g(t)   = sigmoid(G(p(t), m(t), x(t)))
h(t)   = concat(p(t), m(t))
ŷ      = sign(wᵀ h + b)
```

Here, `p` is the primary state, `m` the memory state, `r` the return signal, `g` its gate, and `γ` the feedback gain. **Only the final readout is trained** in the benchmark (ridge regression with `λ = 0.35`); the reservoir is randomly initialised and then frozen. This tests the usefulness of fixed representations, not end-to-end neural plasticity.

## Features

### Controlled benchmarks

- Three tasks: **contextual delayed recall**, **delayed XOR/parity**, and **context-gated parity**.
- Adjustable cue separation, input noise, feedback gain, reservoir size, training examples, and independent seeds.
- Seeded synthetic episodes, matched architecture evaluation, held-out accuracy, seed-to-seed variation, and out-of-distribution stress evaluation.
- Architecture comparison tables, learning curves, signal inspector, trial refresh, and SHA-256 configuration receipt when supported by the browser.
- Presets for long memory, sensory noise, and harder parity.

### Live neural chamber

- **Start / Pause**, **One step**, **Restart stream**, and **+100 episodes** controls.
- Live speeds up to **80 simulation steps/second**.
- Select which architecture to visualise and adjust live-only feedback gain, sensor noise, and RNG seed.
- Inspect animated primary and secondary compartments, axonal return/gate, state heatmap, and streaming traces.
- Inject **positive/negative sensory pulses**, **context pulses**, **distractors**, and a **25-step noise burst**.
- Side-by-side rolling accuracy for four architectures on a common stream of episodes.
- Optional online supervised readout adaptation. Without adaptation, live pretrained readouts stay frozen.
- Perturbed trials remain visible in logs and telemetry but are **excluded** from clean rolling-accuracy figures.

The live chamber is a **simulation driven by JavaScript**, not a connection to a physical neuron, brain recording device, or an external AI model. Live speed controls simulated steps per second, not biological time.

### Sensitivity and exports

- **Delay × noise stress map:** 4 cue-separation values × 4 noise settings = **16 paired conditions** (3 seeds per cell).
- Benchmark **audit JSON** and live **JSON**, **CSV**, and **PNG** exports.
- Live telemetry exports retain up to **6,000 recent samples**, including configuration and intervention indicators.
- All computations and files stay on your machine; this prototype sends no results to a remote server.

## Suggested reproducible experiment

1. Set **Contextual delayed recall**, `24` states, `240` training examples/seed, `10` seeds.
2. Run once with **5-step separation**, `σ = 0.12`, and `γ = 0.8`. Export audit JSON.
3. Increase separation to **12** and noise to **0.30**; rerun, exporting a distinct audit.
4. Use the **16-condition stress map** to see where the full DSAG advantage changes sign.
5. Compare **Full DSAG** not only with **Point neuron**, but also with the **Dual soma** and **Ungated graft** ablations.
6. Repeat with new seeds and report mean, dispersion, failures, and compute costs rather than selecting only the best run.

**Interpretation:** A positive result is a candidate hypothesis, not proof. The 16-cell map is exploratory; no multiple-comparison correction or confidence guarantee is provided. Real validation requires independent repeats and complexity-matched baselines such as GRUs or LSTMs.

## Controls and reproducibility notes

- Benchmark sidebar settings control **benchmark runs**. The live controls change **the live stream**; some benchmark settings take effect at the next live episode or after restarting the stream.
- When changing the live random seed, use **Restart stream** to apply it.
- Increasing seed count and training budget raises CPU demand; the simulator runs on the browser's main client machine.
- Reproducibility is conditional on identical code, configuration, and seed. Interactive interventions and optional online adaptation change future live states.
- Promoting the graft into a larger Sonapse-LAB codebase would require regression tests, independent baselines, forgetting/continual-learning checks, and verification against existing features.

## Repository layout

```text
Two-Soma-Neuron-Lab/
├── index.html       # Complete local interactive simulator (V2.0)
└── README.md        # Project overview, usage and experiment protocol
```

## Known limitations

- Synthetic benchmarks are small and may fail to generalise to realistic sequential workloads.
- Fixed random reservoir dynamics and ridge-trained readouts cannot establish that the entire architecture is optimally trainable.
- Equal hidden-state counts do **not** imply equal parameter count, energy, inference cost, or tuning budget.
- The neuron image is illustrative; this is a **non-spiking** computational model, not a faithful electrophysiological simulation.
- No peer-reviewed result, biological implementation, or validated performance advantage is claimed.

## Development roadmap

- [ ] Parameter-, FLOP-, and latency-matched baseline comparisons.
- [ ] GRU/LSTM baselines using the same train/validation/test protocol.
- [ ] Additional continual-learning, catastrophic-forgetting, and transfer tasks.
- [ ] Automated regression tests and headless benchmark runners.
- [ ] Separate numerical engine from browser interface without changing results.
- [ ] Signed, versioned experiment manifests and audit/report exports.
- [ ] Document findings with uncertainty intervals and replicate across larger seed sets.

## Origin

This prototype is an independent experimental workspace intended to explore dual-soma and axonal-dendrite-graft ideas associated with **Sonapse-LAB**. It does not modify, supersede, or deploy into other Sonapse, Supermix/Expanse, or Poseidon repositories.
