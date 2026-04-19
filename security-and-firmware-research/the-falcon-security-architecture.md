# The Falcon Security Architecture

**The Falcon Security Architecture**

Understanding why the CMP 170HX cannot be fully unlocked via VBIOS modification requires understanding the NVIDIA Falcon security processor — the on-die security subsystem that enforces firmware authenticity on every modern NVIDIA GPU.

**What Falcon Is**

Falcon is NVIDIA's custom embedded microprocessor architecture, present in all NVIDIA GPUs since the Maxwell generation (GeForce 900-series, 2014). Multiple independent Falcon instances run simultaneously on each GPU, managing different subsystems: power management, display engine, video decode, and — most importantly for our purposes — secure boot and firmware authentication.

Falcon supports three operating modes:

**Non-Secure (NS):** The baseline mode. Certain registers are inaccessible and physical memory access may be disabled. This is the only mode available if the microcode running on Falcon has not been cryptographically signed by NVIDIA.

**Light Secure (LS):** More privileges than NS but fewer than HS. Some internal state is visible to host software for debugging. Can only be entered from HS mode — NVIDIA-signed HS microcode must explicitly enable it.

**Heavy Secure (HS):** The highest privilege mode. When running in HS, Falcon is a complete black box — no external software can read or write any Falcon internal state or registers. The GPU hardware itself enforces this isolation. The only way to enter HS mode is by loading microcode cryptographically signed by NVIDIA. The signature is validated in hardware before HS privileges are granted.

**How Firmware Authentication Works**

When a GPU boots, the Falcon processor loads the VBIOS from the EEPROM chip. Before executing it, Falcon validates a cryptographic signature embedded in the firmware image against NVIDIA's public key, which is stored in hardware. If the signature is valid, the firmware is authenticated and the GPU proceeds to boot normally. If the signature is invalid or absent — including if any byte of the firmware has been modified after signing — Falcon rejects the firmware and the GPU will not boot.

This means: **any modification to the VBIOS, even a single byte change, invalidates the signature and renders the modified firmware unbootable.** Flashing a modified VBIOS via SPI programmer would still result in a non-booting card on Ampere.

**The Signing Primitive**

The signature scheme is **SHA-256 + PKCS#1 v2.1 RSA-3072**, as confirmed by NVIDIA's Falcon security documentation and the Open-IOV wiki. FalconUCodeDescV3 (used on consumer Ampere/GA102) stores 384-byte RSA signatures inline at offset `imem_load_size + pkc_data_offset`. Multiple signatures are stored per blob and selected at runtime by a `reg_fuse_version` silicon register matched against the descriptor's `signature_versions` bitmask — meaning a single firmware blob can carry valid signatures for several production fuse configurations simultaneously.

**The Ampere Boot Chain**

The full initialization sequence on Ampere runs in this order: **BROM → Booter (HS on SEC2) → GSP-RM (LS) → FWSEC (HS on GSP) → DEVINIT (LS on PMU) → SEC2-RTOS (LS)**. FWSEC creates a Write-Protected Region (WPR2) in VRAM and copies authenticated sections of the VBIOS into it after verifying them. Any component that is unsigned or that has been modified at any point in this chain causes Falcon to halt. The `Falcon In HALT or STOP state` error seen when attempting modified flashes on Ampere is the surface-level symptom of this halt.

**GA100 Uses V2 Descriptors — A Key Research Distinction**

A late-2025/early-2026 Linux nouveau patch series by Timur Tabi revealed that **GA100 (and Turing) use FalconUCodeDescV2, not V3**. V2 returns zero for `pkc_data_offset`, `engine_id_mask`, `ucode_id`, `signature_count`, and `signature_versions`, and uses a split `imem_sec_base`/`imem_sec_size` layout for NS vs. secure IMEM. The Linux nova-core driver comment reads: *"Only used on Turing and GA100, so return None for now."* V2 descriptors have received far less public scrutiny than V3. The CMP 170HX's GA100 die places it closer to Turing's signing infrastructure than to consumer Ampere — this matters because the Turing-generation signature implementation was the one ultimately exploited to produce OMGVflash.

**VBIOS ROM Structure — What Is and Isn't Signed**

A VBIOS ROM is a concatenated sequence of PCI-spec images:

* **Type 0x00 (PciAt):** The main ROM image. Contains the `0xAA55` header, PCIR signature, NPDE, and the BIT (BIOS Info Table) with token `0x70` (BIT\_TOKEN\_ID\_FALCON\_DATA) pointing to the PMU Lookup Table. The PciAt header metadata — including PCI subsystem vendor and device IDs — is **not** covered by Falcon signatures.
* **Type 0x03 (EFI/UEFI GOP):** The UEFI driver image for display output.
* **Type 0xE0 (FwSec) × 2:** Contains all signed microcode blobs: FWSEC, DEVINIT, Booter, GSP-RM, SEC2-RTOS, and PMU ucode. The FWSEC DMEM includes a `FalconAppifHdrV1` header with entries exposing `FRTS` and `SB` (Secure Boot) commands used by the driver.

Everything inside the Type 0xE0 partitions is authenticated. Any byte-level modification to any ucode blob, any DEVINIT script, or any thermal/voltage table referenced by WPR2 causes Falcon to halt. The PciAt header is not signature-protected — which is precisely why cross-flash tools can rebrand between signed VBIOSes (rewriting subsystem IDs) but cannot flash modified firmware.

**The Ampere Security Level**

NVIDIA has progressively strengthened Falcon security across GPU generations:

| Generation                | Signature Enforcement      | Custom VBIOS possible?    |
| ------------------------- | -------------------------- | ------------------------- |
| Maxwell (900-series)      | Introduced                 | No (but bypasses found)   |
| Pascal (10-series)        | Tightened                  | No                        |
| Turing (20-series)        | Strong                     | No (bypass found 2023)    |
| Ampere (30-series, GA100) | Strong + certificate chain | **No — not yet bypassed** |
| Ada (40-series)           | Same as Ampere             | No (cross-flash only)     |

The key distinction: OMGVflash and NVflashk achieved signature check bypass for Turing (RTX 20-series) and older cards, enabling custom VBIOS modification on those generations. For Ampere and Ada, these tools enable cross-flashing of signed firmware between compatible cards — but **cannot bypass the signature check to allow modified unsigned firmware**. Any byte-level modification to an Ampere VBIOS still results in a non-booting card.

The CMP 170HX uses the GA100 die — Ampere. Its firmware is fully within this unbreached security regime.

**Why This Matters for the CMP 170HX**

The FMA throttle, PCIe Gen 1 lock, and all other firmware-level restrictions on the CMP 170HX are enforced by signed VBIOS code that Falcon authenticates before allowing the GPU to boot. To remove these restrictions at the firmware level requires either:

1. Obtaining NVIDIA's signing key (not realistic)
2. Finding a vulnerability in Falcon's authentication implementation that allows bypassing the signature check on Ampere
3. Finding a way to patch the running firmware after authentication (runtime exploit)

None of these currently exist publicly. This is the single biggest barrier to fully unlocking the card.
