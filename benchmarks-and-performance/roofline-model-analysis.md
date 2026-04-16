# Roofline Model Analysis

**Roofline Model Analysis**

The roofline model is the single most useful framework for understanding what the CMP 170HX can and cannot do. It explains why some workloads are barely affected by NVIDIA's restrictions while others are crippled by them.

<figure><img src="https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/roofline-cmp170hx.svg" alt=""><figcaption></figcaption></figure>

**What the Roofline Model Is**

The roofline model is a graphical performance analysis tool. It shows the maximum achievable performance of a given program on a given piece of hardware, based on two factors: the hardware's peak compute throughput (FLOPS) and its peak memory bandwidth (GB/s), and the program's arithmetic intensity (how many floating-point operations are performed per byte of memory accessed).

A program with low arithmetic intensity is **memory-bound** — it can't go faster than the bandwidth limit. A program with high arithmetic intensity is **compute-bound** — it can't go faster than the FLOPS limit. The point where these two limits intersect is called the ridge point.

**The CMP 170HX's Two Rooflines**

The CMP 170HX has two relevant compute ceilings:

_With FMA (standard workloads):_

* Compute ceiling: 0.39 TFLOPS
* Memory bandwidth: 1,355 GB/s
* Ridge point: \~0.29 FLOPs/byte

_Without FMA (patched workloads):_

* Compute ceiling: 6.25 TFLOPS
* Memory bandwidth: 1,355 GB/s
* Ridge point: \~4.6 FLOPs/byte

**What This Means**

Any algorithm with arithmetic intensity below 0.29 FLOPs/byte runs at full memory bandwidth speed regardless of FMA throttling. This is a very low threshold — many physics simulations fall here by design.

Any algorithm with intensity between 0.29 and 4.6 FLOPs/byte runs compute-bound without the FMA workaround (very slowly), but memory-bound with it (fast).

Any algorithm above 4.6 FLOPs/byte is compute-bound even with the FMA workaround, and the card will not perform well.

**Real-World Application Data Points**

_FDTD Electromagnetic Simulation:_

* Arithmetic intensity: 0.25 FLOPs/byte
* Below the FMA ridge point → FMA throttling is **irrelevant**
* Achieved: 10,110 MC/s (megacells per second), 181 GFLOPS
* Result: On par with a real A100 for this workload

_FluidX3D Lattice Boltzmann (with FMA — standard):_

* Arithmetic intensity: \~1.7 FLOPs/byte
* Above the FMA ridge point → compute-bound
* Achieved: 2,276 MLUPs/s, 348 GB/s memory utilization
* Similar to an RTX 3060

_FluidX3D Lattice Boltzmann (without FMA — patched):_

* Same arithmetic intensity, now below the no-FMA ridge point
* Achieved: 7,684 MLUPs/s, 1,175 GB/s memory utilization
* **90% of a real A100's performance**

_FluidX3D FP32/FP16S (without FMA):_

* Mixed precision reduces memory traffic, increases effective intensity
* Achieved: 12,392 MLUPs/s, 954 GB/s
* **80% of A100, 10% faster than RTX 4090**

**The Practical Conclusion**

The roofline model makes it clear that the CMP 170HX is not uniformly bad — it is specifically bad for compute-bound FMA workloads, which is most common software. For workloads that are memory-bound by nature (many physics simulations, LLM decode, streaming compute), the FMA throttle is irrelevant and the card's bandwidth advantage is the only thing that matters. For workloads in the middle range, the FMA workaround unlocks performance close to a real A100.

Choosing workloads intelligently — or modifying them to avoid FMA — is the key to getting value from this card.
