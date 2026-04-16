# Sources

### Sources

A comprehensive reference list of all sources, tools, papers, and community resources referenced throughout this documentation.

***

**Primary Research Articles**

**niconiconi — "All GB/s without FLOPS: Nvidia CMP 170HX Review, Performance Lockdown Workaround, Teardown, Watercooling, and Repair"** The foundational article on the CMP 170HX. Covers performance benchmarking, FMA throttle discovery, PCB analysis, teardown, watercooling installation, and repair log. The single most comprehensive public resource on this card. [https://niconiconi.neocities.org/tech-notes/nvidia-cmp-170hx-review/](https://niconiconi.neocities.org/tech-notes/nvidia-cmp-170hx-review/)

**Xing Kangwei — "Exploration of Cryptocurrency Mining-Specific GPUs in AI Applications: A Case Study of CMP 170HX" (arXiv 2505.03782)** Peer-reviewed academic paper confirming the FMA workaround, providing llama-bench LLM inference results, energy efficiency analysis, and theoretical analysis of further improvements. Published April 2025. [https://arxiv.org/abs/2505.03782](https://arxiv.org/abs/2505.03782)

**Hackaday — "Hacking An NVIDIA CMP 170HX Crypto GPU For EM Sim Work"** Feature article covering niconiconi's work, published September 2024. Good entry point for newcomers. [https://hackaday.com/2024/09/11/hacking-an-nvidia-cmp-170hx-crypto-gpu-for-em-sim-work/](https://hackaday.com/2024/09/11/hacking-an-nvidia-cmp-170hx-crypto-gpu-for-em-sim-work/)

**Xing Kangwei — "Microbenchmarking Instruction-Level Tensor Core Throttling in NVIDIA CMP 170HX" (Zenodo 19002983)** Definitive microbenchmarking study characterizing the Tensor Core throttle mechanism. Discovers the 256 fixed-cycle MMA latency and 4-warp-per-SM dispatch limit, constructing a theoretical model that explains the observed 6.3 TFLOPS ceiling. Confirms the throttle is dispatch-level hardware gating, not physical damage. Published March 2026. [https://zenodo.org/records/19002983](https://zenodo.org/records/19002983)

**Xing Kangwei — "Instruction-Level Performance Analysis and Optimization Strategies for Constrained AI Accelerators: A Case Study of CMP 170HX" (Zenodo 18994970)** Proposes a systematic instruction-level framework for analyzing and bypassing restrictions on the CMP 170HX across three dimensions: instruction type, numerical precision, and execution unit. Introduces four operator-level circumvention strategies and validates them on Transformer inference, Stable Diffusion, and Diffusion Transformer workloads, achieving up to \~2× end-to-end improvement. Published March 2026. [https://zenodo.org/records/18994970](https://zenodo.org/records/18994970)

***

**Community Research & Tools**

**dartraiden/NVIDIA-patcher (GitHub)** Driver patcher adding 3D acceleration support for CMP series mining cards including the CMP 170HX. Active issue tracker is one of the best sources of community knowledge on driver compatibility and card behavior. [https://github.com/dartraiden/NVIDIA-patcher](https://github.com/dartraiden/NVIDIA-patcher)

**Issue #73 — "170hx can run higher FP32 flops than before" (dartraiden/NVIDIA-patcher)** The original December 2023 community discovery of the FMA workaround via OpenCL kernel modification. The two-line code diff that started everything. [https://github.com/dartraiden/NVIDIA-patcher/issues/73](https://github.com/dartraiden/NVIDIA-patcher/issues/73)

**ProjectPhysX/FluidX3D (GitHub)** Lattice Boltzmann Method CFD solver in OpenCL by Dr. Moritz Lehmann. The card is officially listed in the FluidX3D benchmark table. The no-FMA modification that unlocked 7,684 MLUPs/s originated here. [https://github.com/ProjectPhysX/FluidX3D](https://github.com/ProjectPhysX/FluidX3D)

**OMGVflash — TechPowerUp Forum Thread (Veii)** Development history and documentation of the Turing VBIOS signature bypass. Essential reading for understanding what has and hasn't been achieved in Ampere firmware security research. [https://www.techpowerup.com/forums/threads/omg-vflash-fully-patched-nvflash-from-x-to-ada-lovelace-v5-780.312601/](https://www.techpowerup.com/forums/threads/omg-vflash-fully-patched-nvflash-from-x-to-ada-lovelace-v5-780.312601/)

**NVflashk (GitHub — notfromstatefarm)** Modified nvflash with permanent mismatch bypass. Used for cross-flashing signed VBIOSes between compatible NVIDIA cards. [https://github.com/notfromstatefarm/nvflashk](https://github.com/notfromstatefarm/nvflashk)

***

**Hardware Documentation**

**NVIDIA Tesla A100 GA100-883 P1001-B02 PCB Schematics** Leaked NVIDIA reference schematics for the A100 40GB PCIe. Every component reference designator matches the CMP 170HX PCB exactly. Used for power tree analysis, repair, PCIe capacitor identification, and NVLink research throughout this documentation. Circulates in electronics repair communities — search for "NVIDIA Tesla A100 schematic GA100-883" to find.

**NVIDIA A100 Tensor Core GPU Architecture Whitepaper** Official NVIDIA documentation of the GA100 architecture, SM layout, memory subsystem, and compute capabilities. Authoritative reference for theoretical performance numbers. [https://images.nvidia.com/aem-dam/en-zz/Solutions/data-center/nvidia-ampere-architecture-whitepaper.pdf](https://images.nvidia.com/aem-dam/en-zz/Solutions/data-center/nvidia-ampere-architecture-whitepaper.pdf)

**NVIDIA Ampere Architecture In-Depth (NVIDIA Technical Blog)** Detailed developer-facing explanation of GA100 architecture features including Tensor Cores, NVLink, and SM design. [https://developer.nvidia.com/blog/nvidia-ampere-architecture-in-depth/](https://developer.nvidia.com/blog/nvidia-ampere-architecture-in-depth/)

**NVIDIA Falcon Security Documentation** NVIDIA's official (partial) documentation of the Falcon security processor and its operating modes. Essential for understanding the firmware signing architecture. [https://download.nvidia.com/open-gpu-doc/Falcon-Security/1/Falcon-Security.html](https://download.nvidia.com/open-gpu-doc/Falcon-Security/1/Falcon-Security.html)

**NVIDIA open-gpu-doc (GitHub)** NVIDIA's partial hardware documentation release. Contains register specifications and architectural details useful for driver and firmware research. [https://github.com/NVIDIA/open-gpu-doc](https://github.com/NVIDIA/open-gpu-doc)

**NVIDIA Open-Source Kernel Modules (GitHub)** Partially open-sourced NVIDIA Linux kernel driver, released 2022. Useful for analysis of driver-level behavior including any per-device restrictions. [https://github.com/NVIDIA/open-gpu-kernel-modules](https://github.com/NVIDIA/open-gpu-kernel-modules)

***

**Tools**

**nvflash 5.867** NVIDIA's official VBIOS flash utility for Linux and Windows. Used for backup and flashing of signed VBIOSes. [https://www.techpowerup.com/download/nvidia-nvflash/](https://www.techpowerup.com/download/nvidia-nvflash/)

**TechPowerUp VBIOS Database — CMP 170HX** Repository of known NVIDIA VBIOS files. The authoritative public source for CMP 170HX firmware versions with verified subsystem IDs. [https://www.techpowerup.com/vgabios/?type=\&model=CMP+170HX](https://www.techpowerup.com/vgabios/?type=\&model=CMP+170HX)

**clpeak** OpenCL benchmark measuring peak compute throughput and memory bandwidth. Primary tool for characterizing the FMA throttle and real-world memory bandwidth. [https://github.com/krrishnarraj/clpeak](https://github.com/krrishnarraj/clpeak)

**mixbench** GPU benchmark for evaluating performance at mixed operational intensities. Used to measure FP16 throughput (\~42 TFLOPS) on the CMP 170HX. [https://github.com/ekondis/mixbench](https://github.com/ekondis/mixbench)

**gpu\_burn** GPU stress testing tool. Used for Tensor Core testing via cuBLAS and thermal stability verification. [https://github.com/wilicc/gpu-burn](https://github.com/wilicc/gpu-burn)

**llama.cpp (ggml-org)** C/C++ LLM inference engine with CUDA support. Primary recommended framework for LLM inference on the CMP 170HX. Build with `-DCMAKE_CUDA_FLAGS="-fmad=false"`. [https://github.com/ggml-org/llama.cpp](https://github.com/ggml-org/llama.cpp)

**hashcat** GPU-accelerated password recovery tool. Used as an integer workload benchmark confirming unthrottled INT32 performance at 43,930 MH/s MD5. [https://hashcat.net/hashcat/](https://hashcat.net/hashcat/)

***

**Watercooling & Hardware**

**Bykski N-TESLA-A100-X-V2 Product Page (Bykski.us)** Official US distributor page for the only commercially available full-coverage waterblock compatible with the CMP 170HX. Note: AliExpress is significantly cheaper. [https://www.bykski.us/products/bykski-metal-pom-gpu-water-block-and-backplate-for-nvidia-tesla-a100-40gb](https://www.bykski.us/products/bykski-metal-pom-gpu-water-block-and-backplate-for-nvidia-tesla-a100-40gb)

**Bykski N-TESLA-A100-X-V2 (FormulaMod)** Alternative retailer with international shipping. [https://www.formulamod.com/Bykski-GPU-Block-For-Nvidia-Tesla-A100-40GB-Nvidia-CMP-170HX-Nvidia-Tesla-A30-24G-High-Heat-Resistance-Material-POM-Full-Metal-Construction-With-Backplate-Full-Cover-GPU-Water-Cooling-Cooler-Radiator-Block-N-TESLA-A100-X-V2-p3765067.html](https://www.formulamod.com/Bykski-GPU-Block-For-Nvidia-Tesla-A100-40GB-Nvidia-CMP-170HX-Nvidia-Tesla-A30-24G-High-Heat-Resistance-Material-POM-Full-Metal-Construction-With-Backplate-Full-Cover-GPU-Water-Cooling-Cooler-Radiator-Block-N-TESLA-A100-X-V2-p3765067.html)

***

**Media**

**Linus Tech Tips — "We Spent $5000 on a Crypto Mining GPU for Science…" (YouTube)** First major public coverage of the CMP 170HX. November 2021. Confirms early hardware details and the mining-only restriction at launch. [https://www.youtube.com/watch?v=vfVnxkj8p6I](https://www.youtube.com/watch?v=vfVnxkj8p6I)

**Chinese teardown video — 英伟达CMP 170HX显卡拆解 (YouTube)** Visual teardown guide. Essential reference for the difficult Step 7 board removal procedure. [https://www.youtube.com/watch?v=N1yhvXj\_Do8](https://www.youtube.com/watch?v=N1yhvXj_Do8)

**Second teardown video — 技数犬 CMP 170HX 拆机测试 (YouTube)** Alternative teardown with captions (auto-translatable to English). [https://www.youtube.com/watch?v=kaiB7R4mhTg](https://www.youtube.com/watch?v=kaiB7R4mhTg)

***

**Image Sources**

All hardware photos unless otherwise noted are sourced from niconiconi's article. Direct image URLs used throughout this documentation:

| Image                      | URL                                                                                                                                                                                |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| PCB front                  | [https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-pcb-1.jpg](https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-pcb-1.jpg)               |
| PCB back                   | [https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-pcb-2.jpg](https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-pcb-2.jpg)               |
| PCIe missing caps          | [https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-pcb-3.jpg](https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-pcb-3.jpg)               |
| Chassis exterior           | [https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-teardown-0.jpg](https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-teardown-0.jpg)     |
| Teardown step 1            | [https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-teardown-1.jpg](https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-teardown-1.jpg)     |
| Teardown step 2            | [https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-teardown-2.jpg](https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-teardown-2.jpg)     |
| Teardown step 3            | [https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-teardown-3.jpg](https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-teardown-3.jpg)     |
| Power topology schematic   | [https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-schematics-1.png](https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-schematics-1.png) |
| Power sequencing schematic | [https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-schematics-2.png](https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-schematics-2.png) |
| Repair photo 1             | [https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-repair-1.jpg](https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-repair-1.jpg)         |
| Repair photo 2             | [https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-repair-6.jpg](https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-repair-6.jpg)         |
| Watercooling step 1        | [https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-watercool-1.jpg](https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-watercool-1.jpg)   |
| Watercooling step 2        | [https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-watercool-2.jpg](https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-watercool-2.jpg)   |
| Watercooling complete      | [https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-watercool-3.jpg](https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/cmp-170hx-watercool-3.jpg)   |
| Roofline model SVG         | [https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/roofline-cmp170hx.svg](https://niconiconi.neocities.org/img/nvidia-cmp-170hx-review/roofline-cmp170hx.svg)           |

***

**Further Reading**

**repair.wiki — NVIDIA Pascal GPU Diagnosing Guide** General GPU repair methodology. The diagnostic approach applies broadly beyond Pascal. [https://repair.wiki/w/Nvidia\_Pascal\_GPU\_Diagnosing\_Guide](https://repair.wiki/w/Nvidia_Pascal_GPU_Diagnosing_Guide)

**VideoCardz — "NVIDIA CMP 170HX mining card with GA100 GPU has a massive heatspreader"** Early hardware coverage with additional teardown details. November 2021. [https://videocardz.com/newz/nvidia-cmp-170hx-mining-card-with-ga100-gpu-has-a-massive-heatspreader](https://videocardz.com/newz/nvidia-cmp-170hx-mining-card-with-ga100-gpu-has-a-massive-heatspreader)

**TechPowerUp GPU Database — CMP 170HX** Spec database entry. Note documented inaccuracies in the memory size field — real card is 8GB HBM2e. [https://www.techpowerup.com/gpu-specs/cmp-170hx.c3840](https://www.techpowerup.com/gpu-specs/cmp-170hx.c3840)
