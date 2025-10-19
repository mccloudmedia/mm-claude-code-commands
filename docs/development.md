# Command Development Guide

Learn how to create your own custom slash commands for Claude Code.

## Table of Contents

- [Overview](#overview)
- [How Slash Commands Work](#how-slash-commands-work)
- [Creating Your First Command](#creating-your-first-command)
- [Command Structure](#command-structure)
- [Best Practices](#best-practices)
- [Advanced Features](#advanced-features)
- [Testing](#testing)
- [Examples](#examples)

## Overview

Claude Code slash commands are custom instructions stored as Markdown files that tell Claude how to handle specific tasks. They're a powerful way to automate workflows, integrate with external tools, and create reusable development patterns.

## How Slash Commands Work

1. **Storage**: Commands are stored in `.claude/commands/` directory
2. **Naming**: File name becomes the command (e.g., `issue.md` → `/issue`)
3. **Execution**: When you type `/commandname`, Claude reads the `.md` file and uses it as instructions
4. **Context**: Commands have access to all Claude Code tools (Bash, file operations, etc.)

## Creating Your First Command

### Step 1: Create the Command File

```bash
# Create the commands directory if it doesn't exist
mkdir -p .claude/commands

# Create a new command file
touch .claude/commands/mycommand.md
```

### Step 2: Write Command Instructions

Open `mycommand.md` and write instructions for Claude:

```markdown
# My Custom Command

You are helping the user with [specific task].

## When the user runs this command

1. First, do [step 1]
2. Then, do [step 2]
3. Finally, do [step 3]

## Implementation

Use the Bash tool to run:
\`\`\`bash
echo "Hello from my custom command!"
\`\`\`
```

### Step 3: Use the Command

```bash
/mycommand
```

Claude will read your instructions and execute the command accordingly.

## Command Structure

### Basic Template

```markdown
# [Command Name]

Brief description of what this command does.

## Context
- Important context about when to use this
- Prerequisites or requirements
- What tools or APIs this integrates with

## Parse the user's input

Explain how to parse any arguments or subcommands.

### Subcommand 1
Description and implementation details.

### Subcommand 2
Description and implementation details.

## Implementation Details

Specific technical instructions:
- How to use Bash tool
- What files to read/write
- What APIs to call
- Error handling

## Response Format

How to format the output to the user.
```

### Key Sections

1. **Title and Overview**: Clear description of command purpose
2. **Context**: Important background information
3. **Argument Parsing**: How to handle user input
4. **Subcommands**: Different modes or operations
5. **Implementation**: Specific technical steps
6. **Response Format**: How to present results

## Best Practices

### 1. Be Specific and Clear

**Good:**
```markdown
Use the Bash tool to run:
\`\`\`bash
gh issue list --limit 50 --json number,title,labels
\`\`\`
```

**Bad:**
```markdown
Use GitHub to list issues.
```

### 2. Handle Edge Cases

```markdown
## Error Handling

1. If `gh` is not installed:
   - Display installation instructions
   - Provide download link

2. If user is not authenticated:
   - Run `gh auth status` to check
   - Guide them through `gh auth login`

3. If repository is not initialized:
   - Check with `git status`
   - Offer to initialize with `git init`
```

### 3. Provide Examples

```markdown
## Examples

\`\`\`bash
# List all issues
/mycommand list

# Create new issue
/mycommand new "Add feature X"

# Work on issue #5
/mycommand work 5
\`\`\`
```

### 4. Use Structured Output

```markdown
## Response Format

- Be concise and direct
- Show command output when relevant
- Use formatting for readability:
  - Code blocks for commands
  - Lists for steps
  - Tables for data
- Don't create files unless explicitly requested
```

### 5. Integrate with Existing Tools

```markdown
## Tools to Use

- **Bash tool**: For running CLI commands
- **Read tool**: For reading files
- **Write tool**: For creating files
- **Edit tool**: For modifying files
- **Grep tool**: For searching code
```

## Advanced Features

### Subcommands

Create commands with multiple modes:

```markdown
## Parse the user's command

The user will provide one of the following subcommands:

### \`init\` - Initialize the project
Steps to initialize...

### \`build\` - Build the project
Steps to build...

### \`deploy\` - Deploy the project
Steps to deploy...

### \`help\` - Show help
Display all available subcommands.
```

### Conditional Logic

```markdown
## Workflow

1. Check if file exists
2. **If file exists:**
   - Read the file
   - Update existing content
   - Ask user for confirmation
3. **If file doesn't exist:**
   - Create new file
   - Initialize with template
   - Display success message
```

### Integration with External APIs

```markdown
## API Integration

Use the Bash tool to interact with external APIs:

\`\`\`bash
# GitHub API
gh api repos/:owner/:repo/issues

# Custom API with curl
curl -H "Authorization: Bearer $TOKEN" https://api.example.com/data
\`\`\`
```

### State Management

```markdown
## State Management

1. Read current state from file:
   \`\`\`bash
   cat .project-state.json
   \`\`\`

2. Update state:
   \`\`\`bash
   echo '{"status": "in-progress"}' > .project-state.json
   \`\`\`

3. Clean up when done:
   \`\`\`bash
   rm .project-state.json
   \`\`\`
```

## Testing

### Manual Testing

1. Create command file
2. Run command: `/mycommand`
3. Verify output and behavior
4. Test edge cases
5. Test error conditions

### Test Checklist

- [ ] Command executes successfully
- [ ] Handles invalid input gracefully
- [ ] Error messages are clear and helpful
- [ ] Output is properly formatted
- [ ] All subcommands work as expected
- [ ] Integration with external tools works
- [ ] Documentation is clear

## Examples

### Example 1: Simple Utility Command

`.claude/commands/timestamp.md`:

```markdown
# Timestamp Command

Generate a timestamp in various formats.

## Parse the user's input

If user provides a format, use it. Otherwise, use ISO 8601 format.

## Implementation

\`\`\`bash
# ISO 8601 format (default)
date -u +"%Y-%m-%dT%H:%M:%SZ"

# Unix timestamp
date +%s

# Human readable
date "+%Y-%m-%d %H:%M:%S"
\`\`\`

## Response

Display the timestamp in the requested format.
```

Usage: `/timestamp`

### Example 2: Project Setup Command

`.claude/commands/setup.md`:

```markdown
# Project Setup Command

Initialize a new project with standard configuration.

## Steps

1. Check if current directory is empty
2. Ask user for project details:
   - Project name
   - Project type (node, python, go, etc.)
   - License
3. Initialize based on type:
   - Create directory structure
   - Initialize package manager
   - Create config files
   - Initialize git
   - Create README

## Implementation for Node.js

\`\`\`bash
# Initialize npm
npm init -y

# Create directories
mkdir -p src tests docs

# Create .gitignore
cat > .gitignore << EOF
node_modules/
dist/
.env
EOF

# Initialize git
git init
\`\`\`

## Response

Display summary of created files and next steps.
```

Usage: `/setup`

### Example 3: Code Review Command

`.claude/commands/review.md`:

```markdown
# Code Review Command

Perform automated code review on recent changes.

## Steps

1. Get git diff of recent changes
2. Analyze code for:
   - Syntax errors
   - Code style issues
   - Security vulnerabilities
   - Performance concerns
   - Best practice violations
3. Generate review report

## Implementation

\`\`\`bash
# Get recent changes
git diff HEAD~1

# Run linters if available
npm run lint 2>/dev/null || true
\`\`\`

## Analysis

For each file:
1. Check syntax
2. Look for common issues
3. Suggest improvements
4. Rate complexity

## Report Format

\`\`\`markdown
## Code Review Report

### Summary
- X files changed
- Y issues found
- Z suggestions

### Issues
1. [File:Line] - Description
2. [File:Line] - Description

### Suggestions
- Suggestion 1
- Suggestion 2
\`\`\`
```

Usage: `/review`

## Publishing Your Commands

### Sharing with Team

1. Commit `.claude/commands/` to your repository
2. Team members can pull and use immediately
3. Document commands in project README

### Creating a Command Library

1. Create a dedicated repository
2. Organize commands by category
3. Provide comprehensive documentation
4. Include installation instructions
5. Add examples and use cases

### Repository Structure

```
my-claude-commands/
├── .claude/
│   └── commands/
│       ├── issue.md
│       ├── deploy.md
│       └── review.md
├── docs/
│   ├── issue-command.md
│   ├── deploy-command.md
│   └── review-command.md
├── examples/
│   └── usage-examples.md
└── README.md
```

## Resources

- [Claude Code Documentation](https://docs.anthropic.com/en/docs/claude-code)
- [Slash Commands Guide](https://docs.anthropic.com/en/docs/claude-code/slash-commands)
- [GitHub CLI Manual](https://cli.github.com/manual/)

## Tips and Tricks

1. **Start Simple**: Begin with basic commands and add complexity gradually
2. **Test Thoroughly**: Try various inputs and edge cases
3. **Document Well**: Clear documentation helps users and maintainers
4. **Use Version Control**: Track command changes in git
5. **Get Feedback**: Have others test your commands
6. **Iterate**: Improve based on real-world usage
7. **Stay Focused**: Each command should do one thing well

---

Ready to create your own commands? Start with a simple utility and build from there!
