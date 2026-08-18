# DPVO Ablation Study on Visual Odometry

This repository contains my reproduction and extension of Deep Patch Visual Odometry (DPVO) on the TartanAir dataset using ASU Sol HPC resources.

The project began as a course ablation study on monocular visual odometry and evolved into a full research-engineering effort involving baseline reproduction, HPC training/evaluation workflows, checkpoint-resume infrastructure, and architectural ablations.

## Overview

Monocular visual odometry estimates 6-DoF camera motion from a single video stream. It is challenging because depth is not directly observed, and small tracking errors can accumulate into severe trajectory drift.

DPVO (Deep Patch Visual Odometry) is a patch-based learning-based VO system that combines:
- sparse learned patch matching
- iterative neural refinement
- geometric optimization / bundle adjustment

This project focuses on:
1. reproducing the official DPVO baseline
2. implementing ablation variants
3. analyzing how architecture changes affect training behavior and evaluation-time stability

## What I Implemented

### 1. Official Baseline Reproduction
- Set up the official DPVO codebase on ASU Sol
- Resolved CUDA / extension build issues
- Downloaded and organized TartanAir benchmark data
- Reproduced baseline evaluation on the TartanAir mono test split

### 2. ConvLSTM-style Temporal Refinement
- Replaced the original update refinement block with an LSTM-style recurrent temporal module
- Added recurrent-state handling across optimization updates
- Added resumable checkpoint support for long-running jobs on Slurm

### 3. Softmax Aggregation Temperature Tuning
- Modified the `SoftAgg` module to use a temperature-scaled softmax
- Trained tuned variants to study the effect of smoother vs. sharper attention aggregation

### 4. Evaluation Robustness Improvements
- Patched TartanAir evaluation to continue across per-scene failures instead of aborting the full run
- Captured failure modes such as cyclic pose-chain recursion and degenerate alignment cases

## Main Findings

### Official DPVO Baseline
The official DPVO baseline was reproduced successfully on Sol and evaluated on the TartanAir mono benchmark.

### ConvLSTM Ablation
The ConvLSTM-style variant trained successfully and produced resumable checkpoints, but evaluation revealed instability during trajectory reconstruction. Failure modes included cyclic pose-chain recursion and invalid trajectory structures at test time.

### Softmax Aggregation Tuning
The temperature-tuned SoftAgg variant also trained successfully and showed improving training metrics, but the tested checkpoint exhibited evaluation-time instability on the benchmark split.

## Key Takeaway

Ablations that appear promising during training can still fail at evaluation time in monocular visual odometry. This project highlights the importance of:
- full pipeline reproduction
- robust training infrastructure
- checkpoint-resume workflows
- evaluation harness reliability
- failure-mode analysis, not just top-line metrics

## Repository Origin and Attribution

This project is based on the official DPVO repository by Princeton Vision Lab:

- Deep Patch Visual Odometry  
  Zachary Teed, Lahav Lipson, Jia Deng  
  NeurIPS 2023

- Deep Patch Visual SLAM  
  Lahav Lipson, Zachary Teed, Jia Deng  
  ECCV 2024

Official repository:
https://github.com/princeton-vl/DPVO

If you use the original DPVO work, please cite the original authors.

## Project Structure

- `dpvo/` - core model code and modules
- `train.py` - training script
- `evaluate_tartan.py` - TartanAir evaluation
- `config/` - configuration files
- `calib/` - calibration files

## Environment

This project was run on ASU Sol HPC with GPU jobs scheduled using Slurm.

Main stack:
- Python
- PyTorch
- CUDA
- Slurm
- TartanAir
- DPVO

## Notes on Data and Artifacts

This repository does **not** include:
- TartanAir dataset files
- training checkpoints
- generated plots
- large logs
- trajectory dumps

Those artifacts were excluded to keep the repository lightweight and portable.

## Example Workflow

### Baseline Evaluation
```bash
python evaluate_tartan.py --split=test --trials=1 --plot --save_trajectory

## Contributors

This project was developed collaboratively by Milan Tiwari and Kareena Lakhani
as part of coursework and research on monocular visual odometry. The work covered
baseline reproduction on ASU Sol HPC, architectural ablations (ConvLSTM-style
temporal refinement and temperature-scaled softmax aggregation), and
evaluation-harness robustness improvements across the TartanAir and EuRoC benchmarks.
