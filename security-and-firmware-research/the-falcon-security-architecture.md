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
