# Model Benchmark Results

## Repository layout

- `benchmark_comparison.json` — the four-model comparison from the supplied report.
- Each model folder contains `model.json` and a `result/` directory.
- Benchmark result files contain numeric summary scores.
- Where raw execution data is available, the benchmark file also includes every task record and the observed execution summary.

## Data categories

**Projected estimates:** The four-model comparison values transcribed from `model_result_kids_vinothsir.docx` are planning estimates, not measurements. They remain labelled `projected_estimate`.

**Actual execution:** The Qwen HumanEval upload contains 164 individual task outcomes and generated completions. Its observed result is 60 passed, 102 failed, and 2 timed out (36.59% pass@1). The exact Qwen checkpoint, run date, runtime, hardware, quantization and context settings were not present in those uploaded JSONL files.

The uploaded report projects 89.0% for Qwen3.5 9B HumanEval. The actual uploaded run is 36.59%. These are deliberately stored as separate values because a projection and an observed run are not interchangeable.

## Task-level data policy

A task-level record is included only when an original generation/evaluation artifact was supplied. The Qwen HumanEval file preserves all 164 task IDs, generated completions, evaluator result strings, pass/fail booleans and timeout outcomes.

The other benchmark cells currently contain report-level projections only. No per-task outcomes have been supplied for SWE-bench Verified, SWE-bench Pro, Terminal-Bench, SWE-ReBench or MBPP, so this repository does not invent task lists, pass counts or failure counts for them.

## Hardware planning

Model configuration files contain Q5_K_M, RTX 5070 12 GB planning assumptions, FP16 KV-cache estimates and projected inference speeds. These fields are planning estimates; they are not measurements from the HumanEval JSONL run.
