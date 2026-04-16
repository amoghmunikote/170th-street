# Open Research Problems

**Open Research Problems**

This page documents the active open problems in CMP 170HX security and firmware research. These are the unsolved challenges that, if cracked, would be the most impactful contributions to the project.

**Problem 1 — Ampere Falcon Signature Bypass**

**Impact if solved:** Full firmware modification capability. Would enable removing the FMA throttle, PCIe speed lock, and any other firmware-enforced restriction directly.

**Current state:** No public progress. The Turing bypass (OMGVflash) exploited a specific vulnerability in Falcon's authentication path for that generation. Ampere uses a certificate chain validation approach that the same techniques do not apply to. Veii (OMGVflash author) has indicated Ampere requires a fundamentally different approach and that "going around it is pretty pretty impossible" without knowing how to reprogram Falcon itself.

**Research directions:**

* Analysis of Falcon microcode for Ampere — NVIDIA has published some Falcon documentation but key implementation details are proprietary
* Side-channel analysis of the signature verification process
* Review of NVIDIA security bulletins for Ampere firmware vulnerabilities (CVE database)
* Hardware-level analysis (requires decapsulation of the GPU package — extreme difficulty)

**Problem 2 — FMA Throttle Mechanism Identification**

**Impact if solved:** Better understanding of exactly where and how the throttle is enforced, potentially revealing alternative bypass paths.

**Current state:** The throttle is known to be driver or firmware enforced (not silicon fuse), confirmed by the fact that non-FMA FP32 runs at full speed. Whether it is enforced in the VBIOS, in the NVIDIA kernel driver, or in Falcon microcode that runs at boot time is not definitively established.

**Research directions:**

* Differential analysis of NVIDIA driver binaries across versions — does the throttle change across driver versions?
* Analysis of the NVIDIA Linux open-source kernel module (partially released by NVIDIA in 2022) for any per-device instruction throttling logic
* Testing whether the datacenter driver (used for A100) applied to the CMP 170HX produces different FMA behavior

**Problem 3 — PCIe Gen 1 Lock — Runtime Override**

**Impact if solved:** 4× improvement in PCIe bandwidth (after capacitor mod) or 64× if Gen 4 can be reached.

**Current state:** The lock is enforced in firmware during PCIe link training. No runtime override is known. The NVIDIA System Management Interface (nvidia-smi) does not expose PCIe speed as a configurable parameter.

**Research directions:**

* Direct PCIe register access via `/dev/mem` or similar to attempt speed renegotiation after boot
* Analysis of the PCIe link training sequence in the VBIOS
* Testing whether any version of the datacenter driver exposes PCIe speed configuration for the GA100

**Problem 4 — Driver Compatibility with Newer Drivers**

**Impact if solved:** Access to newer CUDA features, better software compatibility, security fixes.

**Current state:** The dartraiden patcher has shown instability with newer NVIDIA driver versions on the CMP 170HX. Driver 471.50 (Windows) and equivalent Linux datacenter drivers are the most stable. Newer drivers (576.80+) have reported issues including misidentification as a "3080 Laptop GPU" and error code 43.

**Research directions:**

* Community testing of intermediate driver versions to identify when the regression was introduced
* Contribution to dartraiden/NVIDIA-patcher with updated patches for newer driver versions
* Investigation of whether the misidentification can be corrected via INF/device ID patches

**Problem 5 — Tensor Core Full Activation**

**Impact if solved:** Access to full \~312 TFLOPS FP16 Tensor Core throughput versus the current hardware-gated 6.3 TFLOPS — a 49× improvement for Tensor Core workloads.

**Current state — now significantly better understood**

Microbenchmarking research (Zenodo 19002983, March 2026) has definitively characterized the Tensor Core throttle mechanism. Key findings:

* A single MMA instruction executes with a **256 fixed-cycle latency** that cannot be hidden by ILP or pipeline overlap
* Only **4 warps per SM** can simultaneously issue Tensor Core instructions
* These two constraints together produce exactly the measured 6.3 TFLOPS — the theoretical model closes perfectly
* The mechanism is **dispatch-level hardware gating** — confirmed by ILP scaling, warp scaling, dependency chain, and cross-pipeline interference experiments
* The Tensor Core execution units are physically intact — it is the dispatch gate that limits throughput, not damaged or disabled hardware

**Why this is harder to bypass than the FMA throttle**

The FMA throttle was bypassed at the compiler level — by preventing FMA instructions from being generated, the throttled instruction path is simply never invoked. The Tensor Core dispatch gate operates differently: the execution units are present, the instructions are accepted, but the hardware gate limits how many warps can issue them and enforces a fixed latency regardless of pipeline state. There is no compiler-level equivalent of `-fmad=false` for a dispatch-level gate. Bypassing it likely requires either firmware modification (to remove the gate) or a microarchitectural technique that has not yet been identified.

**Research directions**

* Analysis of whether the dispatch gate is enforced in Falcon microcode loaded at boot, in the VBIOS power table, or in a hardwired GPU register — the paper identifies it as hardware gating but the exact enforcement point in the boot chain is not established
* Testing whether any version of the A100 datacenter driver changes the warp issue limit or MMA latency on the CMP 170HX
* PTX-level experiments testing alternative Tensor Core instruction forms (WMMA vs MMA vs warp-level primitives) to see if any path avoids the gate
* Cross-referencing with the Ampere Falcon bypass problem — if custom firmware can be loaded, the dispatch gate configuration may be modifiable

**Contributing to Research**

The most effective contributions right now are:

* **Binary analysis skills:** NVIDIA driver and firmware binary analysis to locate throttle logic
* **Falcon expertise:** Anyone with Falcon microarchitecture knowledge or prior GPU firmware security research experience
* **Systematic testing:** Methodical testing of driver versions, CUDA versions, and compute modes to narrow down where restrictions are enforced
* **Hardware access:** Anyone with both a CMP 170HX and a working A100 can perform comparative analysis that most researchers cannot

All findings should be documented publicly — in issues on dartraiden/NVIDIA-patcher, in the 170th Street community, or as standalone publications. The FMA workaround itself was discovered by a single community researcher posting a code diff to a GitHub issue. The next breakthrough could come the same way.
