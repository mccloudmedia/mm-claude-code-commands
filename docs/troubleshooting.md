# Troubleshooting Guide

Common issues and solutions for MM Claude Code Commands.

## Table of Contents

- [GitHub CLI Issues](#github-cli-issues)
- [Authentication Problems](#authentication-problems)
- [Command Not Found](#command-not-found)
- [Git Repository Issues](#git-repository-issues)
- [Permission Errors](#permission-errors)
- [Network Issues](#network-issues)
- [Common Error Messages](#common-error-messages)

## GitHub CLI Issues

### GitHub CLI Not Installed

**Symptoms:**
- Error: `gh: command not found`
- Commands fail with "gh not recognized"

**Solution:**

Install the GitHub CLI for your platform:

**Windows:**
```bash
winget install --id GitHub.cli
# or
choco install gh
# or
scoop install gh
```

**macOS:**
```bash
brew install gh
```

**Linux:**
```bash
# Debian/Ubuntu
sudo apt install gh

# Fedora/RHEL
sudo dnf install gh

# Arch
sudo pacman -S github-cli
```

After installation, verify:
```bash
gh --version
```

### GitHub CLI Out of Date

**Symptoms:**
- Commands behave unexpectedly
- Missing features or options

**Solution:**

Update GitHub CLI:

**Windows:**
```bash
winget upgrade --id GitHub.cli
# or
choco upgrade gh
```

**macOS:**
```bash
brew upgrade gh
```

**Linux:**
```bash
# Debian/Ubuntu
sudo apt update && sudo apt upgrade gh

# Fedora/RHEL
sudo dnf upgrade gh
```

Verify update:
```bash
gh --version
```

## Authentication Problems

### Not Authenticated

**Symptoms:**
- Error: `You are not authenticated with GitHub`
- `gh` commands fail with authentication errors

**Solution:**

Authenticate with GitHub:

```bash
gh auth login
```

Follow the prompts:
1. Choose **GitHub.com**
2. Select **HTTPS** as protocol (recommended)
3. Authenticate via **web browser** (easiest) or paste a token

Verify authentication:
```bash
gh auth status
```

Expected output:
```
✓ Logged in to github.com as [username]
✓ Git operations protocol: https
✓ Token scopes: 'repo', 'read:org', 'gist'
```

### Insufficient Permissions

**Symptoms:**
- Error: `Resource not accessible by integration`
- Some operations fail while others work

**Solution:**

Your token may lack required scopes. Re-authenticate with proper scopes:

```bash
gh auth refresh -s repo -s read:org -s gist
```

Or login again:
```bash
gh auth logout
gh auth login
```

### Token Expired

**Symptoms:**
- Previously working commands now fail
- Authentication errors appear suddenly

**Solution:**

Refresh your authentication:

```bash
gh auth refresh
```

If that doesn't work, login again:
```bash
gh auth logout
gh auth login
```

## Command Not Found

### Slash Command Not Recognized

**Symptoms:**
- `/issue` or other commands don't work
- Claude doesn't recognize the command

**Solution:**

1. **Verify command file exists:**
   ```bash
   ls .claude/commands/
   ```
   You should see `issue.md` or other command files.

2. **Check file location:**
   Commands must be in `.claude/commands/` directory relative to your project root.

3. **Install commands:**
   ```bash
   # If not already present, create directory and copy commands
   mkdir -p .claude/commands
   cp /path/to/mm-claude-code-commands/.claude/commands/* .claude/commands/
   ```

4. **Verify file permissions:**
   ```bash
   chmod 644 .claude/commands/*.md
   ```

5. **Restart Claude Code** (if necessary)

### Wrong Directory

**Symptoms:**
- Commands work in one project but not another
- "Command not found" in specific directories

**Solution:**

Slash commands are project-specific. Each project needs its own `.claude/commands/` directory:

```bash
# In your project directory
mkdir -p .claude/commands

# Copy commands from this repository
cp /path/to/mm-claude-code-commands/.claude/commands/* .claude/commands/
```

Or clone and copy:
```bash
git clone https://github.com/mccloudmedia/mm-claude-code-commands.git
cp -r mm-claude-code-commands/.claude .
```

## Git Repository Issues

### Not a Git Repository

**Symptoms:**
- Error: `fatal: not a git repository`
- `/issue work` or git commands fail

**Solution:**

Initialize git repository:

```bash
git init
```

Set up initial commit:
```bash
git add .
git commit -m "Initial commit"
```

### No Remote Repository

**Symptoms:**
- Error: `No GitHub remotes found`
- GitHub operations fail

**Solution:**

1. **Check current remotes:**
   ```bash
   git remote -v
   ```

2. **Add GitHub remote:**
   ```bash
   git remote add origin https://github.com/username/repo.git
   ```

3. **Or create new GitHub repo:**
   ```bash
   gh repo create my-repo --private --source=. --remote=origin
   ```

### Branch Already Exists

**Symptoms:**
- Error: `fatal: A branch named 'issue-5' already exists`
- `/issue work` fails

**Solution:**

1. **Check existing branches:**
   ```bash
   git branch
   ```

2. **Switch to existing branch:**
   ```bash
   git checkout issue-5
   ```

3. **Or delete and recreate:**
   ```bash
   git branch -D issue-5
   /issue work 5
   ```

## Permission Errors

### Access Denied to Repository

**Symptoms:**
- Error: `You don't have permission to access this repository`
- Can't create/modify issues

**Solution:**

1. **Verify repository access:**
   - You must be a collaborator on the repository
   - Or have write access via organization membership

2. **Check with repository owner:**
   - Ask to be added as a collaborator
   - Or fork the repository

3. **Verify authentication:**
   ```bash
   gh auth status
   ```

### File Permission Issues

**Symptoms:**
- Can't create/modify files
- Permission denied errors

**Solution:**

**Linux/macOS:**
```bash
# Fix command file permissions
chmod 644 .claude/commands/*.md

# Fix directory permissions
chmod 755 .claude/commands
```

**Windows:**
- Right-click directory → Properties → Security
- Ensure you have write permissions

## Network Issues

### Connection Timeout

**Symptoms:**
- Commands hang or timeout
- Error: `Connection timed out`

**Solution:**

1. **Check internet connection:**
   ```bash
   ping github.com
   ```

2. **Check GitHub status:**
   - Visit https://www.githubstatus.com/

3. **Try with different network:**
   - Switch to different WiFi
   - Try mobile hotspot
   - Disable VPN temporarily

4. **Configure proxy (if needed):**
   ```bash
   # Set proxy for git
   git config --global http.proxy http://proxy.example.com:8080

   # Set proxy for gh
   export HTTP_PROXY=http://proxy.example.com:8080
   export HTTPS_PROXY=http://proxy.example.com:8080
   ```

### Rate Limit Exceeded

**Symptoms:**
- Error: `API rate limit exceeded`
- Commands fail after many requests

**Solution:**

1. **Check rate limit status:**
   ```bash
   gh api rate_limit
   ```

2. **Wait for reset:**
   - Authenticated: 5,000 requests/hour
   - Unauthenticated: 60 requests/hour
   - Check `reset` time in rate_limit response

3. **Ensure you're authenticated:**
   ```bash
   gh auth status
   ```
   Authenticated users get higher rate limits.

## Common Error Messages

### "Resource not found"

**Possible Causes:**
- Issue number doesn't exist
- Repository doesn't exist
- Wrong repository context

**Solutions:**
1. Verify issue exists: `/issue list`
2. Check repository: `gh repo view`
3. Ensure you're in correct directory

### "Failed to create issue"

**Possible Causes:**
- Authentication issues
- Insufficient permissions
- Invalid label names

**Solutions:**
1. Check authentication: `gh auth status`
2. Verify available labels: `gh label list`
3. Check repository permissions

### "Branch already exists"

**Possible Causes:**
- Previous `/issue work` command created the branch
- Branch exists from manual creation

**Solutions:**
1. Switch to existing branch: `git checkout issue-X`
2. Delete old branch: `git branch -D issue-X`
3. Use different issue number

### "Git diff failed"

**Possible Causes:**
- No commits in repository
- Working directory is clean
- Not in git repository

**Solutions:**
1. Make initial commit if needed
2. Check status: `git status`
3. Verify git repository: `git log`

### "Invalid credentials"

**Possible Causes:**
- Token expired
- Wrong credentials
- Account access changed

**Solutions:**
1. Re-authenticate: `gh auth login`
2. Refresh token: `gh auth refresh`
3. Check account access on GitHub.com

## Getting More Help

### Enable Verbose Output

Get more detailed error information:

```bash
# For git commands
GIT_TRACE=1 git <command>

# For gh commands
GH_DEBUG=1 gh <command>
GH_DEBUG=api gh <command>  # API-specific debugging
```

### Check Logs

**Claude Code logs:**
- Check Claude Code output for error messages
- Look for specific error codes or messages

**Git logs:**
```bash
# Recent git history
git log --oneline -10

# Detailed commit info
git log -p -1
```

**GitHub CLI logs:**
```bash
# View recent gh commands
gh api /user/events
```

### Reset Configuration

If all else fails, reset configurations:

```bash
# Reset gh auth
gh auth logout
gh auth login

# Reset git config (careful!)
git config --global --list
git config --global --unset-all [setting]

# Verify repository
gh repo view
```

### Report Issues

If you encounter a bug or need help:

1. **Check existing issues:**
   - Search repository issues: https://github.com/mccloudmedia/mm-claude-code-commands/issues

2. **Create new issue:**
   ```bash
   gh issue create --repo mccloudmedia/mm-claude-code-commands \
     --title "Bug: [Brief description]" \
     --body "**Description:**
   [Detailed description of the problem]

   **Steps to Reproduce:**
   1. Step 1
   2. Step 2

   **Expected Behavior:**
   [What should happen]

   **Actual Behavior:**
   [What actually happens]

   **Environment:**
   - OS: [Your OS]
   - Claude Code version: [version]
   - gh version: [run: gh --version]
   - git version: [run: git --version]"
   ```

3. **Include diagnostics:**
   ```bash
   # System info
   gh --version
   git --version

   # Auth status
   gh auth status

   # Repository info
   gh repo view
   ```

## Additional Resources

- [GitHub CLI Manual](https://cli.github.com/manual/)
- [Git Documentation](https://git-scm.com/doc)
- [Claude Code Documentation](https://docs.anthropic.com/en/docs/claude-code)
- [GitHub Support](https://support.github.com/)

---

**Still having issues?** Open an issue in this repository with detailed information about your problem, and we'll help you troubleshoot!
