---
cover: .gitbook/assets/New Project.png
coverY: 0
---

# Welcome!

Welcome to 170th Street — the most comprehensive community resource for the NVIDIA CMP 170HX, a misunderstood piece of hardware that deserves a second life.

The CMP 170HX is built on the same GA100 silicon as NVIDIA's $10,000 A100 datacenter GPU. Sold in 2021 as a dedicated Ethereum mining card, it was deliberately crippled by NVIDIA to prevent general compute use — but the underlying hardware is extraordinary. With 1,493 GB/s of HBM2e memory bandwidth that beats even the RTX 4090, 42 TFLOPS of FP16 compute, and a PCB nearly identical to the A100, this card has enormous untapped potential.

This documentation covers everything we know about unlocking that potential:

* **Hardware** — full teardown, PCB analysis, comparison to the A100, and what NVIDIA removed and why
* **Software Setup** — driver installation, power configuration, and clock tuning on Linux
* **Performance** — honest benchmarks, what the FMA throttle actually costs you, and what workloads still shine
* **Modifications** — VBIOS flashing, PCIe capacitor mod, watercooling installation, and NVLink research
* **AI & ML** — LLM inference, FP16 workloads, the FMA workaround, and real-world usability
* **Security Research** — firmware signing, the path toward unlocking FMA, and tools for investigation

This is a living document. The goal is to turn a piece of discarded mining hardware into a viable budget compute platform — and eventually, to fully unlock the GA100 silicon inside.

This project is community-driven and everyone is welcome to contribute. Whether you've done your own benchmarks, discovered a new workaround, successfully performed a hardware mod, or are researching the firmware — your findings belong here. Open a pull request, submit a change request, or reach out directly. No contribution is too small. The more people working on this, the faster we unlock everything this card can do.
