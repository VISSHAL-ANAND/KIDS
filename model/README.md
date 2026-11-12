# Model Benchmark Results

This folder contains the model configurations and benchmark result records.

## Layout

- `benchmark_comparison.json` — side-by-side score comparison.
- Each model directory contains `model.json` and a `result/` directory.
- Each benchmark file records the test definition, execution status, pass/fail fields and score.

## Result interpretation

The numeric scores in these files are **projected estimates**, not results from completed benchmark runs. No SWE-bench, SWE-bench Pro, Terminal-Bench, SWE-ReBench, HumanEval or MBPP execution has been recorded for these model/GPU combinations.

Therefore each benchmark record deliberately has:
- `test.execution_status = "not_run"`
- `test.total_tests = null`
- `test.passed = null`
- `test.failed = null`
- `result.status = "projected_estimate"`

Null pass/fail counts mean there is no execution evidence to count. They must be filled with measured integers only after a benchmark run, with its dataset version, harness, run date and raw output captured.

## Hardware planning assumptions

The model configuration files capture the RTX 5070 12 GB planning target, Q5_K_M weights, FP16 KV cache, context length and estimated inference speed. These planning values are estimates and must be measured on the target host before being treated as actual performance.

## Model naming

The second model is recorded as `Qwen3-8B`. It must not be renamed to Qwen3-Coder-8B unless the exact checkpoint is confirmed. The MiMo model is recorded as the no-MTP GGUF variant used for the memory/speed estimate.
