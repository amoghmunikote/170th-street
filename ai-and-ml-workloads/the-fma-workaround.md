# The FMA Workaround

**The FMA Workaround**

Every AI and ML workload on the CMP 170HX depends on understanding and applying the FMA workaround. This page documents exactly what it is, how it works, and how to apply it in each compute framework.

**What the Workaround Does**

The FMA throttle is enforced at the driver level when the GPU executes Fused Multiply-Add instructions. The workaround prevents FMA instructions from being generated in the first place — the compiler produces separate multiply and add operations instead. This is fractionally less numerically precise (one extra rounding step) but in practice the difference is negligible for inference workloads, and for physics simulations the community has confirmed the results are scientifically valid.

The result: FP32 performance goes from 0.39 TFLOPS to 6.25 TFLOPS — a 16× improvement confirmed by both niconiconi's 2023 benchmarks and the 2025 arXiv paper (2505.03782).

**OpenCL — The Original Workaround**

The workaround was first discovered and confirmed in OpenCL. Add these two lines at the top of any OpenCL kernel:

c

```c
#pragma OPENCL FP_CONTRACT OFF   // prevents implicit FMA from compiler optimization
#define fma(a, b, c) ((a) * (b) + (c))  // shadows the explicit fma() function
```

The first line disables the compiler's automatic FMA contraction. The second line redefines the explicit `fma()` function call as a plain multiply-add, preventing the FMA instruction path even when called directly. Together they cover both ways FMA can appear in a kernel.

This was the modification made to FluidX3D (two lines of code) that raised its performance from 2,276 MLUPs/s to 7,684 MLUPs/s — 90% of a real A100.

**CUDA — The `-fmad=false` Flag**

For CUDA code, the equivalent workaround is a compiler flag:

bash

```bash
nvcc -fmad=false your_kernel.cu
```

This instructs the NVCC compiler not to generate FMA instructions anywhere in the compilation unit. It is the cleanest solution for CUDA-based code and does not require source modifications.

For llama.cpp and other projects that use CMake:

bash

```bash
cmake -B build -DGGML_CUDA=ON \
  -DCMAKE_CUDA_FLAGS="-fmad=false" \
  -DCMAKE_CUDA_ARCHITECTURES=80
cmake --build build -j$(nproc)
```

The `-DCMAKE_CUDA_ARCHITECTURES=80` flag targets Compute Capability 8.0 specifically — the correct value for the GA100-105F-A1 die.

**PoCL — OpenCL Runtime Patch**

For workloads using the PoCL (Portable Computing Language) OpenCL runtime, a runtime-level patch can disable FMA globally without requiring source code changes to individual kernels:

bash

```bash
# Build PoCL with FMA disabled
cmake -DENABLE_FMA=OFF ..
make -j$(nproc)
sudo make install
```

This is the most transparent approach — existing OpenCL programs run without modification and benefit automatically. However it requires building PoCL from source and may affect other OpenCL workloads on the system.

**What the Workaround Cannot Fix**

The FMA workaround addresses the FP32 FMA throttle specifically. It does not:

* Restore FP64 performance to useful levels (remains \~0.094 TFLOPS without FMA)
* Improve PCIe bandwidth (still \~0.85 GB/s)
* Enable display outputs
* Fix Tensor Core throughput beyond the non-FMA FP32 ceiling
* Help workloads that are compute-bound above \~4.6 FLOPs/byte even without FMA

The workaround is most impactful for memory-bound workloads between 0.29 and 4.6 FLOPs/byte arithmetic intensity — unlocking the full 1,355 GB/s bandwidth advantage of the card for those workloads.

**Choosing the Right Approach**

| Framework              | Method                                           | Source Changes? |
| ---------------------- | ------------------------------------------------ | --------------- |
| OpenCL (manual kernel) | `#pragma OPENCL FP_CONTRACT OFF` + `#define fma` | Yes — 2 lines   |
| CUDA (manual kernel)   | `nvcc -fmad=false`                               | No              |
| llama.cpp              | CMake `-DCMAKE_CUDA_FLAGS="-fmad=false"`         | No              |
| PyTorch custom kernels | `-fmad=false` in CUDA compilation                | No              |
| PoCL (any OpenCL app)  | Build PoCL with `-DENABLE_FMA=OFF`               | No              |
| Standard PyTorch ops   | No workaround — FP16 is preferred path           | N/A             |
