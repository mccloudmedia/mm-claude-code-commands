# GitHub Issue Management Command

You are helping the user manage GitHub issues for the current repository using the GitHub CLI (`gh`).

**IMPORTANT CONTEXT:**
- When the user says "issue", they are referring to GitHub issues in THIS repository
- ALWAYS use the `gh` CLI tool via the Bash tool for all GitHub operations
- All issue references are for the current project repository
- Issue numbers should be referenced as `#N` where N is the issue number

## Parse the user's command

The user will provide one of the following subcommands after `/issue`:

### `list` - List all open issues
List all currently open issues in the repository with their numbers, titles, and labels.

**Implementation:**
```bash
gh issue list --limit 50
```

For better readability, you may also show:
- Issue state (open/closed)
- Assignees
- Labels
- Created date

### `new [issue description]` - Create a new issue
Create a new GitHub issue with the provided description. ONLY create the issue entry - do not implement or work on it.

**IMPORTANT:** Before creating, check if a similar issue already exists to prevent duplicates.

**Steps:**
1. Parse the issue description from the command
2. **Analyze and format the issue following best practices:**
   - **Title Best Practices:**
     - Start with a verb (e.g., "Add", "Fix", "Update", "Remove", "Improve")
     - Be concise but descriptive (50-72 characters ideal)
     - Use title case or sentence case consistently
     - Avoid generic titles like "Bug" or "Feature"
     - Examples:
       - Good: "Add dark mode toggle to settings panel"
       - Bad: "dark mode"
       - Good: "Fix image upload failing for PNG files over 5MB"
       - Bad: "Upload broken"
   - **Infer appropriate labels** based on issue content:
     - `bug` - Something isn't working correctly
     - `enhancement` / `feature` - New functionality or improvement
     - `documentation` - Updates to docs, README, or comments
     - `performance` - Speed or optimization improvements
     - `refactor` - Code restructuring without behavior change
     - `ui/ux` - User interface or experience improvements
     - `dependencies` - Dependency updates or issues
     - `question` - Further information or clarification needed
     - `good first issue` - Good for newcomers (if simple)
     - `help wanted` - Extra attention needed
     - Priority labels: `priority: high`, `priority: medium`, `priority: low`
   - **Structure the issue body** with:
     - Clear description of the problem or feature
     - Steps to reproduce (for bugs)
     - Expected vs actual behavior (for bugs)
     - Acceptance criteria (for features)
     - Relevant context or screenshots
3. Extract key terms from the title/description
4. **Search for existing similar issues:**
   - Use `gh issue list --search "[key terms]"` to find potential matches
   - Check both open and closed issues
5. **If similar issue(s) found:**
   - Display the matching issue(s) to the user
   - Ask if they want to:
     - Update the existing issue with new information
     - Create a new issue anyway
     - Cancel the operation
6. **If user chooses to update existing issue:**
   - Add a comment with the new information using `gh issue comment`
   - Update labels if new ones are identified
   - Optionally reopen if closed
   - Display the updated issue
7. **If no similar issues found OR user wants to create new:**
   - If the description is too brief, ask for more details
   - Show the proposed title and labels to the user
   - Allow user to confirm or modify before creating
   - Create the issue using `gh issue create` with proper labels
   - Display the created issue number and URL

**Implementation:**
```bash
# First, get existing labels to ensure we use valid ones
gh label list

# Search for similar issues
gh issue list --search "[key terms from title]" --limit 10
gh issue list --state closed --search "[key terms from title]" --limit 10

# If match found and user confirms update:
gh issue comment [number] --body "Additional information: [new details]"
gh issue edit [number] --add-label "label1,label2"
# Optionally reopen if closed:
gh issue reopen [number]

# If creating new with proper formatting and labels:
gh issue create \
  --title "Verb-based Descriptive Title" \
  --body "## Description
[Clear description]

## Steps to Reproduce (for bugs)
1. Step one
2. Step two

## Expected Behavior
[What should happen]

## Actual Behavior
[What actually happens]

## Acceptance Criteria (for features)
- [ ] Criterion 1
- [ ] Criterion 2" \
  --label "bug,priority: high" \
  --assignee @me
```

**Label Inference Guidelines:**
- Keywords "fix", "bug", "error", "crash", "broken" → `bug`
- Keywords "add", "new", "feature", "implement" → `enhancement`
- Keywords "docs", "readme", "documentation" → `documentation`
- Keywords "slow", "performance", "optimize" → `performance`
- Keywords "refactor", "restructure", "clean up" → `refactor`
- Keywords "UI", "UX", "design", "layout" → `ui/ux`
- Keywords "update dependency", "upgrade", "package" → `dependencies`
- Simple issues with clear solutions → `good first issue`

### `work [issue number or description]` - Start working on an issue
Check if the issue exists, set it to working status, and create a new branch for it.

**Steps:**
1. Parse the issue number or search for the issue by description
2. If issue doesn't exist:
   - Create the issue first using `gh issue create`
   - Display the new issue number
3. Check current git branch and status
4. Create a new branch with format: `issue-[number]-[short-description]`
5. Optionally, add a comment to the issue indicating work has started
6. Checkout the new branch

**Implementation:**
```bash
# Check if issue exists
gh issue view [number]

# If not exists, create it
gh issue create --title "..." --body "..."

# Create and checkout branch
git checkout -b issue-[number]-[description]

# Optional: Add comment
gh issue comment [number] --body "Started working on this issue"
```

### `check` - Check and close completed issues
Look at recent commits and check against open issues. Close any issues that have been completed.

**Steps:**
1. Get recent commits using `git log`
2. Look for issue references in commit messages (e.g., "fixes #123", "closes #456", "resolves #789")
3. List open issues using `gh issue list`
4. Compare commit messages with open issues
5. For each issue mentioned in commits, check if it's been resolved
6. Ask user for confirmation before closing issues, or report which issues appear completed

**Implementation:**
```bash
# Get recent commits
git log --oneline -20

# List open issues
gh issue list

# Parse commit messages for issue references
# Close issues if appropriate
gh issue close [number] --comment "Completed in commit [hash]"
```

### `help` - List all issue commands
Display all available `/issue` subcommands with brief descriptions.

**Output:**
```
Available /issue commands:

  /issue list              - List all open issues
  /issue new [desc]        - Create a new issue (entry only)
  /issue work [num/desc]   - Start working on an issue (create branch)
  /issue check             - Check recent commits and close completed issues
  /issue consolidate       - Analyze and consolidate duplicate/related issues
  /issue show [number]     - Show detailed information about an issue
  /issue close [number]    - Close an issue with optional comment
  /issue comment [number]  - Add a comment to an issue
  /issue assign [number]   - Assign an issue to yourself or someone else
  /issue status [number]   - Update issue status/labels
  /issue search [query]    - Search issues by keyword
  /issue find-bugs         - Analyze code for bugs and improvement opportunities
  /issue find-features     - Suggest enhancements and new features
  /issue recommend         - Recommend top 3 issues to work on next
  /issue help              - Show this help message
```

### `recommend` - Recommend top issues to work on
Analyze open issues and recommend the top 3 that should be worked on next with concise explanations.

**Steps:**
1. Get all open issues with `gh issue list`
2. Analyze issues based on:
   - Labels (priority, bug, enhancement, etc.)
   - Age (older issues may need attention)
   - Dependencies or blockers
   - Impact on the project
3. Present top 3 recommendations with brief (1-2 sentence) explanations for each

Keep the output concise and actionable.

### `consolidate` - Consolidate duplicate or related issues
Analyze all open issues and identify opportunities to combine related or duplicate issues together.

**Steps:**
1. **Fetch all open issues:**
   ```bash
   gh issue list --limit 100 --json number,title,body,labels,createdAt,comments
   ```

2. **Analyze issues for consolidation opportunities based on:**
   - **Duplicates:** Nearly identical titles or descriptions
   - **Related functionality:** Issues addressing the same feature area
   - **Dependencies:** Issues that depend on each other or overlap
   - **Incremental improvements:** Multiple small issues that could be one larger issue
   - **Common labels:** Issues sharing multiple labels and subject matter
   - **Temporal proximity:** Related issues created around the same time

3. **For each consolidation opportunity, identify:**
   - Which issues should be combined
   - Which issue should be the "parent" (usually the oldest, most detailed, or highest priority)
   - What the consolidated issue title should be
   - What information needs to be preserved from each issue

4. **Present findings to the user:**
   - Show groups of issues that should be consolidated
   - Explain WHY they should be combined (1-2 sentences per group)
   - Display the proposed consolidated title and body
   - Ask for confirmation before proceeding

5. **If user approves consolidation:**
   - Update the parent issue:
     - Add a comprehensive description incorporating all related issues
     - Update the body with references to consolidated issues: "Consolidates: #X, #Y, #Z"
     - Add all relevant labels from consolidated issues
     - Add a comment listing what was consolidated
   - For each child issue being consolidated:
     - Add a comment: "This issue has been consolidated into #[parent] for better tracking."
     - Close the issue with `gh issue close`
     - Optionally add a `duplicate` or `consolidated` label before closing

6. **Display summary:**
   - Show which issues were consolidated
   - Provide link to the consolidated parent issue(s)
   - Show count of issues reduced

**Implementation:**
```bash
# Get all open issues with full details
gh issue list --limit 100 --json number,title,body,labels,createdAt,comments

# After user confirms consolidation, update parent issue
gh issue edit [parent-number] \
  --body "## Original Description
[parent description]

## Consolidated Issues
This issue consolidates:
- #X: [title]
- #Y: [title]
- #Z: [title]

## Combined Requirements
[merged requirements and details]" \
  --add-label "label1,label2"

gh issue comment [parent-number] --body "Consolidated issues #X, #Y, and #Z into this issue for better tracking."

# Close child issues
for issue in X Y Z; do
  gh issue comment $issue --body "This issue has been consolidated into #[parent] for better tracking and to reduce duplication."
  gh issue close $issue
done
```

**Consolidation Criteria:**
- **High priority to consolidate:**
  - Exact or near-duplicate titles
  - Same feature request phrased differently
  - Bug reports with identical root cause
  - Sequential improvements to the same component

- **Consider consolidating:**
  - 3+ issues with the same primary label addressing related functionality
  - Issues that reference each other in comments
  - Issues that would require changes to the same files/components

- **Do NOT consolidate:**
  - Issues in different areas of the codebase unless clearly related
  - Different bug reports even if they seem similar (might have different causes)
  - Issues at different priority levels unless they're true duplicates
  - Issues that can be worked on independently

**Best Practices:**
- Preserve ALL important information from consolidated issues
- Keep the most descriptive and well-formatted issue as the parent
- Clearly document what was consolidated and why
- Don't over-consolidate - some related issues are better tracked separately
- Be conservative - when in doubt, ask the user before consolidating

### `show [number]` - Show issue details
Display detailed information about a specific issue.

**Implementation:**
```bash
gh issue view [number]
```

### `close [number]` - Close an issue
Close an issue with an optional comment.

**Steps:**
1. Ask for confirmation if not provided
2. Optionally ask for a closing comment
3. Close the issue

**Implementation:**
```bash
gh issue close [number] --comment "Closing comment here"
```

### `comment [number] [text]` - Add a comment
Add a comment to an issue.

**Implementation:**
```bash
gh issue comment [number] --body "Comment text"
```

### `assign [number] [assignee]` - Assign an issue
Assign an issue to a user. If no assignee provided, assign to yourself.

**Implementation:**
```bash
# Assign to self
gh issue edit [number] --add-assignee @me

# Assign to specific user
gh issue edit [number] --add-assignee [username]
```

### `status [number]` - Update issue status
Update issue labels or status.

**Steps:**
1. Show current labels
2. Ask what labels to add/remove
3. Update the issue

**Implementation:**
```bash
gh issue edit [number] --add-label "label1,label2" --remove-label "label3"
```

### `search [query]` - Search issues
Search for issues by keyword or filter.

**Implementation:**
```bash
gh issue list --search "[query]"
```

### `find-bugs` - Analyze code for bugs and improvement opportunities
Perform a comprehensive code analysis to identify bugs, potential issues, and coding practice improvements.

**IMPORTANT:** This command analyzes code and creates issues for findings - it does NOT implement fixes.

**Steps:**
1. **Analyze the codebase:**
   - Use Read and Grep tools to examine code files
   - Look for common bug patterns:
     - Unhandled errors or exceptions
     - Null/undefined reference possibilities
     - Race conditions or async issues
     - Memory leaks or resource management issues
     - Security vulnerabilities (SQL injection, XSS, etc.)
     - Logic errors or edge cases not handled
     - Dead code or unreachable statements
   - Identify coding practice improvements:
     - Code duplication (DRY violations)
     - Poor error handling
     - Missing input validation
     - Inconsistent naming conventions
     - Missing or inadequate type checking
     - Performance anti-patterns
     - Lack of documentation or comments where needed

2. **Categorize findings:**
   - **Critical bugs:** Security issues, data loss risks, crashes
   - **High priority bugs:** Functionality broken, major errors
   - **Medium priority bugs:** Edge cases, minor errors
   - **Low priority bugs:** Code quality issues, optimization opportunities

3. **Check for existing issues:**
   - Search existing issues to avoid duplicates
   - Use `gh issue list --search` with relevant keywords

4. **Present findings to user:**
   - Show findings organized by priority
   - Include:
     - File and line number references
     - Description of the issue
     - Why it's a problem (impact/risk)
     - Suggested priority level
   - Limit initial display to top 5-10 most important findings

5. **Ask user which issues to create:**
   - Let user select which findings should become issues
   - Or offer to create issues for all high/critical findings

6. **Create issues for selected findings:**
   - Use `/issue new` workflow for each selected finding
   - Title format: "Fix [brief description]"
   - Labels: `bug`, appropriate priority label
   - Body includes:
     - File/line references
     - Detailed description
     - Why it's important
     - Suggested approach (if applicable)

**Analysis Scope:**
- Focus on source code files (exclude node_modules, build outputs, etc.)
- Prioritize core application logic
- Consider project type (web app, API, library, etc.)

**Output Format:**
```
Code Analysis Results
====================

CRITICAL ISSUES (0 found)
[List critical bugs]

HIGH PRIORITY (2 found)
1. [File:Line] - Unhandled promise rejection in authentication flow
   Impact: Could cause silent failures during login
   Recommendation: Add .catch() handler and user feedback

2. [File:Line] - SQL injection vulnerability in search query
   Impact: Security risk - attackers could access database
   Recommendation: Use parameterized queries

MEDIUM PRIORITY (3 found)
[List medium priority issues]

RECOMMENDATIONS (5 found)
[List code quality improvements]

Would you like me to create issues for these findings?
```

**Implementation Notes:**
- Use Grep tool to search for anti-patterns
- Use Read tool to examine specific files
- Be thorough but prioritize actionable findings
- Don't overwhelm user - focus on highest impact issues first

### `find-features` - Suggest enhancements and new features
Analyze the application to suggest valuable enhancements and new features that would improve the product.

**IMPORTANT:** This command suggests and creates issues for features - it does NOT implement them.

**Steps:**
1. **Analyze the application:**
   - Use Read and Grep tools to understand:
     - Current features and functionality
     - Code structure and architecture
     - User-facing components
     - API endpoints or interfaces
     - Configuration and settings
     - Documentation and README
   - Look for:
     - Incomplete features (TODOs, FIXMEs)
     - Missing common functionality
     - User experience gaps
     - Accessibility issues
     - Performance optimization opportunities
     - Integration opportunities
     - Documentation gaps

2. **Review existing issues:**
   - Check open issues for feature requests
   - Identify patterns in existing enhancement requests
   - Use `gh issue list --label enhancement`

3. **Categorize suggestions:**
   - **High impact enhancements:**
     - Features that significantly improve core functionality
     - User experience improvements affecting main workflows
     - Performance optimizations for bottlenecks
     - Accessibility improvements
   - **Medium impact enhancements:**
     - Nice-to-have features complementing existing functionality
     - UI/UX polish and refinements
     - Developer experience improvements
   - **New feature ideas:**
     - Complementary features expanding capabilities
     - Integration with popular tools/services
     - Advanced features for power users

4. **Prioritize by impact:**
   - Consider:
     - User benefit (how many users affected, how much impact)
     - Implementation complexity vs. value
     - Alignment with project goals
     - Dependencies on other features
   - Calculate rough impact score for each suggestion

5. **Present findings to user:**
   - Show top 5-10 suggestions organized by impact
   - Include for each:
     - Feature/enhancement description
     - Why it's valuable (user benefit)
     - Estimated impact level
     - Potential implementation approach
     - Any dependencies or prerequisites

6. **Ask user which suggestions to create as issues:**
   - Let user select which suggestions to track
   - Offer to create issues for high-impact items

7. **Create issues for selected suggestions:**
   - Use `/issue new` workflow
   - Title format: "Add [feature]" or "Improve [existing feature]"
   - Labels: `enhancement`, `feature`, or relevant labels
   - Body includes:
     - Clear description of the feature/enhancement
     - User benefit and use cases
     - Acceptance criteria
     - Optional: Technical approach suggestions

**Analysis Focus:**
- User experience and usability
- Feature completeness
- Performance and optimization
- Accessibility and inclusivity
- Developer experience
- Integration and extensibility

**Output Format:**
```
Feature Analysis Results
========================

HIGH IMPACT ENHANCEMENTS (3 found)

1. Add user authentication with OAuth providers
   Benefit: Users can sign in with existing accounts, reducing friction
   Impact: High - affects all users, improves security and UX
   Approach: Integrate popular OAuth providers (Google, GitHub, etc.)

2. Implement dark mode theme
   Benefit: Better user experience in low-light environments
   Impact: High - frequently requested, improves accessibility
   Approach: Add theme toggle, CSS variables for colors

3. Add export functionality for data
   Benefit: Users can backup and use their data externally
   Impact: High - critical feature for user data ownership
   Approach: Support JSON, CSV export formats

MEDIUM IMPACT ENHANCEMENTS (4 found)
[List medium impact suggestions]

NEW FEATURE IDEAS (3 found)
[List new feature suggestions]

Which suggestions would you like me to create as issues?
```

**Best Practices:**
- Focus on user value, not just technical capabilities
- Consider project scope and goals
- Be realistic about complexity vs. benefit
- Suggest features that align with existing functionality
- Include both quick wins and strategic additions

## General Guidelines

1. **Always use `gh` CLI**: All GitHub operations should use the `gh` command
2. **Current repository**: All commands operate on the current repository
3. **Error handling**: If `gh` is not installed or authenticated, provide clear instructions
4. **Concise output**: Keep responses brief and actionable
5. **Confirmation**: Ask for confirmation before destructive operations (closing issues, etc.)
6. **Branch naming**: Use format `issue-[number]-[kebab-case-description]`
7. **Issue references**: When referencing issues in commits, use "fixes #N", "closes #N", or "resolves #N"

## Response Format

- Be concise and direct
- Show command output when relevant
- Provide issue URLs for easy access
- Use formatting for readability (code blocks, lists, etc.)
- Don't create files or extensive documentation unless explicitly requested
