# SPEC.md: "The Human How"; Exact technical requirements, environment setup, implementation details, etc.

## Execution Description

Builds src/nccl_bw.cu and runs bin/nccl_bw for each yaml collective over message sizes from min_bytes to max_bytes by step_factor. num_gpus: GPUs in the communicator (capped at the GPUs present, max 8). collective: comma-separated all_reduce, all_gather, broadcast, reduce, reduce_scatter. warmup_iters and num_iterations time each size Sweep dimensions: num_gpus, collective, min_bytes, max_bytes, step_factor, warmup_iters, num_iterations.

## Parameters

| Parameter | CLI Flag | Tested Values | Default | Description |
| --- | --- | --- | --- | --- |
| num_gpus | `--num-gpus` | smoke=1, baseline=1, extended=1 | 1 | From Parameter list; see Execution Description With Parameters. |
| collective | `--collective` | smoke=all, baseline=all, extended=all | all | From Parameter list; see Execution Description With Parameters. |
| min_bytes | `--min-bytes` | smoke=8, baseline=8, extended=8 | 8 | From Parameter list; see Execution Description With Parameters. |
| max_bytes | `--max-bytes` | smoke=1024, baseline=16777216, extended=16777216 | 16777216 | From Parameter list; see Execution Description With Parameters. |
| step_factor | `--step-factor` | smoke=2, baseline=2, extended=2 | 2 | From Parameter list; see Execution Description With Parameters. |
| warmup_iters | `--warmup-iters` | smoke=1, baseline=5, extended=10 | 5 | From Parameter list; see Execution Description With Parameters. |
| num_iterations | `--num-iterations` | smoke=2, baseline=295000, extended=620000 | 295000 | From Parameter list; see Execution Description With Parameters. |

## Invocation

```bash
Compile nccl_bw.cu with nvcc and NCCL; run bin/nccl_bw <min_bytes> <warmup> <iters> <num_gpus> <collective>
```

## Raw Output Format

NCCL all_reduce stdout plus one CSV row. Column order is busbw then algbw. A single row is duplicated to two samples

sample_index,status,collective,num_gpus,bytes,busbw_gb_s,algbw_gb_s,latency_us,error_message
0,ok,all_reduce,1,1024,na (requires at least two GPUs),na (requires at least two GPUs),na (requires at least two GPUs),

## Metrics

- **#1: Bus bandwidth at max message size, GB/s** — stored as `busbw_gb_s`.
- **#2: Algorithm bandwidth at max message size, GB/s** — stored as `algbw_gb_s`.
- **#3: Latency at min message size, us** — stored as `latency_us`.

## Framework

Builds src/nccl_bw.cu and runs bin/nccl_bw for each yaml collective over message sizes from min_bytes to max_bytes by step_factor. num_gpus: GPUs in the communicator (capped at the GPUs present, max 8). collective: comma-separated all_reduce, all_gather, broadcast, reduce, reduce_scatter.

## Installation and Execution Summary

Compile src/nccl_bw.cu with nvcc/NCCL and run bin/nccl_bw for each collective and message size, parse busbw_gb_s, algbw_gb_s, and latency_us, to measure NCCL collective bandwidth. This is not upstream nccl-tests or MPI

## Platform Portability

- **AMD (primary):** ```bash
Compile nccl_bw.cu with nvcc and NCCL; run bin/nccl_bw <min_bytes> <warmup> <iters> <num_gpus> <collective>
```
- **NVIDIA:** Native NVIDIA CUDA workload. Execute on the stated Ubuntu release with the host NVIDIA driver and CUDA userspace. ROCm porting notes do not apply.

## Model Context Protocols

- **Active:** None

## Execution-Loop Validation Contract

EXECUTION CHAIN: `run_benchmark.sh` ➔ raw output ➔ `scripts/parse_results.py` ➔ `results/benchmark.db` ➔ `scripts/validate_results.py`

This benchmark uses a lightweight, SQLite-integrated execution loop for result validation. All validation is performed by `scripts/validate_results.py`.

### Validation script usage

```bash
export BENCHMARK_PYTHON=/usr/bin/python3.13  # optional; select the installed interpreter

# After a live run:
".venv/bin/python" scripts/validate_results.py --db results/benchmark.db

# CI / no-GPU path (seeds fixture and validates it):
".venv/bin/python" scripts/validate_results.py --seed-fixture --quiet

# Override DB path via environment variable:
BENCHMARK_DB=tests/fixtures/benchmark.db \
  ".venv/bin/python" scripts/validate_results.py
```

### Run artifact contract

NCCL all_reduce stdout plus one CSV row. Column order is busbw then algbw. A single row is duplicated to two samples

sample_index,status,collective,num_gpus,bytes,busbw_gb_s,algbw_gb_s,latency_us,error_message
0,ok,all_reduce,1,1024,na (requires at least two GPUs),na (requires at least two GPUs),na (requires at least two GPUs),

```bash
bash run_benchmark.sh --help
bash run_benchmark.sh --profile smoke --validate
bash run_benchmark.sh --profile baseline --validate
bash run_benchmark.sh --profile extended --validate
```
`run_benchmark.sh --help` prints usage and exits. The harness calls `scripts/ensure_setup.sh` when `.setup_state` is absent.

### Required integrity checks (built into `validate_results.py`)

1. Latest run exists and `runs.status = 'ok'`.
2. `run.error_message` is NULL.
3. `started_at` and `finished_at` are valid ISO-8601 UTC strings.
4. All required aggregate metrics in `runs` are non-NULL and finite.
5. All required aggregate metrics are physically sensible (positive values). Builds src/nccl_bw.cu and runs bin/nccl_bw for each yaml collective over message sizes from min_bytes to max_bytes by step_factor. num_gpus: GPUs in the communicator (capped at the GPUs present, max 8). collective: comma-separated all_reduce, all_gather, broadcast, reduce, reduce_scatter.
6. At least 2 sample rows exist for the latest `run_id` (sweep coverage).
7. No sample has `status = 'error'`.
8. Builds src/nccl_bw.cu and runs bin/nccl_bw for each yaml collective over message sizes from min_bytes to max_bytes by step_factor. num_gpus: GPUs in the communicator (capped at the GPUs present, max 8). collective: comma-separated all_reduce, all_gather, broadcast, reduce, reduce_scatter.

### Baseline / Threshold configuration (`config/benchmark_config.yaml`)

Expected ranges and gates live in `config/benchmark_config.yaml` under `baselines:` or `thresholds:`. To update them, edit that file — never edit validation code directly.

Threshold key suffixes encode comparison direction when `thresholds:` is present: `_min` → observed value must be ≥ threshold. `_max` → observed value must be ≤ threshold. Informational `baselines:` ranges are not pass/fail gates.
