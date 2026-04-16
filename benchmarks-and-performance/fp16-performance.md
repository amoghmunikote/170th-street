# FP16 Performance

**FP16 Performance**

FP16 (half-precision floating-point) is the second most important compute capability of the CMP 170HX, after memory bandwidth. At approximately 42 TFLOPS, it is unthrottled and represents the primary path for AI inference workloads on this card.

**mixbench Results**

```
FP16 (half precision, CUDA/SYCL):
  Peak throughput: ~41,869 GFLOPS (~42 TFLOPS)
```

Note: FP16 is inaccessible via NVIDIA's OpenCL runtime on this card ("No half precision support" in clpeak). It is accessible via CUDA and SYCL.

**FP16 Comparison**

| GPU            | FP16 Performance               |
| -------------- | ------------------------------ |
| A100 PCIe 40GB | 312 TFLOPS (with Tensor Cores) |
| RTX 4090       | 165 TFLOPS                     |
| RTX 3090       | 71 TFLOPS                      |
| RTX 3080       | 59.4 TFLOPS                    |
| **CMP 170HX**  | **\~42 TFLOPS**                |
| RTX 3060       | 25.9 TFLOPS                    |
| RTX 2060       | 29 TFLOPS                      |

The CMP 170HX's FP16 performance is comparable to a mid-range consumer GPU from 2020–2021. It is substantially below the A100's Tensor Core-accelerated FP16, but for inference workloads it is workable.

**Why FP16 Matters for This Card**

FP16 is the dominant precision for modern AI inference:

* Most LLM inference frameworks default to FP16 weights
* PyTorch's recommended path for GPU inference is FP16
* FP16 uses half the VRAM of FP32, enabling larger models in the 8GB budget
* The memory bandwidth advantage of the CMP 170HX partially compensates for lower TFLOPS versus newer cards on memory-bound inference tasks

The arXiv paper on the CMP 170HX (2505.03782) confirmed that LLM inference at FP16 precision shows over 3× improvement compared to FP32 inference on this card, because the FP16 path avoids the FMA throttle.

**Tensor Core Status**

The CMP 170HX has 280 third-generation Tensor Cores (4 per SM × 70 SMs). The gpu\_burn test using `CUBLAS_TENSOR_OP_MATH` achieved \~6.2 TFLOPS — the same as non-FMA FP32. This suggests the Tensor Cores are either non-functional or the FLOPS ceiling is still being enforced even on the Tensor Core path. The Tensor Core status requires further investigation and community testing to fully characterize.
