---
description: SCP-based code sync rules, appended when user selects scp sync.
placeholders:
  {local_project_dir}: Local project root (absolute path)
  {remote_project_dir}: Remote project root (absolute path)
  {ssh_host}: SSH host alias
---

## Code Sync

- **Method**: scp (manual)
- **Local project directory**: {local_project_dir}
- **Remote project directory**: {remote_project_dir}

### Workflow

1. **Edit locally**
2. **Upload via SCP**:
   ```bash
   scp {local_project_dir}/<file> {ssh_host}:{remote_project_dir}/<file>
   ```
3. **Execute remotely**

**Note**: scp has no version control. Back up important changes or switch to git sync.
