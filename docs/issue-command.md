# Issue Command Reference

Complete guide to using the `/issue` command for GitHub issue management in Claude Code.

## Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Command Reference](#command-reference)
  - [list](#list)
  - [new](#new)
  - [work](#work)
  - [check](#check)
  - [consolidate](#consolidate)
  - [recommend](#recommend)
  - [show](#show)
  - [close](#close)
  - [comment](#comment)
  - [assign](#assign)
  - [status](#status)
  - [search](#search)
  - [help](#help)
- [Workflows](#workflows)
- [Best Practices](#best-practices)

## Overview

The `/issue` command provides comprehensive GitHub issue management directly from Claude Code. It integrates with the GitHub CLI (`gh`) to allow you to create, manage, and track issues without leaving your development environment.

## Prerequisites

- GitHub CLI (`gh`) installed and authenticated
- Active GitHub repository
- Internet connection

## Command Reference

### `list`

List all open issues in the current repository.

**Usage:**
```
/issue list
```

**Output:**
Displays a table with:
- Issue number
- Title
- Labels
- Assignees
- Created date
- State (open/closed)

**Example:**
```
/issue list
```

---

### `new`

Create a new GitHub issue with intelligent analysis and duplicate detection.

**Usage:**
```
/issue new [issue description]
```

**Features:**
- Automatic title generation following best practices
- Smart label inference based on content
- Duplicate detection and consolidation
- Structured issue body with proper sections

**Title Best Practices:**
- Starts with a verb (Add, Fix, Update, Remove, Improve)
- Concise but descriptive (50-72 characters ideal)
- Uses title case or sentence case consistently
- Avoids generic titles like "Bug" or "Feature"

**Label Inference:**

The command automatically infers appropriate labels:

| Keywords | Inferred Label |
|----------|----------------|
| fix, bug, error, crash, broken | `bug` |
| add, new, feature, implement | `enhancement` |
| docs, readme, documentation | `documentation` |
| slow, performance, optimize | `performance` |
| refactor, restructure, clean up | `refactor` |
| UI, UX, design, layout | `ui/ux` |
| update dependency, upgrade, package | `dependencies` |

**Workflow:**

1. Command analyzes your description
2. Generates a well-formatted title and body
3. Infers appropriate labels
4. Searches for similar existing issues
5. If duplicates found, offers to:
   - Update existing issue with new information
   - Create new issue anyway
   - Cancel operation
6. Creates issue with proper formatting

**Examples:**

```
/issue new Fix image upload failing for PNG files over 5MB

# Creates issue with:
# Title: "Fix image upload failing for PNG files over 5MB"
# Labels: bug, priority: high
# Body: Structured with description, steps to reproduce, etc.
```

```
/issue new Add dark mode toggle to settings panel

# Creates issue with:
# Title: "Add dark mode toggle to settings panel"
# Labels: enhancement, ui/ux
# Body: Structured with description and acceptance criteria
```

---

### `work`

Start working on an issue by creating a dedicated branch.

**Usage:**
```
/issue work [issue number or description]
```

**Intelligent Behavior:**

When you provide a description instead of a number, the command:
1. Searches for existing issues matching the description
2. If a matching issue is found, uses that issue
3. If no match is found, creates a new issue first
4. Then proceeds to create the branch

**Workflow:**

1. **If number provided:** Validates issue exists
2. **If description provided:** Searches for matching issue or creates new one
3. Checks current git status
4. Creates a new branch: `issue-[number]-[short-description]`
5. Checks out the new branch
6. Optionally adds a comment to the issue

**Examples:**

```
# Work on existing issue by number
/issue work 5
# Creates and checks out: issue-5-add-dark-mode
```

```
# Work on issue by description (searches first, creates if needed)
/issue work Add authentication system
# Searches for existing "authentication" issues
# If found: uses existing issue
# If not found: creates new issue, then creates branch
```

```
# Another example with description
/issue work Fix login timeout bug
# Intelligently searches for related issues
# Creates issue if needed, then creates branch
```

---

### `check`

Check recent commits and close completed issues.

**Usage:**
```
/issue check
```

**Workflow:**

1. Analyzes recent git commits (last 20)
2. Looks for issue references: `fixes #N`, `closes #N`, `resolves #N`
3. Lists open issues
4. Compares commits with open issues
5. Asks for confirmation before closing issues
6. Closes issues with reference to completing commit

**Example:**

If you committed with message "fixes #5: Implemented dark mode toggle", running `/issue check` will detect this and offer to close issue #5.

---

### `consolidate`

Analyze and consolidate duplicate or related issues.

**Usage:**
```
/issue consolidate
```

**Workflow:**

1. Fetches all open issues with full details
2. Analyzes for consolidation opportunities:
   - Duplicates (nearly identical titles/descriptions)
   - Related functionality (same feature area)
   - Dependencies (issues that overlap)
   - Incremental improvements (multiple small issues)
   - Common labels and subject matter
3. Presents findings with groups of related issues
4. Explains WHY they should be combined
5. Shows proposed consolidated title and body
6. Asks for confirmation
7. If approved:
   - Updates parent issue with comprehensive description
   - Adds references to consolidated issues
   - Closes child issues with consolidation comment
   - Preserves all important information

**Consolidation Criteria:**

**High Priority:**
- Exact or near-duplicate titles
- Same feature request phrased differently
- Bug reports with identical root cause
- Sequential improvements to the same component

**Consider Consolidating:**
- 3+ issues with same primary label addressing related functionality
- Issues that reference each other in comments
- Issues requiring changes to same files/components

**Do NOT Consolidate:**
- Issues in different areas of codebase (unless clearly related)
- Different bug reports (might have different causes)
- Issues at different priority levels (unless true duplicates)
- Issues that can be worked on independently

**Example:**

```
/issue consolidate

# Might find:
# Group 1 (Duplicates):
#   #3: "Add dark mode"
#   #7: "Implement dark theme toggle"
#   #12: "Dark mode feature request"
#
# Recommendation: Consolidate into #3 (oldest, most detailed)
```

---

### `recommend`

Get AI-powered recommendations for which issues to work on next.

**Usage:**
```
/issue recommend
```

**Analysis Factors:**
- Priority labels
- Issue age
- Dependencies and blockers
- Project impact
- Current context

**Output:**

Top 3 issues with 1-2 sentence explanations for each recommendation.

**Example Output:**

```
Top 3 recommended issues:

1. #5: Fix authentication timeout (bug, priority: high)
   High priority bug affecting user experience, should be addressed first.

2. #3: Add dark mode toggle (enhancement, ui/ux)
   Popular feature request with clear scope, good next step after bug fixes.

3. #12: Optimize database queries (performance)
   Improves overall application performance, moderate complexity.
```

---

### `show`

Display detailed information about a specific issue.

**Usage:**
```
/issue show [number]
```

**Output:**
- Issue title
- Issue number and URL
- Status (open/closed)
- Labels
- Assignees
- Description
- Comments
- Timeline

**Example:**
```
/issue show 5
```

---

### `close`

Close an issue with an optional comment.

**Usage:**
```
/issue close [number] [optional: reason]
```

**Workflow:**

1. Asks for confirmation
2. Optionally prompts for closing comment
3. Closes the issue

**Example:**

```
/issue close 5
# Prompts for confirmation and optional comment

/issue close 5 Implemented in commit abc123
# Closes with comment
```

---

### `comment`

Add a comment to an issue.

**Usage:**
```
/issue comment [number] [comment text]
```

**Example:**

```
/issue comment 5 Working on this, should be done by end of week
```

---

### `assign`

Assign an issue to yourself or someone else.

**Usage:**
```
/issue assign [number] [optional: username]
```

**Examples:**

```
/issue assign 5
# Assigns to yourself

/issue assign 5 johndoe
# Assigns to GitHub user 'johndoe'
```

---

### `status`

Update issue labels or status.

**Usage:**
```
/issue status [number]
```

**Workflow:**

1. Shows current labels
2. Asks what labels to add/remove
3. Updates the issue

**Example:**

```
/issue status 5
# Shows current labels, prompts for changes
# You might say: "Add priority: high and remove good first issue"
```

---

### `search`

Search for issues by keyword or filter.

**Usage:**
```
/issue search [query]
```

**Examples:**

```
/issue search authentication
# Finds all issues mentioning "authentication"

/issue search label:bug
# Finds all issues with bug label
```

---

### `help`

Display all available `/issue` subcommands.

**Usage:**
```
/issue help
```

---

## Workflows

### Complete Issue Lifecycle

```bash
# 1. Create issue
/issue new Add user profile page with avatar upload

# 2. Review and prioritize
/issue list
/issue recommend

# 3. Start working
/issue work 8

# 4. Make commits
git commit -m "feat: implement user profile page - fixes #8"

# 5. Check and close
/issue check
```

### Managing Technical Debt

```bash
# Find duplicate issues
/issue consolidate

# Review what needs attention
/issue list

# Get recommendations
/issue recommend

# Tackle high-priority items
/issue work [number]
```

### Organizing Issues

```bash
# Search for related issues
/issue search performance

# Update labels
/issue status 5
# Add: performance, priority: high

# Assign to team members
/issue assign 5 developer-name
```

## Best Practices

### Issue Creation

1. **Be Specific**: Provide clear, detailed descriptions
2. **Use Good Titles**: Start with a verb, be descriptive
3. **Check for Duplicates**: Always search before creating
4. **Add Context**: Include relevant information, screenshots, or examples

### Branch Naming

When using `/issue work [number]`, branches follow the pattern:
```
issue-[number]-[kebab-case-description]
```

Example: `issue-5-add-dark-mode-toggle`

### Commit Messages

Reference issues in commits for automatic tracking:

```bash
# These keywords close issues automatically:
git commit -m "fixes #5: Complete dark mode implementation"
git commit -m "closes #12: Optimize database queries"
git commit -m "resolves #3: Add authentication system"
```

### Label Organization

Use consistent labels across your project:

- **Type**: `bug`, `enhancement`, `documentation`
- **Priority**: `priority: high`, `priority: medium`, `priority: low`
- **Status**: `in-progress`, `blocked`, `needs-review`
- **Area**: `ui/ux`, `backend`, `frontend`, `api`

### Issue Maintenance

1. Regularly run `/issue consolidate` to keep issues organized
2. Use `/issue check` after completing work
3. Update issue statuses to reflect current state
4. Close stale or completed issues promptly

---

For more information, see:
- [GitHub CLI Documentation](https://cli.github.com/manual/)
- [Claude Code Slash Commands](https://docs.anthropic.com/en/docs/claude-code/slash-commands)
- [Troubleshooting Guide](troubleshooting.md)
