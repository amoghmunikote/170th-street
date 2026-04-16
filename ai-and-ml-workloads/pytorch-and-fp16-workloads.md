# PyTorch & FP16 Workloads

**PyTorch & FP16 Workloads**

PyTorch is the dominant deep learning framework and the primary path for AI research and model development on the CMP 170HX. This page covers setup, which operations work well, which do not, and how to structure workloads for best performance.

**Installation**

bash

```bash
# Install PyTorch with CUDA 12.x support
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121

# Verify GPU is detected
python3 -c "import torch; print(torch.cuda.get_device_name(0))"
# Should print: NVIDIA Graphics Device (or similar)

python3 -c "import torch; print(torch.cuda.get_device_properties(0))"
# Should show: compute capability 8.0, 8GB VRAM
```

**The FP16 Priority**

Standard FP32 PyTorch operations use FMA extensively and will perform poorly on the CMP 170HX — expect results close to 0.39 TFLOPS. The fix is to use FP16 (half precision) for all tensor operations wherever possible. FP16 is unthrottled at \~42 TFLOPS on this card.

python

```python
import torch

# Always move tensors to FP16 on the CMP 170HX
model = model.half().cuda()       # convert model weights to FP16
input = input.half().cuda()       # convert input tensors to FP16

# Or use autocast for automatic mixed precision
with torch.autocast(device_type='cuda', dtype=torch.float16):
    output = model(input)
```

**Matrix Multiplication Benchmark**

A quick test to confirm FP16 is working at full speed:

python

```python
import torch
import time

device = torch.device('cuda')
N = 4096

# FP32 — will be slow due to FMA throttle
a_fp32 = torch.randn(N, N, device=device, dtype=torch.float32)
b_fp32 = torch.randn(N, N, device=device, dtype=torch.float32)
torch.cuda.synchronize()
t0 = time.time()
for _ in range(100):
    c = torch.matmul(a_fp32, b_fp32)
torch.cuda.synchronize()
t1 = time.time()
fp32_tflops = (2 * N**3 * 100) / (t1 - t0) / 1e12
print(f"FP32 TFLOPS: {fp32_tflops:.2f}")  # expect ~0.39

# FP16 — should be ~42 TFLOPS
a_fp16 = a_fp32.half()
b_fp16 = b_fp32.half()
torch.cuda.synchronize()
t0 = time.time()
for _ in range(100):
    c = torch.matmul(a_fp16, b_fp16)
torch.cuda.synchronize()
t1 = time.time()
fp16_tflops = (2 * N**3 * 100) / (t1 - t0) / 1e12
print(f"FP16 TFLOPS: {fp16_tflops:.2f}")  # expect ~40+
```

**What Works Well**

✅ **FP16 inference** — load a pretrained model in FP16 and run forward passes. Fully viable for transformer models, CNNs, and most inference workloads.

✅ **INT8 quantized inference** — integer operations are unthrottled at 12.5 TIOPS. Using `torch.quantization` or `bitsandbytes` for INT8 inference works well.

✅ **Memory-bandwidth-bound operations** — attention mechanisms at large sequence lengths, embedding lookups, batch normalization, layer normalization — all benefit from the CMP 170HX's exceptional memory bandwidth.

✅ **Hugging Face Transformers inference in FP16** — the standard pattern works correctly:

python

```python
from transformers import AutoModelForCausalLM, AutoTokenizer
import torch

model_id = "mistralai/Mistral-7B-Instruct-v0.3"
tokenizer = AutoTokenizer.from_pretrained(model_id)
model = AutoModelForCausalLM.from_pretrained(
    model_id,
    torch_dtype=torch.float16,   # critical — use FP16
    device_map="cuda"
)
```

**What Does Not Work Well**

❌ **FP32 training** — uses FMA heavily throughout the training loop. Do not attempt FP32 training on this card without source-level modifications.

❌ **BF16 operations** — BF16 (bfloat16) uses the same FMA hardware path as FP32 on Ampere. Avoid BF16 on the CMP 170HX.

❌ **Large models exceeding 8GB** — no VRAM offload to system RAM at useful speed given the PCIe bottleneck (\~0.85 GB/s). Models must fit entirely in VRAM.

❌ **Compute-intensive FP16 training** — fine-tuning uses FP32 gradient accumulation which triggers FMA. Fine-tuning is not practical on this card without significant framework modifications.

**VRAM Management**

python

```python
# Check VRAM usage
print(torch.cuda.memory_allocated() / 1e9, "GB allocated")
print(torch.cuda.memory_reserved() / 1e9, "GB reserved")

# Clear cache between runs
torch.cuda.empty_cache()

# Monitor during inference
torch.cuda.reset_peak_memory_stats()
with torch.no_grad():
    output = model(input)
print(f"Peak VRAM: {torch.cuda.max_memory_allocated()/1e9:.2f} GB")
```

**Recommended Inference Pattern**

python

```python
import torch
from torch import nn

def run_inference_cmp170hx(model, inputs):
    """
    Optimized inference pattern for CMP 170HX:
    - FP16 precision throughout
    - no_grad to avoid storing activations
    - autocast for safety on any FP32 fallback ops
    """
    model = model.half().cuda().eval()

    with torch.no_grad():
        with torch.autocast(device_type='cuda', dtype=torch.float16):
            outputs = model(**{k: v.cuda() for k, v in inputs.items()})

    return outputs
```
