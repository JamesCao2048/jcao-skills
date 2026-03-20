# Device Health Check — Reference

Expert knowledge for device availability detection. This document is read by the skill at runtime, not written to CLAUDE.md.

## NPU (Ascend) — Critical Gotchas

### npu-smi is unreliable

`npu-smi info` shows `Health=OK` even when a device is hung. A hung NPU will accept the `set_device()` call but block forever on `synchronize()`. This has been observed in production — one case took 555s before the process was killed.

**The only reliable check**: allocate a tensor on the device and call `synchronize()` with a timeout.

### Timeout must be >= 30 seconds

`import torch` + `import torch_npu` cold start takes approximately 18 seconds. A shorter timeout will falsely report ALL devices as hung. 30 seconds is sufficient to distinguish cold start (~18s) from truly hung devices (infinite block).

### Parallel testing

Test all devices concurrently (background processes). Sequential testing would take `N × 30s` which is too slow for 8-device machines. Parallel testing completes in ~30s regardless of device count.

### torch_npu.npu.synchronize(N) is the critical call

Without `synchronize()`, the tensor operation may return immediately (async dispatch) without actually exercising the device. The sync forces the operation to complete and reveals hung state.

## GPU (NVIDIA)

### nvidia-smi is generally reliable

Unlike NPU, `nvidia-smi` output is trustworthy for most failure modes. The tensor dry-run approach is a stronger guarantee but less critical than for NPU.

### Timeout can be shorter (15s)

CUDA import is faster than torch_npu. 15 seconds is sufficient.

### torch.cuda.synchronize(N) is still recommended

Same reasoning as NPU — async dispatch can mask failures.

## Common Patterns

### Device enumeration always happens on host

Even in Docker mode, `npu-smi` / `nvidia-smi` typically runs on the host (the SMI tools are mounted from host into container in most setups, but the host version is canonical).

### Tensor dry-run always happens in the execution environment

`torch` / `torch_npu` are installed in the container or conda environment, not on the host. The health check must use `exec_cmd_template` for tensor tests.

### Reporting format

Always report per-device status and suggest the first available device:
```
Device Health Check (N devices, parallel scan ~30s):
  npu:0  HUNG
  npu:1  OK  <- suggested
  npu:2  OK
  ...
```
If all devices are unavailable, report clearly and halt — do not attempt to proceed.
