---
description: NPU device health check section appended to CLAUDE.md. Two variants selected by health_check_method.
placeholders:
  {host_cmd}: The host_cmd_template from the active exec-env template (for npu-smi)
  {exec_cmd}: The exec_cmd_template from the active exec-env template (for tensor test)
  {device_constraint}: Device constraint string (to know which devices to check)
variants:
  tensor: Full tensor dry-run (default, requires torch + torch_npu)
  smi: SMI-only (fallback, no torch needed)
---

<!-- VARIANT: tensor -->

### Device Health Check

> **Important**: `npu-smi info` showing `Health=OK` is **unreliable** — hung NPUs still report OK.
> The only reliable detection is an actual tensor operation with synchronization.
> Timeout must be >= 30s (torch + torch_npu cold start takes ~18s).

**Step 1 — Get device count** (via host_cmd_template):
```bash
{host_cmd} "npu-smi info | grep -cE '^\| [0-9]'"
```

**Step 2 — Parallel test all devices** (via exec_cmd_template, 30s timeout each):
```bash
for N in $(seq 0 $((COUNT - 1))); do
  (
    out=$({exec_cmd} "timeout 30 python3 -c \"
import torch, torch_npu
torch_npu.npu.set_device(${N})
(torch.ones(1).to('npu:${N}') + 1)
torch_npu.npu.synchronize(${N})
print('OK')
\"" 2>/dev/null)
    [ "$out" = "OK" ] && echo "npu:${N} OK" || echo "npu:${N} HUNG"
  ) &
done
wait
```

**Step 3 — Report results**: list status per device, suggest first available. If all HUNG, report "all NPUs unavailable, contact admin" and do not proceed.

<!-- VARIANT: smi -->

### Device Health Check

> SMI-only check. Note: `npu-smi Health=OK` does not guarantee the device is responsive.

**Check** (via host_cmd_template):
```bash
{host_cmd} "npu-smi info"
```
Review the Health column for each device.
