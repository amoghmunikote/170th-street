# PCIe Capacitor Mod

**PCIe Capacitor Mod**

The CMP 170HX has two independent PCIe bandwidth restrictions. This page documents the hardware-level fix for one of them: the missing AC coupling capacitors that limit the card to x4 lane width. This is an unconfirmed modification specifically on the CMP 170HX — confirmed on other CMP HX models — and requires fine soldering skills.

**Understanding the Two Layers**

As documented in the PCB Analysis page, PCIe bandwidth is restricted in two independent ways:

**Layer 1 — Firmware speed lock:** The VBIOS enforces PCIe Gen 1 speed (2.5 GT/s) regardless of the host slot. This is a firmware-level restriction that cannot currently be removed due to Ampere VBIOS signing. After this mod, the card will still operate at Gen 1 speed.

**Layer 2 — PCB capacitor omission:** 12 of the 16 PCIe data lanes have their AC coupling capacitors physically missing from the PCB. These 0402 capacitors are required for valid differential signaling on each lane. Their absence forces the PCIe link to negotiate down to x4 width.

This mod addresses Layer 2 only. The result will be **PCIe Gen 1 x16 (\~4 GB/s)** instead of **PCIe Gen 1 x4 (\~1 GB/s)** — a 4× improvement in PCIe bandwidth.

<figure><img src="https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-pcb-3.jpg" alt=""><figcaption></figcaption></figure>

**Why This Matters**

Current PCIe bandwidth: \~0.85 GB/s After this mod: \~4 GB/s Full PCIe 4.0 x16 (not achievable without Layer 1 fix): \~64 GB/s

4 GB/s is still far below full PCIe bandwidth but is meaningful for workloads that do CPU-GPU data transfers, multi-GPU setups using PCIe, and any application where the current 0.85 GB/s is a bottleneck.

**Skill Requirements**

This modification requires:

* Proficiency with 0402 SMD soldering (these components are approximately 1.0mm × 0.5mm)
* Hot air rework station or very fine-tipped soldering iron
* Magnification (microscope or loupe recommended)
* Steady hands and patience

0402 components are considered small but workable by experienced hobbyists. If you have not soldered 0402 components before, practice on a dead PCB first.

**Required Components**

* **24× 0402 capacitors, 0.22µF (220nF)**, 25V or higher voltage rating, X7R dielectric
  * You need 2 capacitors per lane × 12 missing lanes = 24 capacitors total
  * **0.22µF is the value specified in the NVIDIA A100 GA100-883 reference schematic (P1001-B02, Page 3 — IO: PCIe CONNECTOR)** — this is the authoritative value direct from NVIDIA's own design
  * Order at least 30 to account for losses during 0402 soldering
  * Available from Mouser, DigiKey, LCSC (cheapest), or harvested from dead electronics
  * Example part: Murata GRM155R61E224KA12D (0402, 0.22µF, 25V, X5R) or equivalent

**Reference Designators**

The capacitors in the schematic are labeled in the C1100–C1350 range (e.g. C1120, C1125, C1130, C1135 for each differential pair). Cross-reference these against your physical PCB to identify exactly which pads are empty on your card. The empty pads will have no component but visible copper pads — they are located along the PCIe lane routing between the connector gold fingers and the GPU die.

**Procedure**

1. **Complete the full teardown** as documented in the Teardown Guide — the PCB must be bare and accessible
2. **Identify the empty pads** — using the PCB image above as reference and the schematic reference designators, locate the 12 pairs of empty 0402 pads along the PCIe lanes on the PCB edge
3. **Tin the pads** — apply a small amount of solder to each pad
4. **Place and reflow** — use tweezers to position each capacitor, then reflow with a soldering iron or hot air
5. **Inspect under magnification** — verify no bridges between pads and good solder joints on both ends of each capacitor
6. **Reassemble and test** — verify with `lspci -vv | grep -i lnksta` that the link now shows x16 width

**Verification**

Before the mod:

```
LnkSta: Speed 2.5GT/s, Width x4 (downgraded)
```

After a successful mod:

```
LnkSta: Speed 2.5GT/s, Width x16
```

**Status on the CMP 170HX**

The PCB-level capacitor mod has been confirmed on other CMP HX models (50HX, 90HX) but has not been publicly documented as confirmed on the CMP 170HX specifically as of this writing. The PCB analysis strongly suggests it should work identically since the PCIe lane routing follows the same design. If you attempt this mod, please report your results to the 170th Street community.

**If You've Done This Mod**

Submit your results including before/after `lspci` output and any PCIe bandwidth measurements to the community. Your contribution will be documented here.
