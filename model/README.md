# Model Benchmark JSON Package

## Directory layout

```text
model/
├── benchmark_comparison.json
├── README.md
├── Qwen3.5-9B/
│   ├── model.json
│   └── result/ (six benchmark JSON files)
├── Qwen3-8B/
│   ├── model.json
│   └── result/ (six benchmark JSON files)
├── DeepSeek-R1-Distill-Qwen-7B/
│   ├── model.json
│   └── result/ (six benchmark JSON files)
└── MiMo-7B-RL-no-MTP/
    ├── model.json
    └── result/ (six benchmark JSON files)
```

Each model directory contains `model.json` for model identity, Q5_K_M weight size, hardware configuration, FP16 KV cache, VRAM planning and estimated inference speed. Its `result/` directory contains one JSON file per benchmark.

## Data status

All scores, VRAM figures and throughput figures are copied from `model_result_kids_vinothsir.docx`. They are projected estimates, not measurements from an RTX 5070 benchmark run. Each JSON record therefore uses `"data_status": "projected_estimate"` and `"measured": false`. Do not present these values as experimentally verified results.

The Qwen entry is recorded as Qwen3-8B, matching the source report; it is not renamed to Qwen3-Coder. The MiMo entry retains the no-MTP GGUF qualifier used in the source report.

All sizes are in GiB. Benchmark results are percentages; the metric is recorded per benchmark.
