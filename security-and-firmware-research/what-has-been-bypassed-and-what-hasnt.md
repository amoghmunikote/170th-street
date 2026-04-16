# What Has Been Bypassed — and What Hasn't

**What Has Been Bypassed — and What Hasn't**

This page documents the current state of NVIDIA firmware security research as it applies to the CMP 170HX, separating confirmed achievements from ongoing open problems.

**Confirmed Bypasses**

**FMA Throttle — Bypassed at the Application Layer**

The FMA throttle is the most impactful restriction and it has been effectively bypassed — not at the firmware level, but at the application level. By preventing FMA instructions from being generated in the first place (via `#pragma OPENCL FP_CONTRACT OFF`, `-fmad=false`, or PoCL patching), software can avoid the throttled instruction entirely. The hardware executes unthrottled non-FMA FP32 at 6.25 TFLOPS, delivering a 16× improvement over the throttled 0.39 TFLOPS.

This bypass was first confirmed publicly in December 2023 by community researcher jetcat8848 in the dartraiden/NVIDIA-patcher GitHub repository (Issue #73), and validated by the 2025 arXiv paper (2505.03782). It is now documented, reproducible, and widely used.

**Important:** this is not a firmware bypass — it is a software-level workaround that avoids the throttled instruction rather than removing the throttle. The firmware restriction remains in place; the workaround simply routes around it.

**Driver-Level Patches — NVIDIA-patcher (dartraiden)**

The [dartraiden/NVIDIA-patcher](https://github.com/dartraiden/NVIDIA-patcher) project patches the NVIDIA Linux and Windows driver to add 3D acceleration support for mining cards including the CMP 170HX. The standard NVIDIA driver refuses to enable 3D acceleration (CUDA, OpenCL, Vulkan) for mining-designated cards. The patcher modifies the driver binary to recognize these cards and enable full compute support.

This is a driver-level modification, not a firmware modification. It does not touch the VBIOS and does not require bypassing Falcon. It patches the userspace/kernel driver code that checks device IDs before enabling features.

The patcher supports the CMP 170HX explicitly and is maintained through regular driver version updates. Note: newer driver versions (576.80+) have shown issues with the CMP 170HX — driver version 471.50 (Windows) and the equivalent Linux datacenter driver are the most stable as of mid-2025.

**Cross-Flashing Between Signed VBIOSes**

OMGVflash and NVflashk enable flashing of any signed, valid NVIDIA VBIOS onto any compatible card, bypassing the subsystem ID checks that standard nvflash enforces. This means you can flash a different official NVIDIA-signed VBIOS (with a different power table, clock table, etc.) onto the CMP 170HX as long as the device IDs match. This is how we flashed `92.00.6D.00.0A` onto our card in place of the original `92.00.67.00.01`.

What it cannot do: flash a modified, unsigned, or custom-authored VBIOS. The firmware must still pass Falcon's signature validation to boot.

**What Has Not Been Bypassed**

**Ampere VBIOS Signature Check — Not Bypassed**

No publicly known method exists to flash modified unsigned firmware onto an Ampere GPU and have it boot. OMGVflash's Falcon access breakthrough was specific to Turing. The developer Veii explicitly stated that Ampere's certificate chain validation is a distinct, stronger mechanism that has not been cracked. Flashing a byte-modified VBIOS to the CMP 170HX via any method including SPI programmer results in a non-booting card.

**PCIe Gen 1 Speed Lock — Not Bypassed at Firmware Level**

The PCIe speed lock is enforced in the signed VBIOS. Without a firmware modification, the card cannot negotiate above Gen 1 speed regardless of host slot capabilities.

Every known software-only runtime path has been independently tested and eliminated — `setpci` write to LnkCap2 is rejected, MMIO BAR0 access to the link registers is PROT-protected on GA100, upstream root port retrains don't change what the endpoint advertises, and the Falcon PRIV register that manipulates the speed bits is not host-addressable. The full test methodology and results are documented in [Runtime PCIe Speed Unlock — Attempted & Failed](runtime-pcie-unlock-attempt.md).

**FMA Throttle at Firmware Level — Not Bypassed**

The FMA throttle persists in the firmware. The application-layer workaround routes around it but does not remove it. Any workload that cannot be modified to avoid FMA instructions (standard binaries, closed-source software) will still encounter throttled FP32 performance.

**NVLink — Not Enabled**

Even if the hardware were fully populated (see the NVLink Population page), whether the firmware would activate it is unknown and currently unverifiable without a firmware modification capability.
