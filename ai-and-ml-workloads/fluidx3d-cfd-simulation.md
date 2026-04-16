# FluidX3D — CFD Simulation

**FluidX3D — CFD Simulation**

FluidX3D is the benchmark application that most dramatically demonstrates the CMP 170HX's capabilities after applying the FMA workaround. It is a Lattice Boltzmann Method (LBM) Computational Fluid Dynamics solver written in OpenCL by Dr. Moritz Lehmann. It was the first real-world application in which the CMP 170HX was confirmed to match A100-class performance.

**Why FluidX3D Works So Well**

LBM is a memory-bandwidth-bound algorithm. Each simulation step reads and writes large amounts of fluid state data but performs a relatively small number of arithmetic operations per byte — arithmetic intensity of approximately 1.7 FLOPs/byte for the FP32/FP32 mode. Without the FMA workaround, this sits above the FMA ridge point (0.29 FLOPs/byte) and the card is compute-bound, achieving only 2,276 MLUPs/s. With the FMA workaround, 1.7 FLOPs/byte falls well below the no-FMA ridge point (4.6 FLOPs/byte), and the card becomes memory-bound — delivering 7,684 MLUPs/s, which is 90% of a real A100 PCIe 40GB.

**Results Summary**

| Mode                           | MLUPs/s | Memory Bandwidth Used | vs A100 PCIe 40GB | vs RTX 4090 |
| ------------------------------ | ------- | --------------------- | ----------------- | ----------- |
| FP32/FP32 (standard, with FMA) | 2,276   | 348 GB/s              | \~14%             | \~20%       |
| FP32/FP32 (no-FMA workaround)  | 7,684   | 1,175 GB/s            | **90%**           | **69%**     |
| FP32/FP16S (no-FMA workaround) | 12,392  | 954 GB/s              | **80%**           | **112%**    |

In FP32/FP16S mode with the FMA workaround, the CMP 170HX is **10% faster than the RTX 4090** for this workload. At its current price of \~$250, this represents extraordinary value for CFD simulation.

**Installation**

bash

```bash
git clone https://github.com/ProjectPhysX/FluidX3D
cd FluidX3D
```

FluidX3D uses OpenCL. Apply the FMA workaround by editing `src/lbm.cpp`:

cpp

```cpp
// Add these two lines at the top of the OpenCL kernel string definition
"\n #pragma OPENCL FP_CONTRACT OFF"
"\n #define fma(a, b, c) ((a) * (b) + (c))"
```

Then build:

bash

```bash
make -j$(nproc)
```

**Running a Benchmark**

bash

```bash
# Run the default benchmark
./FluidX3D
```

The output will show real-time MLUPs/s, memory bandwidth utilization, and steps per second. Monitor the bandwidth figure — with the FMA workaround applied correctly you should see bandwidth above 1,000 GB/s.

**Expected Output (FP32/FP32, no-FMA)**

```
|---------.-------'-----.-----------.-------------------.---------------------|
| MLUPs   | Bandwidth   | Steps/s   | Current Step      | Time Remaining      |
| 7681    | 1175 GB/s   | 458       | 9985  50%         | 0s                  |
|---------.-------------'-----------'-------------------'---------------------|
| Info: Peak MLUPs/s = 7684
```

**Physics Accuracy Note**

Dr. Moritz Lehmann has confirmed that disabling FMA does not meaningfully affect simulation accuracy for LBM. The dominant source of error in LBM simulations is discretization error — the finite grid resolution — not floating-point rounding error from the missing FMA fusion step. Results obtained with the no-FMA workaround are scientifically valid.
