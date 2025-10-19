# MM Claude Code Commands

A collection of custom slash commands for Claude Code to enhance your development workflow with GitHub integration and project management features.

## Overview

This repository contains custom slash commands that extend Claude Code's functionality to enhance your development workflow and boost productivity.

**Current Focus:** The `/issue` command provides comprehensive GitHub issue management, allowing you to create, track, and manage issues directly from Claude Code without leaving your development environment.

**Future Vision:** This is just the beginning! Over time, this collection will expand to include additional commands that streamline various aspects of your workflow, including:
- Project scaffolding and setup automation
- Code review and quality analysis tools
- Deployment and CI/CD integration
- Testing and debugging utilities
- Documentation generation
- And much more based on community needs and contributions

These commands integrate seamlessly with the GitHub CLI and other development tools to help you manage your projects more efficiently.

## Prerequisites

- **Claude Code**: Make sure you have Claude Code installed
- **GitHub CLI (gh)**: Required for GitHub integration features
- **Git**: For version control operations

## Installation

### 1. Install GitHub CLI

The GitHub CLI (`gh`) is required for issue management and GitHub integration features.

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

For other installation methods, visit: https://cli.github.com/

### 2. Authenticate GitHub CLI

After installing the GitHub CLI, you need to authenticate:

```bash
gh auth login
```

Follow the interactive prompts to:
1. Choose **GitHub.com** (or GitHub Enterprise if applicable)
2. Select your preferred protocol (HTTPS recommended)
3. Authenticate via web browser or paste an authentication token

Verify authentication:
```bash
gh auth status
```

You should see output confirming you're logged in with the necessary scopes (`repo`, `read:org`, `gist`).

### 3. Install the Slash Commands

Clone this repository or copy the command files to your project:

```bash
# Clone the repository
git clone https://github.com/mccloudmedia/mm-claude-code-commands.git

# Or copy the .claude directory to your project
cp -r mm-claude-code-commands/.claude /path/to/your/project/
```

Alternatively, you can copy individual command files from `.claude/commands/` to your project's `.claude/commands/` directory.

## Available Commands

### `/issue` - GitHub Issue Management

Comprehensive GitHub issue management directly from Claude Code.

**Quick Reference:**
- `/issue list` - List all open issues
- `/issue new [description]` - Create a new issue
- `/issue work [number|description]` - Start working on an issue (creates branch, intelligently handles existing/new issues)
- `/issue check` - Check and close completed issues
- `/issue consolidate` - Consolidate duplicate/related issues
- `/issue recommend` - Get top 3 recommended issues to work on
- `/issue help` - Show detailed help

[View detailed documentation →](docs/issue-command.md)

## Command Documentation

For detailed information about each command, see:

- [Issue Command Reference](docs/issue-command.md) - Complete guide to the `/issue` command
- [Command Development Guide](docs/development.md) - How to create your own custom commands
- [Troubleshooting](docs/troubleshooting.md) - Common issues and solutions

## Usage Examples

### Creating and Working on an Issue

```bash
# Create a new issue
/issue new Add dark mode toggle to settings panel

# Start working on existing issue #5
/issue work 5

# Start working on issue by description (finds existing or creates new)
/issue work Add authentication system

# Check which issues are completed
/issue check
```

### Managing Issues

```bash
# List open issues
/issue list

# Get recommendations on what to work on next
/issue recommend

# Consolidate duplicate issues
/issue consolidate

# Search for specific issues
/issue search authentication
```

## Project Structure

```
mm-claude-code-commands/
├── .claude/
│   └── commands/
│       └── issue.md          # Issue management slash command
├── docs/
│   ├── issue-command.md      # Detailed issue command docs
│   ├── development.md        # Command development guide
│   └── troubleshooting.md    # Troubleshooting guide
├── README.md
└── issue.md                  # Original command source
```

## How It Works

Claude Code slash commands are markdown files placed in the `.claude/commands/` directory. When you type `/commandname`, Claude Code reads the corresponding `.md` file and uses it as instructions for how to handle your request.

These commands integrate with:
- **GitHub CLI (`gh`)**: For all GitHub operations
- **Git**: For branch management and repository operations
- **Claude Code**: For AI-powered analysis and automation

## Contributing

Contributions are welcome! If you have ideas for new commands or improvements:

1. Fork this repository
2. Create a feature branch (`git checkout -b feature/new-command`)
3. Add your command to `.claude/commands/`
4. Document it in the `docs/` directory
5. Submit a pull request

## License

MIT License - See [LICENSE](LICENSE) file for details

## Resources

- [Claude Code Documentation](https://docs.anthropic.com/en/docs/claude-code)
- [GitHub CLI Documentation](https://cli.github.com/manual/)
- [Creating Custom Slash Commands](https://docs.anthropic.com/en/docs/claude-code/slash-commands)

## Support

For issues or questions:
- Open an issue in this repository
- Check the [troubleshooting guide](docs/troubleshooting.md)
- Consult the [Claude Code documentation](https://docs.anthropic.com/en/docs/claude-code)

---

**Note**: These commands require an active internet connection for GitHub operations and rely on proper GitHub CLI authentication.
