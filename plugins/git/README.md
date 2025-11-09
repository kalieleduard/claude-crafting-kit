# Git Workflow Plugin

**Agent-based git automation with Haiku for fast, efficient operations**

## Overview

The Git Workflow Plugin provides streamlined git commands that leverage specialized agents running on Claude Haiku for fast, token-efficient automation. Instead of manual git operations, use simple slash commands that delegate to purpose-built agents.

## Architecture

This plugin uses an **agent-based architecture**:

- **Commands** (`/commit`, `/commit-push-pr`, `/clean`) - Simple dispatchers that invoke agents
- **Agents** (`git-commit-agent`, `git-pr-creation-agent`, `git-clean-agent`) - Specialized workers running on Haiku
- **Benefits**:
  - ⚡ **Fast**: Haiku agents complete tasks quickly
  - 💰 **Economical**: Lower token costs with Haiku
  - 🎯 **Focused**: Each agent has a single, well-defined responsibility
  - 🔄 **Parallel-ready**: Agents can run concurrently

## Commands

### `/git:commit`

Creates a git commit with conventional commit message.

**What it does:**
1. Analyzes staged and unstaged changes
2. Generates conventional commit message
3. Stages relevant files
4. Creates the commit

**Usage:**

```bash
/git:commit
```

**Example:**

```bash
# Make some changes
# Then:
/git:commit

# Agent analyzes changes and creates:
# feat(auth): add JWT authentication
```

**Delegates to**: `git-commit-agent` (Haiku)

---

### `/git:commit-push-pr`

Complete workflow: commit → push → create PR to dev branch.

**What it does:**
1. Creates conventional commit
2. Pushes to origin
3. Creates comprehensive PR with summary and test plan

**Usage:**

```bash
/git:commit-push-pr
```

**Example:**

```bash
# Complete your feature
# Then:
/git:commit-push-pr

# Agents will:
# 1. Commit your changes
# 2. Push to remote
# 3. Create PR with detailed description
# 4. Return PR URL
```

**Delegates to**: `git-commit-agent` + `git-pr-creation-agent` (Haiku)

**Requirements**:
- GitHub CLI (`gh`) installed and authenticated
- Repository with remote named `origin`

---

### `/git:clean`

Cleans up local branches deleted from remote.

**What it does:**
1. Identifies branches marked as `[gone]`
2. Removes associated worktrees
3. Deletes stale branches

**Usage:**

```bash
/git:clean
```

**Example:**

```bash
# After PRs are merged
/git:clean

# Agent reports:
# Processing branch: feature/old-feature
#   Removing worktree: /path/to/worktree
#   Deleting branch: feature/old-feature
```

**Delegates to**: `git-clean-agent` (Haiku)

---

## Agent Details

### git-commit-agent

**Model**: Claude Haiku
**Purpose**: Create conventional commits

**Process**:
1. `git status` - See changes
2. `git diff HEAD` - Understand modifications
3. `git add .` - Stage files
4. `git commit -m "<type>(<scope>): <subject>"` - Create commit
5. `git status` - Verify success

**Conventional Commit Types**:
- `feat` - New feature
- `fix` - Bug fix
- `docs` - Documentation
- `style` - Code style (formatting)
- `refactor` - Code restructuring
- `perf` - Performance improvement
- `test` - Tests
- `chore` - Build/tooling changes
- `ci` - CI/CD changes
- `build` - Build system changes

---

### git-pr-creation-agent

**Model**: Claude Haiku
**Purpose**: Create comprehensive PRs

**Process**:
1. `gh auth status` - Verify authentication
2. `git branch --show-current` - Get current branch
3. `git log dev..HEAD` - Analyze commits
4. Generate title + body
5. `gh pr create` - Create PR

**PR Structure**:
```markdown
## Summary
[2-3 sentences]

## Key Features
- Feature 1
- Feature 2

## Changes Included
**New Features:**
- ✅ Description

**Bug Fixes:**
- ✅ Description

## Technical Details
**Endpoints/Changes:**
- List of changes

**Database Changes:**
- Schema updates

**Configuration Updates:**
- Environment variables
```

---

### git-clean-agent

**Model**: Claude Haiku
**Purpose**: Clean up stale branches

**Process**:
1. `git branch -v` - List branches
2. `git worktree list` - Find worktrees
3. Remove worktrees for `[gone]` branches
4. `git branch -D` - Delete stale branches
5. Report results

**Safe operation**: Only removes branches already deleted on remote

---

## Conventional Commits

All commits follow the **Conventional Commits** specification:

```
<type>(<scope>): <subject>
```

**Rules**:
- Use imperative mood ("add" not "added")
- Don't capitalize first letter
- No period at end
- Max 72 characters
- Scope is optional but recommended

**Examples**:
```bash
feat(auth): add password hashing with bcrypt
fix(api): handle null user ID in session validation
refactor(db): extract query builders into utilities
test(user): add integration tests for profile updates
docs(readme): update installation instructions
```

---

## Workflow Patterns

### Quick Commit

```bash
# Write code
/git:commit
# Continue development
```

### Feature Branch

```bash
# Develop feature with multiple commits
/git:commit  # First commit
# More changes
/git:commit  # Second commit
# Ready for review
/git:commit-push-pr
```

### Maintenance

```bash
# After several PRs merged
/git:clean
# Clean workspace ready
```

---

## Best Practices

### Using `/git:commit`
- Let the agent analyze and generate commit messages
- Trust the conventional commit format
- Use for routine commits during development
- Review suggested message before confirming

### Using `/git:commit-push-pr`
- Use when feature is complete and tested
- Agent analyzes full branch history for PR description
- Review PR description and edit if needed
- Minimizes context switching

### Using `/git:clean`
- Run periodically after merging PRs
- Safe to run - only removes remote-deleted branches
- Keeps local branch list clean
- Helps maintain tidy repository

---

## Requirements

- **Git** - Installed and configured
- **GitHub CLI (`gh`)** - For `/git:commit-push-pr` command
  - Install: `brew install gh` (macOS) or see [GitHub CLI](https://cli.github.com/)
  - Authenticate: `gh auth login`
- **Repository** - Git repository with remote

---

## Troubleshooting

### `/git:commit` creates empty commit

**Issue**: No changes to commit

**Solution**:
- Ensure you have unstaged or staged changes
- Run `git status` to verify

### `/git:commit-push-pr` fails to create PR

**Issue**: `gh pr create` command fails

**Solution**:
- Install GitHub CLI: `brew install gh`
- Authenticate: `gh auth login`
- Ensure repository has GitHub remote

### `/git:clean` doesn't find branches

**Issue**: No branches marked as `[gone]`

**Solution**:
- Run `git fetch --prune` to update remote tracking
- Branches must be deleted from remote

---

## File Structure

```
git/
├── .claude-plugin/
│   └── plugin.json
├── agents/                    # Haiku agents (NEW!)
│   ├── git-commit-agent.md
│   ├── git-pr-creation-agent.md
│   └── git-clean-agent.md
├── commands/                  # Dispatchers
│   ├── commit.md
│   ├── commit-push-pr.md
│   └── clean.md
└── README.md (this file)
```

---

## Why Agent-Based?

**Previous Architecture** (Skills):
- Commands invoked skills directly
- Ran on main Sonnet model
- Higher token costs
- Slower execution

**New Architecture** (Agents):
- Commands dispatch to specialized agents
- Agents run on Haiku (fast + economical)
- Clear separation of concerns
- Parallel execution ready
- Lower operational costs

---

## When to Use SKILL vs Agents

This plugin uses both **Skills** and **Agents** for different purposes. Understanding when to use each helps optimize your workflow.

### Skills (`skills/` directory)

**Use Skills when:**
- You need comprehensive guidance and detailed context
- You want to understand best practices and patterns
- You're learning or need examples
- You need flexible, context-aware assistance
- You want to invoke capabilities directly in conversation

**Characteristics:**
- Comprehensive documentation with examples
- Detailed best practices and patterns
- Context-aware guidance
- Can be invoked directly: `@git-commit` or `@git-pr-creation`
- Provide extensive explanations and reasoning

**Example Use Cases:**
```bash
# Direct skill invocation for detailed guidance
@git-commit "I've made changes to authentication, help me commit"

# When you need to understand commit message conventions
@git-pr-creation "How should I structure my PR description?"
```

### Agents (`agents/` directory)

**Use Agents when:**
- You want fast, automated execution
- You need a quick, focused task completed
- You're using commands (`/git:commit`, `/git:commit-push-pr`, `/git:clean`)
- You want economical, token-efficient operations
- You trust the automation to handle the task

**Characteristics:**
- Fast execution on Haiku model
- Focused, single-purpose workers
- Minimal explanation (execution-focused)
- Lower token costs
- Optimized for speed and efficiency

**Example Use Cases:**
```bash
# Quick automated commit
/git:commit

# Complete workflow automation
/git:commit-push-pr

# Automated cleanup
/git:clean
```

### Decision Matrix

| Scenario | Use | Why |
|---------|-----|-----|
| Quick commit after coding | Agent (`/git:commit`) | Fast, automated |
| Learning commit conventions | Skill (`@git-commit`) | Detailed guidance |
| Creating PR with full context | Agent (`/git:commit-push-pr`) | Automated workflow |
| Understanding PR best practices | Skill (`@git-pr-creation`) | Comprehensive examples |
| Routine cleanup | Agent (`/git:clean`) | Fast execution |
| Troubleshooting git issues | Skill (direct invocation) | Detailed explanations |

### Summary

- **Commands → Agents**: Fast, automated execution
- **Direct Skill Invocation**: Comprehensive guidance and learning
- **Both have their place**: Use agents for speed, skills for depth

---

## Skill Relationships

Skills in this plugin are designed to work together, with clear relationships and dependencies. Understanding these relationships helps you use them effectively.

### Git Workflow Skills

#### `git-commit` → `git-pr-creation`

**Relationship**: Sequential workflow

The `git-commit` skill explicitly references `git-pr-creation` as the next step:

```markdown
## Related Skills

→ Use `git-pr-creation` skill when you're ready to create a pull request 
  after committing your changes
```

**Usage Pattern:**
1. Use `@git-commit` to create commits during development
2. Use `@git-pr-creation` when ready to open a PR

**Example:**
```bash
# Step 1: Commit your changes
@git-commit "commit authentication changes"

# Step 2: Create PR (skill suggests this)
@git-pr-creation "create PR for auth feature"
```

#### `git-pr-creation` → `git-commit`

**Relationship**: Prerequisite reference

The `git-pr-creation` skill references `git-commit` as a prerequisite:

```markdown
## Related Skills

→ Use `git-commit` skill for creating conventional commit messages 
  before opening a pull request
```

**Usage Pattern:**
- If you haven't committed yet, `git-pr-creation` will guide you to commit first
- Ensures commits follow conventional commit standards before PR creation

### Cross-Plugin Skill Relationships

Skills can reference skills from other plugins for comprehensive workflows:

#### Example: `git-commit` → `project-workflow`

The `git-commit` skill references the `project-workflow` skill from the `architecture-design` plugin:

```markdown
**For pre-commit checklist, quality gates, and Bun-specific commands, 
see `project-workflow` skill from architecture-design plugin**
```

**Usage Pattern:**
- Use `@git-commit` for commit message generation
- Use `@project-workflow` for pre-commit checks and quality gates
- Skills complement each other in the workflow

### Skill Relationship Types

1. **Sequential** (`git-commit` → `git-pr-creation`)
   - One skill naturally follows another
   - Skills reference the next step in the workflow

2. **Prerequisite** (`git-pr-creation` → `git-commit`)
   - One skill requires another to be used first
   - Skills guide you to complete prerequisites

3. **Complementary** (`git-commit` ↔ `project-workflow`)
   - Skills work together but aren't sequential
   - Each handles a different aspect of the same workflow

4. **Informational** (`backend-engineer` → `golang-engineer`)
   - Skills provide context about related capabilities
   - Helps you understand when to use related skills

### Best Practices for Skill Relationships

1. **Follow the Chain**: When a skill references another, consider using it
2. **Read Related Skills**: Check the "Related Skills" section in each skill
3. **Understand Dependencies**: Know which skills are prerequisites
4. **Combine Skills**: Use multiple skills together for complete workflows

### Skill Relationship Graph

```
git-commit
    ↓ (sequential)
git-pr-creation
    ↑ (prerequisite reference)

git-commit
    ↔ (complementary)
project-workflow (from architecture-design plugin)
```

---

## Tips

- **Combine workflows**: Use `/git:commit` during development, `/git:commit-push-pr` when ready
- **Trust the agents**: Haiku agents are optimized for git operations
- **Regular cleanup**: Run `/git:clean` weekly
- **Review before pushing**: Always verify commit messages and changes
- **Use skills for learning**: Invoke skills directly when you need detailed guidance
- **Use agents for speed**: Commands delegate to agents for fast execution

---

## Version

2.0.0 - Agent-based architecture

## Migration from 1.x

If you were using the previous skills-based version:

- **No changes needed** - Commands work the same way
- **Behind the scenes** - Now using faster Haiku agents
- **Benefits** - Faster execution, lower costs
- **Deprecated** - `skills/` directory (moved to `skills.deprecated/`)