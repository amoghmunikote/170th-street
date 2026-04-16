# Repair Guide

**Repair Guide**

This page documents the diagnosis and repair of a CMP 170HX that failed to be detected on PCIe, based on the repair performed by niconiconi using leaked A100 schematics. It is the most detailed public record of CMP 170HX hardware repair and serves as a reference for others encountering similar failures.

**Risk Warning**

Repairing a CMP 170HX requires electronics knowledge, soldering skills, appropriate tools (hot air rework station, oscilloscope, bench power supply, multimeter), and access to the leaked A100 schematics. Attempting repairs without these may result in permanent damage. Cards bought from mining farms may have pre-existing faults from heavy use. Budget accordingly.

<figure><img src="https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-repair-1.jpg" alt=""><figcaption></figcaption></figure>

**Symptom**

GPU operates for approximately one hour then drops off the PCIe bus. After this event, the card can no longer be detected by the host system at all — `nvidia-smi` and `lspci` show nothing.

**Diagnosis Methodology**

When a GPU cannot be detected on PCIe, the power delivery system is the first thing to check. Using the power tree documented on the previous page, work from upstream to downstream:

1. Measure resistance from both 12V external input inductors to ground — check for shorts
2. Measure resistance from the PCIe slot 12V input to ground — check for shorts
3. Power the card and probe output inductors for the core voltage rails — look for switching waveforms

In the documented repair, no shorts were found at the inputs, but no switching waveforms were present on the NVVDD output inductors — indicating the power-on sequence was not completing.

**Root Cause: 5V DC/DC Failure**

Probing the switching node of the MP1475DJ 5V DC/DC converter revealed a momentary pulse lasting tens of nanoseconds before stopping, repeating every few tens of microseconds. This is the hiccup-mode short circuit protection behavior — the converter was detecting a fault and refusing to start.

The MP1475DJ was desoldered and an external 5V supply was injected at its output to test the load. No overcurrent was detected. However, the resistance from the Power Good output pin to ground measured 5 ohms — a near-short. The 5V Power Good signal (`PS_5V_PGOOD`) was shorted, preventing the sequencing chain from proceeding.

**Root Cause: GS7155NVTD Internal Short**

Tracing the `PS_5V_PGOOD` net through the schematic identified the GS7155NVTD 3.3V LDO as the most likely source of the short, as its enable pin connects directly to this net via R1200 and a direct PCB trace.

Removing R1200 confirmed the short cleared — but due to the direct copper trace bypassing R1200 (described on the Power Tree page), the actual cause was an internal short inside the GS7155NVTD itself. The resistor removal worked not because R1200 was the path, but because the removal process accidentally broke the PCB trace.

**Repair Procedure**

1. Order replacement MP1475DJ and GS7155NVTD ICs
2. Resolder a new MP1475DJ — 5V rail restored
3. Replace the GS7155NVTD — requires careful QFN soldering at 420°C with extended heat application due to the PCB's many copper ground planes acting as heatsinks
4. Repair the broken PCB trace by soldering a jumper wire from the `PS_5V_PGOOD` net at a nearby SN74LV1T08 to the GS7155NVTD enable pin

<figure><img src="https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-repair-6.jpg" alt=""><figcaption></figcaption></figure>

**Result**

After the jumper wire repair, 3.3V\_SEQ was restored. Switching waveforms appeared on the oscilloscope. NVVDD (1.0V core) was restored. PEXVDD was restored. The GPU was detected on PCIe and returned to full operation.

**Key Lessons**

Cards from mining farms may have marginal or pre-damaged components before you ever touch them. The GS7155NVTD is a known weak point — its internal short was either caused by the heavy mining workload or by improper waterblock installation creating a short via unprotected unpopulated IC footprints. When installing a waterblock, all unpopulated IC footprints on the PCB must be covered with thermal pads to prevent shorts — this is explicitly called out in the watercooling guide.

The leaked NVIDIA Tesla A100 schematics are the single most important resource for this repair. Without them, the diagnosis would have been impossible. The complete PCB match between the CMP 170HX and the A100 40GB PCIe means these schematics apply directly.

**Resources**

* Primary repair documentation: [niconiconi's article](https://niconiconi.neocities.org/tech-notes/nvidia-cmp-170hx-review/) — Appendix 3
* Reference guide used: [NVIDIA Pascal GPU Diagnosing Guide](https://repair.wiki/w/Nvidia_Pascal_GPU_Diagnosing_Guide) on repair.wiki (methodology applies broadly)
* Leaked A100 schematics: [https://www.scribd.com/document/586601607/NVIDIA-A100-GA100-883-P1001-B02-Rev-A](https://www.scribd.com/document/586601607/NVIDIA-A100-GA100-883-P1001-B02-Rev-A)
