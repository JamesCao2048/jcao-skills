---
description: Git-based code sync rules, appended when user selects git sync.
placeholders:
  {local_project_dir}: Local project root (absolute path)
  {remote_project_dir}: Remote project root (absolute path)
  {git_remote_url}: Shared git remote URL
  {ssh_host}: SSH host alias
---

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
