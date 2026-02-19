# GPT-OSS-120B Production Inference Benchmark  
### KServe + vLLM + NVIDIA H100 (96GB)

This repository contains **production-style load testing artifacts** for serving **GPT-OSS-120B** using **KServe and vLLM** on a single **NVIDIA H100 (96GB)** GPU.

The goal is **not** to present a synthetic micro-benchmark, but to document **how a large LLM behaves under sustained, concurrent, real-world inference traffic**.

This repository accompanies the following articles:
- *Deploying GPT-OSS-120B in Production* (architecture & methodology)
- *Scaling GPT-OSS-120B for Production Inference* (load testing & saturation behavior)

---

## What This Repository Is

- ✅ A **reproducibility aid** for the published benchmark results  
- ✅ A reference for **capacity planning and saturation analysis**  
- ✅ A collection of **real load-test configurations**, not toy examples  
- ✅ Curated dashboards and summary tables focused on actionable signals  

---

## What This Repository Is NOT

- ❌ A model performance leaderboard  
- ❌ A universal benchmark applicable to all deployments  
- ❌ A synthetic offline throughput test  
- ❌ A turnkey “copy-paste” production configuration  

All results are **context-dependent** and tied to the described hardware, model, and inference configuration.

---

## Test Environment Summary

| Component | Configuration |
|---------|--------------|
| Model | GPT-OSS-120B |
| Serving Platform | KServe (Kubernetes) |
| Inference Engine | vLLM |
| GPU | NVIDIA H100 96GB |
| Response Mode | Streaming enabled |
| Load Generator | Grafana k6 (v0.49.0) |
| Test Duration | ~12–15 minutes per scenario |

### Key vLLM Parameters

These parameters were held constant across all tests:

max-model-len = 10000
max-num-batched-tokens = 8000
max-num-seqs = 512
gpu-memory-utilization = 0.95
cuda-graph-sizes = 2048

The intention was to maximize GPU utilization and batching efficiency while remaining realistic for production inference.

---

## Repository Structure

.
├── load-tests/
│ ├── k6-test1-baseline.js
│ ├── k6-test2-stable.js
│ └── k6-test3-saturation.js
│
├── dashboards/
│ ├── test2-throughput-latency.png
│ ├── test3-saturation-behavior.png
│ └── cache-scheduler-state.png
│
├── tables/
│ ├── capacity-zones.md
│ └── latency-ttft-summary.md
│
└── notes/
└── limitations.md

---

## Load Tests

The `load-tests/` directory contains the **exact k6 configurations** used during benchmarking.

Each test simulates:
- Multi-turn chat completion requests
- Streaming responses
- Fixed temperature (0)
- Context sizes between 2048–4096 tokens
- Gradual ramp-up and sustained concurrency (no traffic spikes)

### Test Scenarios

| Test | Purpose |
|----|-------|
| Test 1 | Baseline validation & environment sanity check |
| Test 2 | Stable high-concurrency reference baseline |
| Test 3 | Intentional saturation & throughput ceiling discovery |

> **Important:**  
> Early failures in Test 1 were caused by **OOMKilled load-generator pods**, not backend saturation. This distinction is critical when interpreting results.

---

## Dashboards

The `dashboards/` directory contains **curated Grafana screenshots**, intentionally limited to high-signal views:

- Throughput vs concurrency
- P95 latency and queue wait time
- GPU KV-cache utilization
- Scheduler running vs waiting states
- Successful vs total request rates

Raw Prometheus exports are intentionally omitted to reduce noise.

---

## Summary Tables

The `tables/` directory provides condensed, decision-oriented summaries:

- Safe operating zones
- Early degradation thresholds
- Saturation boundaries
- Latency and TTFT trends across concurrency ranges

These tables are derived from dashboard observations and are meant to support **capacity planning discussions**, not exact numerical guarantees.

---

## Key Observations (High-Level)

- Stable performance up to ~400–500 concurrent requests per pod
- Early degradation appears between ~800–1200 concurrent requests (queue growth)
- True saturation occurs beyond ~1200–1500 concurrent requests
- Throughput plateaus due to GPU token generation limits
- Queue pressure is the earliest and most reliable saturation signal
- Load-generator stability is mandatory for trustworthy benchmarks

---

## Limitations

- Single-GPU (H100) setup only  
- Single model configuration (GPT-OSS-120B)  
- Results depend on prompt length, output length, and streaming behavior  
- No comparison across inference engines or GPU types  

See `notes/limitations.md` for details.

---

## Intended Audience

- ML Platform Engineers  
- MLOps / Infrastructure Engineers  
- LLM system architects  
- Teams operating large-scale LLM inference in production  

---

## Final Notes

This repository is meant to support **transparent, honest discussion** about large-model inference behavior under load.

Feedback, questions, and corrections are welcome.
