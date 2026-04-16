# Comparison Table

**Comparison Table**

A comprehensive benchmark comparison of the CMP 170HX against relevant GPUs.

**Compute Performance**

| Metric      | CMP 170HX   | A100 PCIe 40GB | RTX 4090    | RTX 3090    | RTX 3080    | Radeon VII  |
| ----------- | ----------- | -------------- | ----------- | ----------- | ----------- | ----------- |
| FP32 FMA    | 0.39 TFLOPS | 19.5 TFLOPS    | 82.6 TFLOPS | 35.6 TFLOPS | 29.8 TFLOPS | 13.4 TFLOPS |
| FP32 no-FMA | 6.25 TFLOPS | 19.5 TFLOPS    | N/A         | N/A         | N/A         | N/A         |
| FP16        | 42 TFLOPS   | 312 TFLOPS     | 165 TFLOPS  | 71 TFLOPS   | 59.4 TFLOPS | 26.8 TFLOPS |
| INT32       | 12.5 TIOPS  | 20 TIOPS       | 82.6 TOPS   | 35.6 TOPS   | 29.8 TOPS   | 13.4 TOPS   |
| Memory BW   | 1,355 GB/s  | \~1,400 GB/s   | \~900 GB/s  | \~840 GB/s  | \~680 GB/s  | \~830 GB/s  |
| VRAM        | 8 GB        | 40 GB          | 24 GB       | 24 GB       | 10 GB       | 16 GB       |
| PCIe BW     | 0.85 GB/s   | 64 GB/s        | 64 GB/s     | 64 GB/s     | 64 GB/s     | 64 GB/s     |

**Real-World Benchmark Results**

| Benchmark                    | CMP 170HX         | A100 PCIe 40GB      | RTX 4090             | RTX 3080            |
| ---------------------------- | ----------------- | ------------------- | -------------------- | ------------------- |
| clpeak memory BW             | 1,355 GB/s        | \~1,400 GB/s        | \~900 GB/s           | \~680 GB/s          |
| Hashcat MD5                  | 43,930 MH/s       | \~64,900 MH/s       | \~91,000 MH/s        | \~54,000 MH/s       |
| FluidX3D FP32 (standard)     | 2,276 MLUPs       | —                   | \~11,091 MLUPs       | —                   |
| FluidX3D FP32 (no-FMA)       | 7,684 MLUPs       | \~16,035 MLUPs      | \~11,091 MLUPs       | —                   |
| FluidX3D FP32/FP16S (no-FMA) | 12,392 MLUPs      | \~16,035 MLUPs      | \~11,091 MLUPs       | —                   |
| Blender monster (CUDA)       | \~570 samples/min | \~3,647 samples/min | \~7,000+ samples/min | \~3,500 samples/min |

**Price and Value**

| GPU            | Current Price | Memory BW per $   | Notes                        |
| -------------- | ------------- | ----------------- | ---------------------------- |
| CMP 170HX      | \~$250        | \~5.4 GB/s per $  | Throttled for most workloads |
| A100 PCIe 40GB | \~$3,000      | \~0.47 GB/s per $ | Full performance             |
| RTX 4090       | \~$1,600      | \~0.56 GB/s per $ | Best consumer card           |
| RTX 3090       | \~$600        | \~1.4 GB/s per $  | Good balance                 |
| Radeon VII     | \~$150        | \~5.5 GB/s per $  | No FMA throttle, AMD         |

On pure memory bandwidth per dollar, the CMP 170HX and the secondhand Radeon VII are in a class of their own. The critical difference is that the Radeon VII has no FMA throttle — making it a better general-purpose choice if your workload isn't specifically memory-bound enough to bypass the CMP 170HX's restrictions.

**When to Choose the CMP 170HX**

✅ Your workload has arithmetic intensity below \~0.29 FLOPs/byte (pure memory-bound)&#x20;

✅ You can modify your code to avoid FMA instructions&#x20;

✅ You primarily run FP16 or INT8 inference&#x20;

✅ You're doing integer-heavy compute&#x20;

✅ Maximum memory bandwidth per dollar is your primary metric&#x20;

✅ You're running memory-bound physics simulations (FDTD, LBM with FMA patch)

**When to Choose Something Else**

❌ Standard ML training with FP32 or BF16&#x20;

❌ Blender or other FMA-heavy rendering&#x20;

❌ You need more than 8GB VRAM&#x20;

❌ You need fast CPU-GPU data transfers (PCIe bottleneck)&#x20;

❌ You need general-purpose FP32 compute without code modifications&#x20;

❌ You want something that just works out of the box with all software
