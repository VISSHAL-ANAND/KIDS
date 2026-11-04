# Model Testing — Procedure and Execution Path

This document records the intended path, commands, configuration decisions, acceptance criteria and troubleshooting notes. Update it as the actual work is performed. Clearly distinguish planned steps from steps completed on the machine.

## 0. Scope and operating rules

**Goal:** Discover the target Ubuntu machine, install and validate Docker and Harbor, install and validate a selected local model, then run the requested coding and agent benchmarks. Track measured results in `EXPERIMENT_LOG.md`.

**Target machine:** Ubuntu host with an NVIDIA RTX 5090 reported by the user. All other specifications are unverified until collected from the target host.

**Prior planning context (not yet verified on this machine):**
- GPU: RTX 5090; previously reported 32 GB VRAM.
- System RAM: previously reported approximately 35 GB.
- Model shorthand: "Qwen 3.8 - 27B"; exact model identity is unresolved.
- Proposed format/quantization: GGUF / Q5_K_M; the prior "4-bit" description needs verification against the exact artifact.
- Proposed inference runtime: llama.cpp server.
- LM Studio was previously considered as a single-user client; it is not a prerequisite for the server benchmark workflow.

Do not assume this is the same machine or that reported resource allocation is available. Verify actual hardware, driver, storage and permissions before installation.

### Execution discipline

- Run one phase at a time. Capture command output and versions.
- Prefer official vendor documentation and pinned/reproducible versions.
- Do not run destructive cleanup, prune Docker volumes, repartition disks, disable security controls, or overwrite existing services without explicit approval.
- Do not expose registry passwords, access tokens, private keys, serial numbers or other secrets in logs.
- Keep model weights and benchmark datasets/artifacts out of Git unless their licenses and repository size make that appropriate.
- Record every completed command, configuration change, test, failure and rollback in `EXPERIMENT_LOG.md`.

## 1. Phase 1 — Discover the system (READ-ONLY)

Run the following on the Ubuntu machine and share the complete output. These commands inspect the machine; they do not install or modify software.

```bash
printf '\n===== OS =====\n'
cat /etc/os-release
printf '\n===== KERNEL / ARCH =====\n'
uname -a
printf '\n===== CPU =====\n'
lscpu
printf '\n===== MEMORY =====\n'
free -h
printf '\n===== GPU / DRIVER =====\n'
if command -v nvidia-smi >/dev/null 2>&1; then
  nvidia-smi
  nvidia-smi --query-gpu=name,uuid,memory.total,memory.free,driver_version,pstate,power.limit --format=csv
else
  echo "nvidia-smi not found"
fi
printf '\n===== NVIDIA MODULE =====\n'
lsmod | grep -E '^nvidia' || true
printf '\n===== STORAGE =====\n'
lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINTS,MODEL
df -hT
printf '\n===== PCI DEVICES =====\n'
lspci | grep -Ei 'vga|3d|display|nvidia' || true
printf '\n===== NETWORK INTERFACES =====\n'
ip -brief address
printf '\n===== EXISTING CONTAINERS / TOOLS =====\n'
command -v docker || true
command -v podman || true
command -v harbor || true
docker version 2>&1 || true
docker compose version 2>&1 || true
printf '\n===== LISTENING PORTS =====\n'
ss -lnt
```

If a command is missing, keep its "not found" output; do not install diagnostic utilities yet.

**Phase 1 acceptance criteria**
- Exact Ubuntu release, architecture, kernel and CPU recorded.
- Total and available RAM recorded.
- RTX 5090 identity, reported VRAM, NVIDIA driver and GPU visibility confirmed.
- Storage devices, filesystems and free space recorded.
- Existing Docker/Podman installations and listening ports identified.
- Any shared-machine, permission, proxy or network restrictions clarified.

**Stop here** until the output has been reviewed. Check whether this is a personal workstation or a shared/managed host before changing drivers, Docker groups, firewall rules or services.

## 2. Phase 2 — Install and validate Docker

After Phase 1 review:
1. Check the installed Ubuntu release against Docker Engine's official supported installation instructions.
2. Check for conflicting packages and existing container workloads before making changes.
3. Install Docker Engine, the containerd runtime, Buildx and the Docker Compose plugin using the official Docker repository instructions.
4. Verify daemon state and versions.
5. Run a minimal container smoke test, then a build/run test.
6. Verify container restart behavior and GPU container access only after NVIDIA Container Toolkit compatibility is checked.

**Initial validation commands (after installation):**

```bash
docker version
docker info
docker compose version
sudo systemctl is-active docker
sudo docker run --rm hello-world
```

Do not add the user to the `docker` group without discussing the security implication: membership effectively grants root-equivalent control of the host. Use `sudo docker` unless a deliberate access decision is made.

**Acceptance criteria:** Docker daemon is active; client/server versions are recorded; Compose works; hello-world exits successfully; no existing workload was disrupted.

Official starting point: https://docs.docker.com/engine/install/ubuntu/

## 3. Phase 3 — Install and validate Harbor

Harbor is a self-hosted OCI container image registry, not a replacement for Docker Engine. We will deploy it using its supported installation method after Docker is stable.

Before installation:
- Choose a hostname or local DNS name and decide whether this is LAN-only or remotely accessible.
- Decide HTTPS and certificate handling. Avoid normalizing insecure/plaintext registry access.
- Check available disk space and choose a persistent data directory.
- Check port conflicts (commonly 80/443; actual configuration must be explicit).
- Decide authentication and who may push/pull.
- Confirm whether the host is suitable for running a registry alongside model inference.

Installation path:
1. Consult the official Harbor installation documentation and select a compatible, pinned release.
2. Download the official release package and verify its checksum/signature where published.
3. Configure hostname, HTTPS certificate/private key, persistent data path and admin bootstrap secret without committing secrets.
4. Generate the Harbor deployment configuration and deploy with Docker Compose.
5. Check container health and Harbor UI/API readiness.
6. Log in with a test account, push a small test image, pull it back, and verify the digest.
7. Confirm restart persistence and document backup/recovery expectations.

**Acceptance criteria:** Harbor components are healthy; authenticated login succeeds; test image push and pull succeed; digest matches; persistent data survives a controlled restart; TLS and access controls are documented.

Official starting point: https://goharbor.io/docs/

## 4. Phase 4 — Model selection, installation and inference validation

Do not download a model until its exact identity is confirmed. "Qwen 3.8 - 27B" is ambiguous and must not be guessed.

Record:
- Official model repository and exact model ID.
- Exact revision/commit and artifact URL.
- License and permitted usage.
- Parameter count, architecture and supported context.
- File format and quantization metadata.
- File size and checksum.
- Expected VRAM/RAM/disk needs.
- Runtime and runtime version.

The previously proposed GGUF Q5_K_M and llama.cpp server are candidates, not yet validated decisions. Confirm whether the chosen artifact actually exists in that format and whether its quantization is accurately described. Validate GPU offload, context allocation, prompt processing, generation speed, memory use and stability with a fixed test prompt set.

**Acceptance criteria:** Model loads without unexpected CPU fallback; inference returns correct structured output; peak VRAM/RAM and tokens/sec are measured; sustained run completes without crash or thermal/power issue; configuration is saved.

## 5. Phase 5 — Benchmark preparation

Benchmark results depend on the complete execution stack, not only the model. Before each run, pin and record:
- Benchmark repository and immutable commit/release.
- Dataset and split/version.
- Evaluation harness and dependencies.
- Agent scaffold, system prompt and tool definitions, where applicable.
- Model revision, quantization and inference parameters.
- Container image digests and environment variables (excluding secrets).
- Network policy, timeouts, retries, concurrency and resource limits.
- Random seeds and number of repetitions, where supported.

Check benchmark licenses, data access requirements, compute costs and whether a benchmark requires API access or external services. Never claim a benchmark was run if only a subset or a smoke test was completed.

### Requested benchmark matrix

| Benchmark | Main evaluation focus | Preparation notes |
|---|---|---|
| SWE-bench | Resolve real GitHub issues in repositories | Select a specific track/split and a compatible agent harness; record resolved-instance evaluation. |
| SWE-bench Pro | More challenging software engineering evaluation | Verify current official access, license, split and evaluation requirements before downloading/running. |
| SWE-Rebench | Reproducible software engineering benchmark results | Pin benchmark version and harness; record exact task set and protocol. |
| Terminal-Bench | Agent tasks requiring terminal/tool use | Pin task set and agent/runtime; capture terminal logs and task outcomes. |
| LiveBench | Contamination-resistant, frequently refreshed evaluation | Record release/date and category configuration; scores across releases may not be directly comparable. |
| HumanEval | Python function completion | Record pass@k, sample count, decoding settings and execution sandbox. |
| MBPP | Python programming problems | Record exact subset, pass@k, sample count, decoding settings and execution sandbox. |

### Benchmark execution order

1. HumanEval and MBPP — establish basic code-generation baseline.
2. LiveBench — broader reasoning/coding baseline, with dated release.
3. Terminal-Bench — validate tool-use agent and terminal environment.
4. SWE-bench — full software engineering workflow.
5. SWE-bench Pro — only after access, licensing and compute requirements are confirmed.
6. SWE-Rebench — run with its pinned, documented protocol.

This order is a proposed workflow, not a claim that these benchmarks share a common harness. Build and validate each harness separately.

## 6. Phase 6 — Results and analysis

For every run, create an experiment entry in `EXPERIMENT_LOG.md`. Preserve:
- Raw scores and task counts.
- Pass@k or resolved rate where applicable.
- Runtime, latency and tokens/sec.
- Peak GPU VRAM, GPU utilization, system RAM and disk.
- Failures, timeouts, invalid outputs and skipped tasks.
- Exact environment and configuration.
- Links to raw reports/logs and immutable code revisions.

Compare only runs with sufficiently similar evaluation protocols. Explain limitations and avoid collapsing different benchmark scores into one overall number.

## 7. Completion criteria

The experiment is complete only when:
- Host inventory is verified.
- Docker and Harbor pass their acceptance tests.
- Exact model and runtime are reproducibly configured.
- All requested benchmarks have either completed or have a documented blocker (with no false "pass").
- Raw evidence and summary results are recorded.
- The procedure reflects what was actually done, not merely the original plan.
- Repository contains no secrets, private credentials, model weights or unlicensed benchmark data.

## Change history

| Date | Change |
|---|---|
| 2026-11-04 | Initial procedure created; no host installation or benchmark execution has been performed by this document's author. |
