# Credits

**Amogh Munikote** Creator and maintainer of 170th Street Docs. Responsible for building, organizing, and maintaining this documentation project. First person to publicly confirm the PCIe capacitor mod works on the CMP 170HX (April 2026), upgrading the link from x4 to x16 width. [https://github.com/amoghmunikote](https://github.com/amoghmunikote)

**niconiconi** Author of the foundational CMP 170HX research article covering performance benchmarking, FMA throttle discovery, PCB analysis, teardown, watercooling installation, power tree analysis, and repair documentation. [https://niconiconi.neocities.org/tech-notes/nvidia-cmp-170hx-review/](https://niconiconi.neocities.org/tech-notes/nvidia-cmp-170hx-review/)

**jetcat8848** First public confirmation of the FMA workaround in December 2023, posting the two-line OpenCL code modification that raised FP32 performance 16× on the CMP 170HX. [https://github.com/dartraiden/NVIDIA-patcher/issues/73](https://github.com/dartraiden/NVIDIA-patcher/issues/73)

**Dr. Moritz Lehmann** Author of FluidX3D. Provided the hint that led to the FMA modification in FluidX3D, enabling 7,684 MLUPs/s — 90% of a real A100 — on the CMP 170HX. [https://github.com/ProjectPhysX/FluidX3D](https://github.com/ProjectPhysX/FluidX3D)

**Xing Kangwei** Author of three research papers on the CMP 170HX. arXiv 2505.03782 validated the FMA workaround and documented LLM inference results. Zenodo 19002983 definitively characterized the Tensor Core throttle mechanism through microbenchmarking, discovering the 256-cycle MMA latency and 4-warp dispatch limit. Zenodo 18994970 proposed a systematic instruction-level circumvention framework validated on Transformer inference, Stable Diffusion, and Diffusion Transformer workloads. [https://arxiv.org/abs/2505.03782](https://arxiv.org/abs/2505.03782) [https://zenodo.org/records/19002983](https://zenodo.org/records/19002983) [https://zenodo.org/records/18994970](https://zenodo.org/records/18994970)

**dartraiden** Maintainer of NVIDIA-patcher, which enables 3D acceleration on the CMP 170HX and hosts the community issue tracker where much of the collaborative research has taken place. [https://github.com/dartraiden/NVIDIA-patcher](https://github.com/dartraiden/NVIDIA-patcher)

**Linus Tech Tips** First major public coverage of the CMP 170HX in November 2021, bringing widespread attention to the card's existence, hardware internals, and potential beyond mining. [https://www.youtube.com/watch?v=vfVnxkj8p6I](https://www.youtube.com/watch?v=vfVnxkj8p6I)

**Veii (OMGVflash)** Author of OMGVflash, whose Falcon security research and Turing VBIOS bypass work forms the foundation of the firmware security research documented here. [https://www.techpowerup.com/forums/threads/omg-vflash-fully-patched-nvflash-from-x-to-ada-lovelace-v5-780.312601/](https://www.techpowerup.com/forums/threads/omg-vflash-fully-patched-nvflash-from-x-to-ada-lovelace-v5-780.312601/)
