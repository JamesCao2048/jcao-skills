---
description: Code sync rules. Contains git and scp variants — read the section matching the user's sync method.
placeholders (git):
  {local_project_dir}: Local project root (absolute path)
  {remote_project_dir}: Remote project root (absolute path)
  {git_remote_url}: Shared git remote URL
  {ssh_host}: SSH host alias
placeholders (scp):
  {local_project_dir}: Local project root (absolute path)
  {remote_project_dir}: Remote project root (absolute path)
  {ssh_host}: SSH host alias
---

<!-- VARIANT: git -->

## Code Sync

- **Method**: git
- **Local project directory**: {local_project_dir}
- **Remote project directory**: {remote_project_dir}
- **Git remote URL**: {git_remote_url}

### Workflow (must follow)

For any change to git-tracked files:

1. **Edit locally** — modify files in `{local_project_dir}`
2. **Commit** — `git add <files> && git commit -m "<message>"`
3. **Push** — `git push`
4. **Pull on remote** — `ssh {ssh_host} "cd {remote_project_dir} && git pull"`
5. **Execute remotely** — run commands via exec_cmd_template

**Never**:
- Edit git-tracked files directly on the remote server
- Overwrite git-tracked files via scp/base64 (bypasses version control)
- Commit on remote then sync back (causes divergence — rebase locally first)

<!-- VARIANT: scp -->

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
