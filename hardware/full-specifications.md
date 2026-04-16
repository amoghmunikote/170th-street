# Full Specifications

This page documents every known specification of the NVIDIA CMP 170HX in one place. Where figures differ between sources, the most accurate measured values are used.

<figure><img src="https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-teardown-0.jpg" alt=""><figcaption></figcaption></figure>

**General**

| Property              | Value                                     |
| --------------------- | ----------------------------------------- |
| Model                 | NVIDIA CMP 170HX                          |
| GPU Die               | GA100-105F-A1                             |
| Architecture          | NVIDIA Ampere                             |
| Manufacturing Process | TSMC 7nm N7 FinFET                        |
| Transistors           | 54.2 billion                              |
| Die Size              | 826 mm²                                   |
| Release Date          | September 1, 2021                         |
| Original MSRP         | $4,299 USD                                |
| Current Used Price    | $200–400 USD                              |
| Part Number           | 900-11001-0105-000                        |
| Form Factor           | Dual-slot PCIe add-in card                |
| Cooling               | Passive (server chassis airflow required) |
| Display Outputs       | None                                      |

**Compute**

| Property                  | Value                          |
| ------------------------- | ------------------------------ |
| Streaming Multiprocessors | 70                             |
| CUDA Cores                | 4,480 (64 per SM)              |
| Tensor Cores              | 280 (4 per SM, 3rd generation) |
| Texture Units (TMUs)      | 280                            |
| ROPs                      | 128                            |
| CUDA Compute Capability   | 8.0                            |
| Base Clock                | 1,140 MHz                      |
| Boost Clock               | 1,410 MHz                      |
| Max Clock (observed)      | 1,695 MHz                      |
| L1 Cache                  | 192 KB per SM                  |
| L2 Cache                  | 32,768 KB (32 MB)              |

**Memory**

| Property                       | Value                              |
| ------------------------------ | ---------------------------------- |
| Memory Type                    | HBM2e                              |
| Memory Capacity                | 8 GB                               |
| Memory Bus Width               | 4,096-bit                          |
| Memory Clock                   | 1,458 MHz (729 MHz effective base) |
| Memory Bandwidth (theoretical) | 1,493 GB/s                         |
| Memory Bandwidth (measured)    | \~1,355 GB/s (real-world clpeak)   |
| Memory Stacks                  | 2 HBM2e stacks                     |
| ECC                            | Disabled                           |
| Resizable BAR                  | Present but limited to 64 MiB      |

**Performance (Theoretical vs Actual)**

| Metric                        | Theoretical  | Actual (measured)           |
| ----------------------------- | ------------ | --------------------------- |
| FP32 (with FMA)               | 25.27 TFLOPS | **0.39 TFLOPS** (throttled) |
| FP32 (without FMA)            | —            | 6.25 TFLOPS                 |
| FP16                          | \~42 TFLOPS  | \~42 TFLOPS (unthrottled)   |
| FP64 (with FMA)               | —            | 0.18 TFLOPS (throttled)     |
| FP64 (without FMA)            | —            | 0.094 TFLOPS                |
| INT32                         | —            | 12.5 TIOPS (unthrottled)    |
| Tensor Core (TF32 via cuBLAS) | —            | \~6.2 TFLOPS                |
| Memory Bandwidth              | 1,493 GB/s   | \~1,355 GB/s                |

**Connectivity**

| Property                    | Value                                      |
| --------------------------- | ------------------------------------------ |
| PCIe Interface (physical)   | x16 slot                                   |
| PCIe Interface (electrical) | Gen 1 x4 (firmware locked)                 |
| PCIe Bandwidth              | \~1 GB/s (0.85 GB/s measured)              |
| NVLink                      | Gold fingers present, all ICs unpopulated  |
| Power Connector             | 1× 8-pin CPU power connector (via adapter) |

**Power**

| Property                             | Value      |
| ------------------------------------ | ---------- |
| TDP (default)                        | 250W       |
| Max Power Limit (software)           | 300W       |
| Idle Power (typical)                 | \~30–40W   |
| Load Power (FMA-throttled workload)  | \~75–100W  |
| Load Power (integer/memory workload) | \~160–180W |
| Load Power (non-FMA FP32, FluidX3D)  | \~180W     |

**API Support**

| API           | Supported                      |
| ------------- | ------------------------------ |
| CUDA          | ✅ Yes (Compute Capability 8.0) |
| OpenCL        | ✅ Yes (3.0)                    |
| DirectX       | ❌ No                           |
| Vulkan        | ❌ No                           |
| OpenGL        | ❌ No                           |
| NvEnc / NvDec | ❌ No                           |

<figure><img src="https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-pcb-1.jpg" alt=""><figcaption></figcaption></figure>

**Notes on Specification Discrepancies**

Various third-party spec databases list conflicting values for the CMP 170HX — some list 16GB or 10GB of memory, others list incorrect memory bus widths. The correct values for the original 2021 NVIDIA-manufactured card are 8GB HBM2e on a 4096-bit bus as listed above. The 10GB and 16GB figures appear to be errors in automated spec databases that have propagated widely. Always verify using `nvidia-smi` on your own card.
