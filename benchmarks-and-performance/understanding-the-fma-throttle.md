# Understanding the FMA Throttle

**Understanding the FMA Throttle**

Before looking at any benchmark numbers, it is essential to understand why the CMP 170HX performs the way it does. Almost every confusing benchmark result — low power draw, poor rendering scores, misleading stress test readings — has a single root cause: the FMA throttle. Understanding it is the key to understanding everything else about this card.

<figure><img src="https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/roofline-cmp170hx.svg" alt=""><figcaption></figcaption></figure>

**What FMA Is**

Fused Multiply-Add (FMA) is a single CPU/GPU instruction that performs the operation `a × b + c` in one step, with a single rounding operation at the end. It is foundational to modern computing:

* Every matrix multiplication — the core of neural networks and linear algebra — is FMA operations
* Physics simulations, rendering, signal processing, scientific computing — all FMA-heavy
* Most compiler optimizations for floating-point code generate FMA instructions automatically
* Standard HPC libraries (cuBLAS, FFTW, MKL) are hand-tuned around FMA

Without functional FMA, virtually every piece of existing compute software runs at a tiny fraction of the hardware's capability.

**What NVIDIA Did**

NVIDIA throttled FMA instruction throughput on the CMP 170HX to approximately **0.39 TFLOPS** — about 1/64th of the hardware's theoretical FP32 FMA ceiling of 25 TFLOPS. This throttle is applied at the driver or firmware level. The hardware cores are physically present and functional — non-FMA FP32 operations run at 6.25 TFLOPS on the same hardware.

The throttle applies to both FP32 FMA (0.39 TFLOPS) and FP64 FMA (0.18 TFLOPS). FP16 operations are not throttled and run at their full \~42 TFLOPS. INT32 operations are not throttled and run at 12.5 TIOPS.

The reasoning is straightforward: Ethereum's Ethash mining algorithm is memory-bound and integer-heavy with almost no FMA dependency. NVIDIA throttled the one instruction type that didn't matter for mining and that mattered for everything else.

**Why Benchmark Tools Lie**

Standard GPU stress tests and benchmarks use FMA-heavy kernels to measure floating-point performance — `gpu_burn`, `clpeak`, most synthetic benchmarks. On the CMP 170HX, these tools report power consumption of 60–75W and FP32 performance of 0.39 TFLOPS, leading many users to conclude the card is broken, throttled due to thermal issues, or running at very low clocks. None of these are true.

The card's clocks are fine. The temperature is fine. The hardware is fine. The FMA instruction is the only thing being artificially restricted, and because benchmarks rely on FMA, they paint a false picture of the card's overall capability.

A more accurate stress test uses integer workloads (hashcat, for example) or memory bandwidth tests — these push the card to 160W+ and reveal the true hardware is healthy and capable.

**What This Means in Practice**

The consequence for real-world use depends entirely on what you're running:

* **Standard ML training** (PyTorch, TensorFlow FP32): heavily FMA-dependent → severely impacted
* **LLM inference at FP16**: largely FMA-independent on this precision path → surprisingly capable
* **INT8 inference**: integer operations → unthrottled
* **Memory-bound physics simulation (FDTD)**: arithmetic intensity so low FMA is irrelevant → full speed
* **Blender CUDA rendering**: FMA-heavy → poor performance
* **Hashcat password cracking**: pure integer → 43,930 MH/s, respectable

Understanding where your workload sits on this spectrum is more important than any single benchmark number.
