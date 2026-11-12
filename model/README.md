# Model Benchmark Results

## Repository layout

- `benchmark_comparison.json` — four-model score matrix transcribed from the supplied report.
- Each model folder contains model configuration and benchmark result summaries.
- `benchmark_fixtures/` — 24 deterministic task-level fixture datasets (4 models × 6 benchmarks), with 200 individual records in each JSON file. These support report, parser and dashboard validation; their records are marked `synthetic: true`.
- The original Qwen HumanEval execution artifact remains at `Qwen3.5-9B/result/HumanEval.json` with its uploaded 164 task records.

## Result interpretation

The comparison matrix contains the target percentages from the supplied Word report. The task-level fixture datasets reproduce those target percentages using 200 generated rows per model/benchmark combination. Fixture rows are not official benchmark task IDs, prompts, patches, hidden tests or evaluator outputs.

The uploaded Qwen HumanEval run contains 164 records: 60 passed, 102 failed and 2 timed out (36.59% pass@1). Its exact checkpoint and execution environment were not identified in the uploaded JSONL. The report's Qwen3.5 9B HumanEval target is 89.0%; the uploaded run's 36.59% remains separately preserved.

## Reproducibility requirements

A genuine benchmark run must pin the model checkpoint, quantization, runtime, prompt/scaffold, dataset version and evaluator configuration. Store the raw evaluator output and environment metadata alongside the result summary. Do not infer GPU speed, VRAM use or execution duration from the fixture records.
