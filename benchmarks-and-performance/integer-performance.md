# Integer Performance

**Integer Performance**

Integer (INT32) operations are completely unthrottled on the CMP 170HX. At 12.5 TIOPS, this is the card's second strongest compute capability after memory bandwidth, and it was clearly preserved because Ethereum's Ethash algorithm is integer-based.

**clpeak Results**

```
Integer compute (GIOPS)
  int   : 12,499.07
  int2  : 12,518.02
  int4  : 12,494.16
  int8  : 12,547.55
  int16 : 12,540.22
```

**Integer Comparison**

| GPU            | INT32 Performance |
| -------------- | ----------------- |
| A100 PCIe 40GB | 20 TIOPS          |
| RTX 3090       | 35.6 TIOPS        |
| RTX 3080       | 29.8 TIOPS        |
| RTX 2080 Ti    | 15.6 TIOPS        |
| **CMP 170HX**  | **12.5 TIOPS**    |
| RTX 3060       | 12.7 TIOPS        |

The CMP 170HX sits roughly on par with an RTX 3060 for integer compute — reasonable for a compute card at this price point, and far better than its FMA-throttled FP32 performance suggests.

**Hashcat (MD5)**

```
Hash-Mode 0 (MD5)
Speed: 43,930 MH/s @ Accel:64 Loops:512 Thr:1024 Vec:1
```

For context:

* NVIDIA A100: \~64,900 MH/s
* RTX 3080: \~54,000 MH/s
* **CMP 170HX: \~43,930 MH/s**
* RTX 3060: \~26,000 MH/s

The CMP 170HX is about 67% as fast as an A100 for MD5 hashing, reflecting its 70 active SMs versus the A100's 108. It is slower than the RTX 3080 for this task, which makes sense — the RTX 3080 has more INT32 throughput and faster PCIe which helps hashcat's CPU-GPU data pipeline.

**What Integer Performance Is Good For**

* Password cracking (hashcat)
* Cryptocurrency mining (the original use case — pure integer)
* INT8 inference for quantized neural network models
* Integer-heavy data processing pipelines
* Any workload that can be structured as integer operations rather than floating-point

INT8 inference deserves particular attention. Many modern AI inference frameworks quantize models to INT8 for deployment, and the CMP 170HX's unthrottled integer throughput combined with its exceptional memory bandwidth makes it a viable inference accelerator for quantized models that fit within 8GB.
