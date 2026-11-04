# Model Testing — Experiment Log

This file is the authoritative record of experiments, measured results, failures, and decisions. Add an entry for every meaningful change or test. Never replace measured results with estimates.

## Experiment register

| ID | Date (UTC) | Stage | System / model | Test | Result | Evidence |
|---|---|---|---|---|---|---|
| — | — | — | — | No experiments run yet | Pending system discovery | — |

## Baseline system

**Status:** Not yet inspected. Do not assume hardware specifications from earlier planning notes; verify on the machine being tested.

| Component | Measured value | Evidence / command | Status |
|---|---|---|---|
| Ubuntu version | Pending | `cat /etc/os-release` | Not measured |
| Kernel | Pending | `uname -a` | Not measured |
| CPU | Pending | `lscpu` | Not measured |
| System RAM | Pending | `free -h` | Not measured |
| GPU model / VRAM | Pending | `nvidia-smi` | Not measured |
| NVIDIA driver / CUDA | Pending | `nvidia-smi` | Not measured |
| Storage and free space | Pending | `lsblk`, `df -h` | Not measured |
| Docker | Pending | `docker version` | Not measured |
| Docker Compose | Pending | `docker compose version` | Not measured |
| Network | Pending | `ip -brief address` | Not measured |

## Experiment entry template

Copy this section for each experiment.

### EXP-YYYYMMDD-NNN — Short name

- **Date/time and timezone:**
- **Phase:**
- **Objective:**
- **Host configuration:** CPU, RAM, GPU, VRAM, driver, OS, storage.
- **Software versions:** Docker, Harbor, runtime, benchmark harness and dependencies.
- **Model:** Exact repository/model ID, revision, quantization, format and checksum.
- **Inference configuration:** Runtime, context length, batch size, parallelism, sampling settings, GPU offload.
- **Dataset / benchmark:** Exact benchmark name, split, version/revision and evaluation protocol.
- **Command / procedure:** Link to the corresponding section in `PROCEDURE.md`.
- **Environment controls:** Seeds, network access, timeouts, container limits and other relevant settings.
- **Raw result:** Exact output, score, pass rate and sample count.
- **Performance:** Time per task, tokens/sec, latency, peak VRAM, GPU utilization, RAM and disk use where available.
- **Failures / anomalies:**
- **Interpretation:** What the result does and does not demonstrate.
- **Evidence:** Logs, report paths, commit, run IDs or artifact links.
- **Decision / next action:**

## Benchmark result summary

| Benchmark | Exact version / split | Harness / agent | Attempts | Score / pass rate | Runtime | Status | Experiment ID |
|---|---|---|---:|---:|---:|---|---|
| SWE-bench | Pending selection | Pending | — | — | — | Not run | — |
| SWE-bench Pro | Pending selection | Pending | — | — | — | Not run | — |
| SWE-Rebench | Pending selection | Pending | — | — | — | Not run | — |
| Terminal-Bench | Pending selection | Pending | — | — | — | Not run | — |
| LiveBench | Pending selection | Pending | — | — | — | Not run | — |
| HumanEval | Pending selection | Pending | — | — | — | Not run | — |
| MBPP | Pending selection | Pending | — | — | — | Not run | — |

## Installation and validation status

| Component | Intended state | Validation evidence | Status |
|---|---|---|---|
| Docker Engine | Installed and operational | Pending | Not started |
| Docker Compose plugin | Operational | Pending | Not started |
| Harbor | Registry healthy; push/pull test passes | Pending | Not started |
| Model runtime | Model serves inference | Pending | Not started |
| Model | Exact model and revision recorded | Pending | Not started |

## Rules for trustworthy results

1. Record exact model IDs and immutable revisions; names such as "Qwen 3.8 27B" are not sufficiently precise.
2. Record quantization accurately. Do not infer bit width from a filename or label.
3. Record benchmark version, dataset split, harness, agent/scaffold, prompts, tool configuration and evaluation settings.
4. Separate model capability from agent/harness capability. An agent benchmark score is not a pure model score.
5. Do not compare scores produced by materially different protocols as if they were directly equivalent.
6. Keep raw logs and reports where licensing and dataset terms permit. Do not commit model weights, credentials, tokens, private datasets or large generated artifacts.
7. Mark failed, interrupted and partial runs. Never silently discard them.
8. Record resource measurements and repeatability information; include run count and variance when practical.
9. A test is "passed" only when its stated acceptance criteria and evidence are recorded.
