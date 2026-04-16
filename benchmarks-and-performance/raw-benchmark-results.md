# Raw Benchmark Results

**Raw Benchmark Results**

This page collects all known benchmark results for the CMP 170HX in one place, with context for interpreting each figure. Results are sourced from niconiconi's 2023 article, the 2025 arXiv paper, and community testing including our own.

***

**clpeak — Floating-Point Compute**

clpeak is an OpenCL benchmark that measures peak compute throughput using FMA-heavy kernels. On the CMP 170HX it captures both the throttled and unthrottled performance paths.

_FP32 with FMA (standard, throttled):_

```
Single-precision compute (GFLOPS)
  float   : 394.77
  float2  : 394.77
  float4  : 394.77
  float8  : 394.77
  float16 : 394.77
```

_FP32 without FMA (modified clpeak, unthrottled):_

```
Single-precision compute (GFLOPS)
  float   : 6285.48
  float2  : 6287.64
  float4  : 6294.30
  float8  : 6268.80
  float16 : 6252.80
```

_FP64 with FMA (throttled):_

```
Double-precision compute (GFLOPS)
  double   : 182.72
  double2  : 182.55
  double4  : 182.19
  double8  : 181.48
  double16 : 180.08
```

_FP64 without FMA:_

```
Double-precision compute (GFLOPS)
  double   : 94.98
  double2  : 94.93
  double4  : 94.84
  double8  : 94.65
  double16 : 94.29
```

**clpeak — Memory Bandwidth**

```
Global memory bandwidth (GBPS)
  float   : 1165.79
  float2  : 1269.69
  float4  : 1343.50
  float8  : 1355.40
  float16 : 1350.14
```

Peak measured: **1,355 GB/s** — essentially identical to the A100 PCIe 40GB's real-world bandwidth, and \~1.5× faster than the RTX 4090.

**mixbench — FP16**

```
FP16 (half precision): ~41,869 GFLOPS (~42 TFLOPS)
```

FP16 is entirely unthrottled. This is the most useful compute path for modern AI inference workloads.

**clpeak — Integer**

```
Integer compute (GIOPS)
  int   : 12499.07
  int2  : 12518.02
  int4  : 12494.16
  int8  : 12547.55
  int16 : 12540.22
```

**12.5 TIOPS** — 80% of RTX 2080 Ti, 62.5% of A100. Fully unthrottled and the best compute path for integer workloads.

**clpeak — PCIe Transfer Bandwidth**

```
enqueueWriteBuffer              : 0.85 GB/s
enqueueReadBuffer               : 0.84 GB/s
enqueueWriteBuffer non-blocking : 0.85 GB/s
enqueueReadBuffer non-blocking  : 0.83 GB/s
```

Approximately **0.85 GB/s** — the combined result of the firmware Gen 1 lock and the PCB x4 width restriction. For comparison, full PCIe 4.0 x16 delivers \~64 GB/s.

**gpu\_burn — Tensor Core (Dispatch-Gated)**

```
GPU 0: NVIDIA Graphics Device
Using FLOATS, using Tensor Cores
proc'd: 24504 (6236 Gflop/s)
```

\~**6.2–6.3 TFLOPS** via cuBLAS Tensor Core path.

This figure is now fully explained by microbenchmarking research (Zenodo 19002983). The ceiling is not caused by the same mechanism as FMA throttling but by a **dispatch-level hardware gate** with two components: a **256 fixed-cycle MMA instruction latency** that cannot be hidden by pipeline overlap, and a limit of **4 warps per SM** that can simultaneously issue Tensor Core instructions. These two constraints together produce exactly 6.3 TFLOPS — 1/32 of the theoretical FP16 Tensor Core peak. The Tensor Core execution units are physically intact; only the dispatch gate limits throughput.

**Hashcat — MD5 (Integer Workload)**

```
Hash-Mode 0 (MD5)
Speed.#1.........: 43930.0 MH/s @ Accel:64 Loops:512 Thr:1024 Vec:1
```

**43,930 MH/s** for MD5. This is 67% of the A100 (\~64,900 MH/s) and slower than an RTX 3080 (\~54,000 MH/s) — but it confirms the card is fully functional for integer-heavy workloads.

**Blender Benchmark (our results, Blender 5.1, CUDA)**

```
monster:   ~562–580 samples per minute
junkshop:  ~548–565 samples per minute
classroom: ~368–383 samples per minute
```

Poor results due to FMA throttling — Blender's CUDA renderer is FMA-heavy. For context: A100 SXM4 achieves \~3,646 samples/min on monster, and the CMP 40HX achieves \~1,269 samples/min. The card is operating near its throttled ceiling for this workload.

**Summary Table**

| Benchmark               | Result            | Notes               |
| ----------------------- | ----------------- | ------------------- |
| FP32 FMA (clpeak)       | 0.39 TFLOPS       | Throttled           |
| FP32 no-FMA (clpeak)    | 6.25 TFLOPS       | Unthrottled         |
| FP16 (mixbench)         | 42 TFLOPS         | Unthrottled         |
| FP64 FMA (clpeak)       | 0.18 TFLOPS       | Throttled           |
| FP64 no-FMA (clpeak)    | 0.094 TFLOPS      | Unthrottled but low |
| INT32 (clpeak)          | 12.5 TIOPS        | Unthrottled         |
| Tensor Core (gpu\_burn) | 6.2 TFLOPS        | cuBLAS workaround   |
| Memory BW (clpeak)      | 1,355 GB/s        | Full speed          |
| PCIe BW (clpeak)        | 0.85 GB/s         | Locked              |
| Hashcat MD5             | 43,930 MH/s       | Integer, full speed |
| Blender monster (CUDA)  | \~570 samples/min | FMA-throttled       |
