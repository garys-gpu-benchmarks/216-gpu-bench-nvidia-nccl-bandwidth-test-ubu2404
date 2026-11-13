# Results: 216 NCCL Bandwidth Test

**Main goal:** Measure NCCL collective bandwidth and latency  
**Tools:** Compile nccl_bw.cu with nvcc and NCCL; run bin/nccl_bw <min_bytes> <warmup> <iters> <num_gpus> <collective>

Reference results measured by the maintainer on the system shown in each section (generator TEMPLATE_00_139 · recorded 2026-11-13). Your own runs write `results/RESULTS_ACTUAL.md` in the same format.

> Smoke runs use deliberately small sizes. They check that the workload installs and runs end to end; their numbers are not performance results. Baseline and extended are.

<!-- profile:smoke -->
## smoke

- **System:** 1× NVIDIA H100 PCIe (81559 MiB) · Intel(R) Xeon(R) Platinum 8352Y CPU @ 2.20GHz · Ubuntu 24.04.5 LTS · kernel 6.8.0-138-generic · driver 595.91.07
- **Run:** 2026-11-13T14:15:58Z · status **N/A-EXPECTED** (exit 0)
- **Time (mm:ss):** setup 01:13 · benchmark 00:03 · whole run 01:25
- **Source:** run 216_20261113_141558
- **Command:** `bash run_benchmark.sh --smoke --num-gpus 1 --min-bytes 8 --max-bytes 1024 --step-factor 2 --warmup-iters 1 --num-iterations 2`

| # | Metric | Result |
|---|---|---|
| 1 | All-reduce bus bandwidth at max message size, GB/s, na on single GPU (allreduce_busbw_gb_s) | na (expected: needs at least two GPUs) |
| 2 | All-reduce algorithm bandwidth at max message size, GB/s, na on single GPU (allreduce_algbw_gb_s) | na (expected: needs at least two GPUs) |
| 3 | All-reduce latency at min message size, us, na on single GPU (allreduce_latency_at_min_bytes_us) | na (expected: needs at least two GPUs) |
<!-- /profile:smoke -->
