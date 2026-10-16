# Lab 7 — Spiking Neural Network on N-MNIST (Brian2)

A three-layer **spiking neural network (SNN)** built in [Brian2](https://briansimulator.org/),
trained with **online STDP** on the neuromorphic **N-MNIST** dataset, and compared
against an equivalent PyTorch **ANN** on accuracy and on an **energy proxy** (spike
count / synaptic operations).

> **This repo is the runnable source notebook.** The committed
> [`Lab7_SNN_NMNIST.ipynb`](Lab7_SNN_NMNIST.ipynb) does not ship saved outputs — run
> it (see below) to generate the results, plots and confusion matrices. No metrics are
> quoted here that haven't been produced by an actual run.

## What it does

- **Rate-coded input layer** — each N-MNIST pixel drives a Poisson spike train whose
  rate is proportional to its event intensity (1156 inputs).
- **Hidden recurrent layer** — LIF neurons with **adaptive thresholds** (homeostasis),
  **recurrent lateral inhibition** (competition / winner-take-all), and recurrent
  excitation trained by **unsupervised STDP** — the hidden layer learns digit
  prototypes *without labels*.
- **Output layer** — winner-take-all, trained with a **teacher-guided STDP** signal on
  the hidden→output synapses (labels enter only through this local Hebbian signal).
- **Training** — 20 epochs of online STDP; accuracy measured on a balanced **500-image
  test subset**.
- **ANN baseline** — a matched **1156 → 100 → 10** ReLU network trained in PyTorch on
  the same images, as a reference point for accuracy and cost.
- **Energy proxy** — total spike count and **synaptic operations (SOPs)** for the SNN
  vs. **MACs** for the ANN, with a 45 nm per-operation energy estimate
  (AC ≈ 0.9 pJ, MAC ≈ 4.6 pJ), plus **accuracy-vs-spike-count** trade-off sweeps and
  confusion matrices.

## Notebook outline

Setup → N-MNIST loading & raw events → rate coding → the three-layer Brian2 network →
20-epoch STDP training → SNN results & learned receptive fields → equivalent PyTorch
ANN → spikes-vs-activations energy proxy → energy–accuracy trade-off & confusion
matrices → summary → discussion → references.

## How to run (Google Colab — recommended)

1. Open [`Lab7_SNN_NMNIST.ipynb`](Lab7_SNN_NMNIST.ipynb) in Colab and choose
   **Runtime → Run all**. The first cells install Brian2 and download N-MNIST
   (~1 GB total).
2. A full run takes roughly **30–60 minutes** on a CPU runtime. Set `QUICK = True` in
   the config cell for a few-minute smoke test.
3. **File → Download → .ipynb** saves the executed notebook with all outputs and plots.

If the automatic dataset download is blocked, grab `Train.zip` and `Test.zip` from the
[N-MNIST dataset page](https://www.garrickorchard.com/datasets/n-mnist) and place them
in `./data/nmnist/`.

### Locally

```bash
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
jupyter notebook Lab7_SNN_NMNIST.ipynb
```

## Dataset

**N-MNIST** (Orchard et al., 2015) — a neuromorphic (event-camera) version of MNIST,
where each digit is recorded as a stream of address-events from a saccading DVS sensor.
See the [dataset page](https://www.garrickorchard.com/datasets/n-mnist).

## License

[MIT](LICENSE) © Anson Dias
