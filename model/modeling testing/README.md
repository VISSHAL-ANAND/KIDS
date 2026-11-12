# Benchmark Task Fixtures

This directory contains deterministic synthetic task-level fixtures for validating JSON parsing, aggregation, comparison views and report formatting.

- 4 models × 6 benchmarks; 200 fixture records per combination (4,800 records total).
- The 200-row fixture denominator reproduces every target percentage in `model/benchmark_comparison.json` exactly.
- Every record and summary carries `synthetic: true` and `result_type: "synthetic_fixture"`.
- IDs use `FIXTURE-`; they are not official benchmark instance IDs. Records do not contain official task prompts, patches, hidden tests or evaluator outputs.
- `fixture_count` is the generated fixture row count, not the official benchmark dataset size. Where known, the reference dataset size is separately recorded in each file.
- The original Qwen HumanEval execution artifact remains untouched in `model/Qwen3.5-9B/result/HumanEval.json`.

Each model/benchmark JSON file contains a summary and all 200 individual fixture records. These files are suitable for software/demo validation, not as evidence of benchmark execution. Actual benchmark results require pinned datasets, runtime configuration and evaluator logs.
