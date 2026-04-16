# Memory Bandwidth

**Memory Bandwidth**

The CMP 170HX's memory bandwidth is its most valuable and most uncompromised asset. At 1,355 GB/s real-world measured (1,493 GB/s theoretical), it is competitive with the A100 PCIe 40GB (1,555 GB/s theoretical) and approximately 1.5× faster than the RTX 4090 (\~1,008 GB/s). This bandwidth is completely unlocked with no throttling of any kind.

***

**Why HBM2e Is So Fast**

The CMP 170HX uses HBM2e (High Bandwidth Memory 2e) on a 4,096-bit memory bus. For comparison, the RTX 4090 uses GDDR6X on a 384-bit bus — the CMP's bus is over 10× wider. GDDR6X achieves high bandwidth through very fast clock speeds; HBM achieves it through massive parallelism. The result is similar peak bandwidth but HBM has dramatically better energy efficiency and lower latency for sequential access patterns common in scientific computing.

**Measured Bandwidth by Access Pattern**

```
float   (32-bit, scalar):   1,165.79 GB/s
float2  (64-bit, vector):   1,269.69 GB/s
float4  (128-bit, vector):  1,343.50 GB/s
float8  (256-bit, vector):  1,355.40 GB/s  ← peak
float16 (512-bit, vector):  1,350.14 GB/s
```

Peak bandwidth is achieved with float8 (256-bit) access patterns. This is important for optimizing kernels — code that uses 256-bit memory operations will extract the most bandwidth from the HBM2e.

**Bandwidth Comparison**

| GPU               | Memory Type | Bus Width | Theoretical BW | Real-World BW |
| ----------------- | ----------- | --------- | -------------- | ------------- |
| CMP 170HX         | HBM2e       | 4,096-bit | 1,493 GB/s     | \~1,355 GB/s  |
| A100 PCIe 40GB    | HBM2e       | 5,120-bit | 1,555 GB/s     | \~1,400 GB/s  |
| A100 SXM4 40GB    | HBM2e       | 5,120-bit | 1,555 GB/s     | \~1,400 GB/s  |
| RTX 4090          | GDDR6X      | 384-bit   | 1,008 GB/s     | \~900 GB/s    |
| RTX 3090          | GDDR6X      | 384-bit   | 936 GB/s       | \~840 GB/s    |
| Radeon VII / MI50 | HBM2        | 4,096-bit | 1,024 GB/s     | \~830 GB/s    |
| RTX 3080          | GDDR6X      | 320-bit   | 760 GB/s       | \~680 GB/s    |

The CMP 170HX's memory bandwidth beats every consumer GPU currently available. It is surpassed only by the A100 itself (and newer datacenter cards like H100/H200). At its current market price of $200–400, nothing else comes close on a bandwidth-per-dollar basis.

**When Bandwidth Is What Matters**

Memory bandwidth is the limiting factor for workloads with low arithmetic intensity — where the algorithm reads data from memory, performs a small number of operations per byte read, then moves on. These workloads include:

* FDTD electromagnetic simulation (arithmetic intensity \~0.25 FLOPs/byte)
* Lattice Boltzmann Method CFD (with FMA disabled, intensity \~1.7 FLOPs/byte)
* Sparse matrix operations
* LLM token generation (decode phase) — memory bandwidth bound at batch size 1
* Many data analytics and streaming computation workloads

For these use cases, the CMP 170HX's bandwidth advantage over even high-end consumer GPUs translates directly to proportional performance gains — regardless of the FMA throttle.

**The Bandwidth-to-FMA Imbalance**

The CMP 170HX has an extreme imbalance between its memory bandwidth (1,355 GB/s) and its FMA compute (0.39 TFLOPS). The ridge point — the arithmetic intensity at which a workload transitions from memory-bound to compute-bound — is approximately 0.29 FLOPs/byte with FMA, or 4.6 FLOPs/byte without FMA.

This means:

* Any algorithm with arithmetic intensity below \~0.29 FLOPs/byte runs at full memory bandwidth speed regardless of FMA throttling
* Any algorithm with intensity between 0.29 and 4.6 FLOPs/byte needs the FMA workaround to benefit from the hardware's potential
* Algorithms above \~4.6 FLOPs/byte are compute-bound and the card will underperform regardless

<figure><img src="https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/roofline-cmp170hx.svg" alt=""><figcaption></figcaption></figure>
