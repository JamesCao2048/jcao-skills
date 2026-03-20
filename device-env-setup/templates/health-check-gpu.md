---
description: GPU device health check section appended to CLAUDE.md. Two variants selected by health_check_method.
placeholders:
  {host_cmd}: The host_cmd_template from the active exec-env template (for nvidia-smi)
  {exec_cmd}: The exec_cmd_template from the active exec-env template (for tensor test)
  {device_constraint}: Device constraint string (to know which devices to check)
variants:
  tensor: Full tensor dry-run (default, requires torch with CUDA)
  smi: SMI-only (fallback, no torch needed)
---

<!-- VARIANT: tensor -->

### Device Health Check

> Timeout of 15s per device (CUDA import is faster than torch_npu).

**Step 1 — Get device count** (via host_cmd_template):
```bash
{host_cmd} "nvidia-smi -L | wc -l"
```

**Step 2 — Parallel test all devices** (via exec_cmd_template, 15s timeout each):
```bash
for N in $(seq 0 $((COUNT - 1))); do
  (
    out=$({exec_cmd} "timeout 15 python3 -c \"
import torch
torch.cuda.set_device(${N})
(torch.ones(1).to('cuda:${N}') + 1).cpu()
torch.cuda.synchronize(${N})
print('OK')
\"" 2>/dev/null)
    [ "$out" = "OK" ] && echo "gpu:${N} OK" || echo "gpu:${N} FAIL"
  ) &
done
wait
```

**Step 3 — Report results**: list status per device, suggest first available.

<!-- VARIANT: smi -->

### Device Health Check

**Check** (via host_cmd_template):
```bash
{host_cmd} "nvidia-smi"
```
Review GPU status and memory usage.
