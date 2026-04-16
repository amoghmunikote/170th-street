# Contributions

Here's the Contributions page:

***

**Contributing to 170th Street**

170th Street is a living research project. The documentation you're reading exists because a handful of people took the time to benchmark, disassemble, repair, and reverse-engineer a $250 piece of hardware that NVIDIA wrote off as electronic waste. There is still a significant amount we don't know — and the next breakthrough is just as likely to come from someone reading this page as from anywhere else.

This page explains how to contribute, what kinds of contributions are most needed, and what standards we hold contributions to.

***

**Who Should Contribute**

Anyone with direct experience with the CMP 170HX or relevant technical expertise. You do not need to be a researcher or professional engineer. The FMA workaround that unlocked 16× FP32 performance was discovered by a community member posting a two-line code diff to a GitHub issue. The repair procedure that saved a dead card was documented by someone who had never worked with that specific LDO before. The most valuable contributions come from people who tried something and wrote down exactly what happened.

If you own a CMP 170HX, your benchmark results and setup notes are valuable. If you have electronics rework experience, your attempt at the PCIe capacitor mod — successful or not — is valuable. If you have firmware or driver reverse engineering skills, you may be sitting on the most impactful contribution of all.

***

**How to Contribute**

The documentation source lives on GitHub. All contributions go through pull requests.

**Step 1 — Fork the repository**

bash

```bash
# Fork via the GitHub UI, then clone your fork
git clone https://github.com/YOUR-USERNAME/170th-street
cd 170th-street
```

**Step 2 — Create a branch**

bash

```bash
git checkout -b your-contribution-name
# Examples:
# git checkout -b pcie-cap-mod-results
# git checkout -b llama-bench-llama3-8b
# git checkout -b nvlink-component-sourcing
```

**Step 3 — Make your changes**

All pages are Markdown files. Edit the relevant file or create a new one. See the Style Guide section below for formatting conventions.

**Step 4 — Commit and push**

bash

```bash
git add .
git commit -m "Add PCIe capacitor mod results — confirmed x16 on CMP 170HX"
git push origin your-contribution-name
```

**Step 5 — Open a pull request**

Open a PR against the `main` branch on GitHub. In the PR description, include:

* What you changed and why
* What hardware/software environment you used if submitting results
* Any caveats or uncertainties in your findings
* Sources for any factual claims

PRs are reviewed by maintainers and merged when accurate and well-documented. Once merged, changes sync automatically to the GitBook.

***

**What We Need Most**

These are the open problems and documentation gaps where contributions would have the most impact, roughly in order of priority.

**🔴 Hardware Research — High Priority**

_PCIe capacitor mod confirmation on CMP 170HX_ The capacitor mod has been confirmed on other CMP HX models but not yet publicly documented on the 170HX specifically. If you attempt it — successfully or not — document your exact procedure, the reference designators you populated, and your before/after `lspci` output. Even a failed attempt with a clear failure mode is useful data.

_NVLink hardware population_ The schematic analysis page documents what needs to be populated. The actual soldering work has not been publicly attempted. Anyone with BGA/QFN rework capability and access to the necessary components is in a position to make the most significant hardware contribution to this project.

_Tensor Core activation investigation_ gpu\_burn reports \~6.2 TFLOPS via the cuBLAS Tensor Core path — the same as non-FMA FP32. Whether Tensor Cores are disabled by firmware or simply capped at the same ceiling is unknown. Direct PTX-level testing of WMMA instructions would clarify this.

**🟡 Firmware & Driver Research — High Priority**

_Ampere Falcon bypass research_ The single most impactful open problem. Any analysis of Falcon microcode, authentication implementation, or potential vulnerabilities in the Ampere certificate chain validation is welcome. Even negative results — approaches confirmed not to work — narrow the search space.

_Driver compatibility with newer releases_ Drivers newer than \~595.x on Linux and 471.50 on Windows have shown issues with the CMP 170HX via dartraiden's patcher. Systematic testing of intermediate versions to identify when regressions were introduced, and patches to fix them, would benefit everyone running this card.

_FMA throttle mechanism identification_ Whether the FMA throttle lives in the VBIOS, the kernel driver, or Falcon microcode loaded at boot is not definitively established. Binary analysis of NVIDIA driver versions, or testing the datacenter driver (used for A100) on the CMP 170HX, could answer this.

**🟢 Benchmarks & Results — Always Welcome**

_llama-bench results_ Submit your token generation and prefill speeds with model name, quantization level, context size, driver version, and VBIOS version. Any 3B–8B model at Q4–Q8 quantization is useful.

_FluidX3D results_ Before and after FMA patch MLUPs/s, with your exact modification. If you tested a different simulation domain size or precision mode, include that.

_Any workload not currently documented_ If you ran something on this card that isn't covered — molecular dynamics, sparse linear solvers, video processing, signal processing, anything — write it up.

_Power consumption measurements_ Accurate power draw at different workloads, measured at the wall with a meter rather than reported by nvidia-smi (which underreports due to the FMA throttle misleading the power model).

**🔵 Documentation Improvements — Always Welcome**

* Corrections to factual errors anywhere in the docs
* Clarifications to confusing explanations
* Better installation instructions based on your actual experience
* Translations of Chinese-language resources that are currently inaccessible to most readers
* Additional photos from your own teardown or modification

***

**Contribution Standards**

**Be specific.** "I got good results with llama.cpp" is not a contribution. "Llama 3.1 8B Q4\_K\_M, 99 GPU layers, driver 525.89.02, VBIOS 92.00.6D.00.0A, decode speed 28.4 ± 0.6 t/s, prefill 187 t/s at 512 token context" is a contribution.

**Document failures too.** A carefully documented failed attempt at the PCIe capacitor mod is more valuable than silence. Knowing what doesn't work, and exactly why, is progress.

**Source your claims.** If you're adding factual information that isn't from your own direct testing, link the source. If you're not sure about something, say so.

**Don't speculate without labeling it.** Clearly distinguish between confirmed results, reasonable inferences from data, and open questions. The NVLink page is a good model for how to handle genuinely uncertain territory.

**Test before submitting.** If you're submitting benchmark results, run them at least three times and report the average. If you're documenting a procedure, make sure you've actually followed it start to finish.

**Keep it hardware-focused.** This documentation is about the CMP 170HX specifically. General GPU knowledge, CUDA tutorials, and AI framework guides belong elsewhere. Contributions should tie back to something specific about this card.

***

**Style Guide**

Pages use standard Markdown with GitBook extensions for callouts and hints. A few conventions to keep things consistent:

* **Headers:** Use sentence case (`## What the FMA throttle is`, not `## What The FMA Throttle Is`)
* **Code blocks:** Always specify the language for syntax highlighting (` ```bash `, ` ```python `, ` ```c `)
* **Numbers:** Use specific measured values over vague descriptors. "1,355 GB/s" not "very fast memory bandwidth"
* **Warnings:** Use GitBook's `> ⚠️` callout style for anything that could damage hardware
* **Images:** Link images from the niconiconi source URLs already established in the docs, or host new images at a stable public URL and note the source
* **Tables:** Prefer tables over bullet lists for comparative data
* **Tone:** Technical and direct. No marketing language. Uncertainty should be stated plainly

***

**Reporting Issues Without a PR**

If you found an error, have a question, or want to flag something that needs research but aren't able to write the fix yourself, open a GitHub Issue. Use the following labels if available:

* `factual-error` — something in the docs is wrong
* `missing-information` — a gap that should be filled
* `research-needed` — an open question that requires investigation
* `benchmark-submission` — submitting results via issue rather than PR

***

**Recognition**

Contributors who make meaningful additions to the documentation are acknowledged in the credits. If you make a significant hardware discovery or research breakthrough, it will be credited prominently in the relevant section of the docs as well as the Sources page.

This project stands on the shoulders of niconiconi, jetcat8848, Dr. Moritz Lehmann, Xing Kangwei, and dartraiden. Every person who contributes verified knowledge about this hardware is part of that lineage.

***

**Questions**

If you're unsure whether something is worth contributing, or want feedback before investing time in a full write-up, open a GitHub Discussion and describe what you're thinking. The answer will almost certainly be yes.
