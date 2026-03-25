# device-env-setup

Configure how Claude executes commands on machines with accelerator devices (Ascend NPU / NVIDIA GPU).

## Prerequisites

### SSH (for remote environments)

This skill requires passwordless SSH login for remote run modes (ssh, ssh+docker, ssh+conda).

**1. Set up SSH key authentication:**
```bash
ssh-copy-id user@your-server
```

**2. Configure SSH host alias** in `~/.ssh/config`:
```
Host myserver
  HostName 203.0.113.1
  User myuser
  IdentityFile ~/.ssh/id_rsa
```

**3. Verify:**
```bash
ssh myserver true  # should complete silently, no password prompt
```

### Accelerator Environment

Your device drivers and frameworks (CUDA, PyTorch, CANN, torch_npu, etc.) must already be installed and working. This skill configures command execution, not environment setup.

## Usage

```
/device-env-setup
```

The skill will interactively collect your environment details and write a `## Device Environment Config` section to `./CLAUDE.md`.

## Supported Environments

| Run Mode | Description |
|----------|-------------|
| `local` | Commands run directly on local machine |
| `ssh` | Commands run on remote server via SSH |
| `local+docker` | Commands run in a local Docker container |
| `ssh+docker` | Commands run in a Docker container on a remote server |
| `local+conda` | Commands run in a local Conda environment |
| `ssh+conda` | Commands run in a Conda environment on a remote server |
