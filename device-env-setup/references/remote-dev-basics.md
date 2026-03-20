---
description: Remote file transfer methods, appended to all SSH run_modes.
placeholders:
  {ssh_host}: SSH host alias
  {container_id}: Docker container ID (only used in docker examples, omit if N/A)
---

## Remote File Transfer

**Method 1: SCP (recommended)**
```bash
# Local file → remote host
scp local_file.sh {ssh_host}:/remote/path/

# Local file → remote Docker container (if applicable)
scp local_file.sh {ssh_host}:/tmp/ && \
  ssh {ssh_host} 'docker cp /tmp/local_file.sh {container_id}:/remote/path/'
```

**Method 2: HERE document (prevents local variable expansion)**
```bash
ssh {ssh_host} 'cat > /path/to/file.sh << "EOF"
#!/bin/bash
# Quoted "EOF" prevents local shell from expanding $VAR
echo "Hello"
EOF'
```

**Method 3: Base64 encoding (fallback for complex content)**
```bash
# Step 1: Encode locally
base64 input.sh > /tmp/encoded.b64
# Step 2: Transfer
scp /tmp/encoded.b64 {ssh_host}:/tmp/
# Step 3: Decode on remote
ssh {ssh_host} 'base64 -d /tmp/encoded.b64 > /final/path/output.sh'
```
**Avoid**: `echo "b64..." | base64 -d > file` — shell special character handling is unreliable.
