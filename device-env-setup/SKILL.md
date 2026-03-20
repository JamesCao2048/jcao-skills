---
name: device-env-setup
description: >-
  Use when you need to configure how Claude executes commands on machines with
  accelerator devices (Ascend NPU or NVIDIA GPU). Interactively collects
  environment details and writes a self-contained device environment config
  section to CLAUDE.md, including command execution templates and optional
  device health checks. Supports local, SSH, Docker, and Conda environments.
  Triggers on: "device env setup", "device-env-setup", "configure device environment",
  "setup NPU environment", "setup GPU environment", "配置设备环境", "设备环境配置".
---

# device-env-setup

Configure how Claude executes commands on machines with accelerator devices.

## Input

- **Launch script** (optional): path to a shell/python launch script for auto-detection
- **User answers** to interactive questions (accelerator type, run location, isolation, etc.)

## Output

A `## Device Environment Config` section written to `./CLAUDE.md` containing:
- Run mode and connection details
- Command execution templates (`exec_cmd_template`, `host_cmd_template`)
- Device constraints and health check instructions (if applicable)
- Remote file transfer methods (if remote)
- Code sync rules (if configured)

## Disclaimer (always show first)

```
This skill configures how Claude executes commands in your environment.
It does NOT install or set up CUDA, PyTorch, CANN, or other frameworks.
Your accelerator environment must already be working.
```

---

## Step 0: Initialize

Perform both actions before starting Q&A:

### 0a. Check existing config

Read `./CLAUDE.md`. Search for either section header:
- `## Device Environment Config` (current format)
- `## 执行环境配置` (legacy format from old env-setup skill)

If found:
```
Detected existing device environment config:
  Run mode: {extracted}
  Working directory: {extracted}
  ...
  [Legacy format from old env-setup skill — will be replaced with new format]

Update this config? (yes / no — keep existing)
```
User picks "no" → end.
User picks "yes" → continue (will replace in-place, never append second section).

### 0b. Optional script analysis

```
Do you have a launch script to analyze? (file path / skip)
```

If path provided, read the script and grep for:
- `ASCEND_RT_VISIBLE_DEVICES=...` or `CUDA_VISIBLE_DEVICES=...` → prefill device constraint
- `cd /path/...` or absolute paths → prefill working directory
- `torchrun`, `python`, `bash` invocations → context (not directly used as prefill)

Store extracted values as prefill suggestions. They are suggestions only — user can override.

---

## Step 1: Interactive Q&A

1-2 questions per turn. Wait for user reply before continuing.

### Phase 1: What (Accelerator)

**Q1 — Accelerator type**
```
What type of accelerator does this environment use?
  [1] Ascend NPU
  [2] NVIDIA GPU
  [3] None (CPU only)
```

Internally sets:
- `accel_type`: `npu` / `gpu` / `none`
- `device_env_var`: `ASCEND_RT_VISIBLE_DEVICES` / `CUDA_VISIBLE_DEVICES` / (empty)

### Phase 2: Where (Environment)

**Q2 — Run location + isolation + SSH host**

Ask together in one turn:
```
Where do commands run?
  Location: [1] Local  [2] Remote SSH
  Isolation: [1] Bare shell  [2] Docker  [3] Conda
```

If user picks Remote SSH, also ask:
```
  SSH host (~/.ssh/config Host alias):
```

**SSH connectivity check** (remote only, after collecting host):

Run:
```bash
ssh -o BatchMode=yes -o ConnectTimeout=5 {ssh_host} true
```

- Success → continue
- Failure → show:
  ```
  SSH passwordless login to '{ssh_host}' failed. Please ensure:
  1. Run: ssh-copy-id {ssh_host}
  2. Verify ~/.ssh/config has a Host entry for '{ssh_host}'

  [Retry] / [Abort] / [Continue anyway — config may not work]
  ```

Determine `run_mode` from answers:

| Location | Isolation | run_mode |
|----------|-----------|----------|
| Local | Bare shell | `local` |
| Local | Docker | `local+docker` |
| Local | Conda | `local+conda` |
| Remote | Bare shell | `ssh` |
| Remote | Docker | `ssh+docker` |
| Remote | Conda | `ssh+conda` |

**Q3 — Path & isolation details**

Conditional on Q2 answers. Collect all in one turn:
- **Docker** → Container ID or name: / Container working dir same as host? (yes / different path:)
- **Conda** → Environment name:
- **All** → Working directory: `{prefill}`

**Q4 — Code sync** (remote run_modes only)

First, auto-detect git remote match:
1. Local: `git remote get-url origin` (in project root)
2. Remote: `ssh {ssh_host} "cd {remote_working_dir} && git remote get-url origin"`
3. If both succeed and match → recommend git sync

```
Code sync method (how local edits reach the remote):
  [1] git (recommended — matching remote URL detected)
  [2] scp (manual upload)
  [3] No sync needed
```

If git: confirm `{local_project_dir}` (default: current working directory) and `{remote_project_dir}` (default: working dir from Q3).
If scp: same confirmations.

### Phase 3: Device (Constraints & Health)

**Skip entirely if Q1 = None (CPU only).**

**Q5 — Device constraints**

Based on `accel_type`:
```
# NPU:
ASCEND_RT_VISIBLE_DEVICES=? (e.g. 6,7 — leave blank for all devices)

# GPU:
CUDA_VISIBLE_DEVICES=? (e.g. 0,1 — leave blank for all devices)
```

Build `{device_constraint}`:
- User provides value (e.g. `6,7`) → `ASCEND_RT_VISIBLE_DEVICES=6,7`
- User leaves blank → omit the device constraint line from output

**Q6 — Health check**
```
Check device availability before running? (yes / no)
```

If yes:
```
Health check method:
  [1] Tensor dry-run (default) — parallel test each device with torch
      Requires torch (+ torch_npu for NPU) installed in your execution environment.
      Warning: NPU npu-smi Health=OK is unreliable; hung NPUs still show OK.
  [2] SMI only — nvidia-smi / npu-smi (no torch needed, faster, less reliable for NPU)
```

---

## Step 2: Confirm & Write

### 2a. Assemble output

Read the corresponding template and fill placeholders:

1. **Base template**: `references/exec-env-{run_mode}.md`
   - Fill all `{placeholder}` values from collected answers
   - If no device constraint: remove the `- **Device constraint**: ...` line entirely

2. **Health check** (if Q6 = yes):
   - Read `references/health-check-{npu|gpu}.md`
   - Select the correct variant (tensor or smi) based on Q6 answer
   - Fill `{host_cmd}` and `{exec_cmd}` from the base template's command wrappers:
     - For run_modes with only `exec_cmd_template`: use it for both
     - For run_modes with `host_cmd_template` + `exec_cmd_template`: use host for SMI, exec for tensor
   - Append after the base template

3. **Remote file transfer** (if run_mode contains `ssh`):
   - Read `references/remote-dev-basics.md`, fill `{ssh_host}` (and `{container_id}` if docker)
   - Append after health check (or after base if no health check)

4. **Code sync** (if Q4 = git or scp):
   - Read `references/remote-code-sync-{git|scp}.md`, fill placeholders
   - Append at the end

### 2b. Preview

Show the assembled output:
```
=== Will write to ./CLAUDE.md ===

[assembled content]

CHECKPOINT: Confirm to write? (yes / point out changes needed)
```

Wait for user confirmation. If changes requested, adjust and re-preview.

### 2c. Write

Write to `./CLAUDE.md`:
- File doesn't exist → create with the assembled content
- File exists, no device config section → append at the end
- File exists, has `## Device Environment Config` or `## 执行环境配置` → **replace that section in-place** (preserve everything else)

After writing, read back `./CLAUDE.md` to verify the section is present.

```
Device environment config written to ./CLAUDE.md

Downstream agents will use this config to execute commands.
To modify: edit ./CLAUDE.md directly, or re-run /device-env-setup.
```

---

## Notes

1. **Never assume execution details** — always collect via Q&A, even if CLAUDE.md has old config
2. **Script analysis is advisory only** — prefill suggestions, user can override everything
3. **Only touch the device config section** — never modify other parts of CLAUDE.md
4. **Same paths = simplify** — if host and container working dirs are identical, show once
5. **Read `references/device-health-check.md`** for expert knowledge on health check design decisions (timeout rationale, npu-smi unreliability, etc.)
