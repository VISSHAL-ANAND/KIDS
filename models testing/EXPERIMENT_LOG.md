# Model Testing — Experiment Log

This file is the authoritative record of experiments, measured results, failures, and decisions. Add an entry for every meaningful change or test. Never replace measured results with estimates.

## Experiment register

| ID | Date (UTC) | Stage | System / model | Test | Result | Evidence |
|---|---|---|---|---|---|---|
| SYS-001 | 2026-11-04 (local time, Asia/Kolkata) | System discovery | Dell Precision 3650 Tower | Read-only host inventory | Partial: OS, CPU, GPU, memory and storage observed; Docker absent; sudo denied | User-provided terminal photos and typed output |

## Baseline system

**Status:** Partially inspected on 2026-11-04 from user-provided terminal output. Values below are observations, not independently collected by the assistant. Memory figures were transcribed by the user and are approximate.

| Component | Measured value | Evidence / command | Status |
|---|---|---|---|
| Workstation | Dell Precision 3650 Tower | `hostnamectl` photo | Observed |
| Ubuntu version | Ubuntu 24.04.4 LTS | `hostnamectl` photo | Observed |
| Kernel | `6.8.0-136-generic` | `hostnamectl` photo | Observed |
| Architecture | x86_64 | `lscpu` photo | Observed |
| CPU | Intel Core i7-10700K, 8 cores / 16 threads | `lscpu` photo | Observed |
| System RAM | Approximately 15 GiB total; user reported ~2.1 GiB used and ~10 GiB free/available | User-transcribed `free -h` output | Approximate; verify later |
| Swap | 6 GiB total, 0 used | User-transcribed `free -h` output | Approximate; verify later |
| GPU model / VRAM | NVIDIA GeForce RTX 5070; 12,227 MiB total VRAM | `nvidia-smi` photo | Observed |
| NVIDIA driver | 610.43.02 | `nvidia-smi` photo | Observed |
| Storage `/` | 79 GB total, 51 GB available | `df -h` photo | Observed |
| Storage `/home` | 916 GB total, 846 GB available | `df -h` photo | Observed |
| Storage `/clusterhome` | 590 GB total, 491 GB available | `df -h` photo | Observed; network mount |
| Storage `/srv/datasets`, `/srv/apps`, `/srv/cosmos` | Each 98 GB total, 7.9 GB available (92% used) | `df -h` photo | Observed; avoid for new data |
| Docker | Not installed (`docker` command unavailable) | User report after `docker --version` | Observed |
| Docker Compose | Not checked successfully because Docker is absent | — | Pending |
| Administrator access | `sudo -v` returned permission denied | User report | Blocker; admin rights required |
| Network / listening ports | Pending | `ip -brief address`, `ss -lnt` | Not measured |

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


## SYS-001 — Initial host discovery and access check (2026-11-04)

- **Actions:** Read-only inspection using `hostnamectl`, `nvidia-smi`, `free -h`, `lscpu`, `df -h`, `docker --version`, `id`, and `sudo -v`.
- **Findings:** See baseline system table above.
- **Docker / Harbor:** Neither installation has been attempted. Docker CLI was not present.
- **Blocker:** The logged-in `student` account (`uid=21000`, primary group `students`) received permission denied from `sudo -v`. No administrator privileges have been established.
- **Storage caution:** `/srv/datasets`, `/srv/apps`, and `/srv/cosmos` each have only 7.9 GB available. Do not place Docker/Harbor data there. `/home` has 846 GB available, but suitability and ownership must be confirmed by the system administrator.
- **Decision:** Stop before installation. Obtain authorized administrator access or an administrator-assisted installation. Do not attempt privilege escalation or bypass institutional controls.
- **Model selection:** Deferred until infrastructure setup is ready, per user instruction.
