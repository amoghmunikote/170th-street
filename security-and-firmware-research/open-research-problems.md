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

**Impact if solved:** Potential access to \~312 TFLOPS FP16 Tensor Core throughput (the A100's full figure) versus the current \~6.2 TFLOPS observed.

**Current state:** gpu\_burn with Tensor Cores enabled achieves only \~6.2 TFLOPS — the same as non-FMA FP32 — suggesting either the Tensor Core path is throttled by the same mechanism as FMA, or the Tensor Cores are being disabled entirely. Whether this is a firmware-level restriction or a driver-level restriction is unknown.

**Research directions:**

* Testing with different cuBLAS configurations and CUDA compute modes
* Direct PTX assembly testing of Tensor Core instructions (WMMA operations) to see if the hardware executes them at all
* Analysis of NVIDIA's Ampere whitepaper for Tensor Core architecture details that might indicate how throttling is implemented

**Contributing to Research**

The most effective contributions right now are:

* **Binary analysis skills:** NVIDIA driver and firmware binary analysis to locate throttle logic
* **Falcon expertise:** Anyone with Falcon microarchitecture knowledge or prior GPU firmware security research experience
* **Systematic testing:** Methodical testing of driver versions, CUDA versions, and compute modes to narrow down where restrictions are enforced
* **Hardware access:** Anyone with both a CMP 170HX and a working A100 can perform comparative analysis that most researchers cannot

All findings should be documented publicly — in issues on dartraiden/NVIDIA-patcher, in the 170th Street community, or as standalone publications. The FMA workaround itself was discovered by a single community researcher posting a code diff to a GitHub issue. The next breakthrough could come the same way.
