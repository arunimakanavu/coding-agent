# OpenVINO Coding Agent — Notebook Suite

3 notebooks for building and benchmarking a coding agent with OpenVINO on Intel AI PCs.

## Notebooks

| # | Notebook | Purpose |
|---|----------|---------|
| 01 | `01_coding_agent.ipynb` | Build the agent: model export, OVMS setup, tool calling loop |
| 02 | `02_benchmark_pantherlake.ipynb` | Benchmark on Panther Lake (Core Ultra Series 3, Xe3 Arc B390) |
| 03 | `03_benchmark_lunarlake.ipynb` | Benchmark on Lunar Lake (Core Ultra Series 2, Xe2 Arc 140V) |

## Quick Start

```bash
# 1. Create virtualenv
python3 -m venv ovino-env && source ovino-env/bin/activate

# 2. Install Jupyter
pip install jupyterlab

# 3. Launch
jupyter lab
```

## Prerequisites (Ubuntu 24.04 Noble)

### Intel GPU Drivers
```bash
wget -qO - https://repositories.intel.com/gpu/intel-graphics.key | \
  sudo gpg --dearmor -o /usr/share/keyrings/intel-graphics.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/intel-graphics.gpg] \
  https://repositories.intel.com/gpu/ubuntu noble unified" | \
  sudo tee /etc/apt/sources.list.d/intel-gpu-noble.list
sudo apt update && sudo apt install -y intel-opencl-icd intel-level-zero-gpu level-zero
```

### NPU Driver
```bash
sudo apt install -y intel-npu-driver
# Or download from: github.com/intel/linux-npu-driver/releases
# Minimum version: 32.0.100.3104 (Lunar Lake), latest for Panther Lake
```

### User permissions
```bash
sudo usermod -aG render,video $USER && newgrp render
```

### HWE Kernel (Panther Lake)
```bash
sudo apt install linux-generic-hwe-24.04  # gets kernel 6.11+
```

## Workflow

1. Run **notebook 01** on either platform — exports model, starts OVMS, tests the agent
2. Run **notebook 02** on Panther Lake hardware — benchmarks CPU/GPU/NPU, saves `pantherlake_results.json`
3. Run **notebook 03** on Lunar Lake hardware — benchmarks CPU/GPU/NPU, loads PL results for comparison chart

## Output Files

- `agent_config.json` — model config shared between notebooks
- `pantherlake_results.json` — raw benchmark data
- `lunarlake_results.json` — raw benchmark data
- `pantherlake_benchmark.png` — throughput/TTFT chart
- `lunarlake_benchmark.png` — throughput/TTFT/memory chart
- `comparison_pantherlake_vs_lunarlake.png` — side-by-side comparison

## Model Notes

| Model | Tool Parser | NPU Support | Notes |
|-------|------------|------------|-------|
| Qwen3-Coder-30B | `qwen3coder` | GPU only (too large) | Best coding quality |
| Qwen3-8B | `hermes3` + `qwen3` reasoning | ✓ (cw-ov variant) | Best all-round |
| gpt-oss-20b | `gptoss` | GPU only | Use `--pipeline_type LM` |
