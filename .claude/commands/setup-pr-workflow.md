# Setup GitHub PR Workflow

Initialize a repository with complete GitHub Copilot PR workflow automation, including custom agents, GitHub Actions workflows, branch protection rules, and code review tool integration.

## Command Syntax

```
/setup-pr-workflow [--code-reviewer coderabbit|sourcery|none] [--branch-strategy gitflow|trunk]
```

## Parameters

- **--code-reviewer** (optional): Choose secondary code review tool
  - `coderabbit`: Install CodeRabbit integration (default)
  - `sourcery`: Install Sourcery AI integration
  - `none`: GitHub Copilot only, no secondary reviewer
- **--branch-strategy** (optional): Choose branching model
  - `gitflow`: Feature branches, release branches (default)
  - `trunk`: Trunk-based development with short-lived branches

## Workflow

You are a **GitHub Workflow Setup Specialist**. Execute this comprehensive setup:

### Phase 1: Repository Analysis

#### Step 1: Verify Repository Context

Check current repository state:

```bash
# Get repository information
REPO_INFO=$(gh repo view --json nameWithOwner,defaultBranchRef,owner)
REPO_NAME=$(echo $REPO_INFO | jq -r '.nameWithOwner')
DEFAULT_BRANCH=$(echo $REPO_INFO | jq -r '.defaultBranchRef.name')
OWNER=$(echo $REPO_INFO | jq -r '.owner.login')

echo "Setting up PR workflow for: $REPO_NAME"
echo "Default branch: $DEFAULT_BRANCH"
```

#### Step 2: Detect Project Type

Analyze project structure to customize setup:

```bash
# Detect language/framework
if [ -f "package.json" ]; then
  PROJECT_TYPE="node"
  TEST_COMMAND="npm test"
  LINT_COMMAND="npm run lint"
elif [ -f "requirements.txt" ] || [ -f "pyproject.toml" ]; then
  PROJECT_TYPE="python"
  TEST_COMMAND="pytest"
  LINT_COMMAND="pylint ."
elif [ -f "Cargo.toml" ]; then
  PROJECT_TYPE="rust"
  TEST_COMMAND="cargo test"
  LINT_COMMAND="cargo clippy"
elif [ -f "go.mod" ]; then
  PROJECT_TYPE="go"
  TEST_COMMAND="go test ./..."
  LINT_COMMAND="golint ./..."
else
  PROJECT_TYPE="generic"
  TEST_COMMAND="echo 'No tests configured'"
  LINT_COMMAND="echo 'No linter configured'"
fi

echo "Detected project type: $PROJECT_TYPE"
```

#### Step 3: Check Existing Configuration

Identify what's already configured:

```bash
# Check for existing workflows
EXISTING_WORKFLOWS=$(find .github/workflows -name "*.yml" 2>/dev/null || echo "")

# Check for existing agents
EXISTING_AGENTS=$(find .github/agents -name "*.agent.md" 2>/dev/null || echo "")

# Check branch protection
PROTECTED=$(gh api repos/$REPO_NAME/branches/$DEFAULT_BRANCH/protection 2>/dev/null || echo "not protected")
```

### Phase 2: Directory Structure Creation

#### Step 4: Create Directory Structure

```bash
# Create necessary directories
mkdir -p .github/workflows
mkdir -p .github/agents
mkdir -p .github/ISSUE_TEMPLATE
mkdir -p .github/PULL_REQUEST_TEMPLATE
mkdir -p docs/workflows

echo "✅ Directory structure created"
```

### Phase 3: Custom Agents Setup

#### Step 5: Create Custom Copilot Agents

Create `.github/agents/code-quality-reviewer.agent.md`:

```markdown
---
name: Code Quality Reviewer
description: Reviews PR for code quality, maintainability, and best practices
tools:
  - file_access
  - terminal
  - github
---

You are an expert code quality reviewer for ${PROJECT_TYPE} projects.

## Review Focus Areas

1. **Code Maintainability**
   - Clear variable and function names
   - Appropriate abstraction levels
   - DRY principle adherence
   - Single Responsibility Principle

2. **Best Practices**
   - ${PROJECT_TYPE}-specific idioms
   - Error handling patterns
   - Resource management
   - Security considerations

3. **Performance**
   - Algorithm efficiency
   - Memory usage
   - Database query optimization
   - Caching opportunities

4. **Testing**
   - Test coverage for new code
   - Edge case handling
   - Integration test needs
   - Mock usage appropriateness

## Review Process

For each PR:
1. Analyze the diff comprehensively
2. Identify issues by severity (critical/high/medium/low)
3. Suggest specific, actionable improvements
4. Provide code examples where helpful
5. Explain the reasoning behind each suggestion

Be constructive and educational. Help developers improve their skills.
```

Create `.github/agents/test-coverage-agent.agent.md`:

```markdown
---
name: Test Coverage Agent
description: Ensures adequate test coverage and suggests test improvements
tools:
  - file_access
  - terminal
  - github
---

You are a testing specialist for ${PROJECT_TYPE} projects.

## Responsibilities

1. **Coverage Analysis**
   - Identify untested code paths
   - Calculate coverage gaps
   - Suggest test priorities

2. **Test Quality**
   - Review test assertions
   - Check edge case coverage
   - Validate test isolation
   - Ensure proper setup/teardown

3. **Test Suggestions**
   - Provide test templates
   - Suggest test scenarios
   - Recommend testing strategies
   - Identify integration test needs

## Test Commands

- Unit tests: ${TEST_COMMAND}
- Coverage: ${TEST_COMMAND} --coverage
- Watch mode: ${TEST_COMMAND} --watch

For each code change, suggest specific tests that should be added.
```

Create `.github/agents/security-reviewer.agent.md`:

```markdown
---
name: Security Reviewer
description: Identifies security vulnerabilities and compliance issues
tools:
  - file_access
  - terminal
  - github
---

You are a security specialist reviewing ${PROJECT_TYPE} code.

## Security Checklist

1. **Input Validation**
   - User input sanitization
   - Type checking
   - Range validation
   - Injection prevention

2. **Authentication & Authorization**
   - Proper authentication checks
   - Authorization enforcement
   - Session management
   - Token handling

3. **Data Protection**
   - Sensitive data encryption
   - Secure storage
   - PII handling
   - Secrets management

4. **Common Vulnerabilities**
   - SQL injection
   - XSS prevention
   - CSRF protection
   - Path traversal
   - Dependency vulnerabilities

Flag any security concerns immediately with CRITICAL severity.
```

### Phase 4: GitHub Actions Workflows

#### Step 6: Create PR Automation Workflow

Create `.github/workflows/pr-automation.yml`:

```yaml
name: PR Automation

on:
  pull_request:
    types: [opened, synchronize, reopened]
  pull_request_review_comment:
    types: [created]

permissions:
  contents: write
  pull-requests: write
  checks: write

jobs:
  pr-analysis:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: PR Size Analysis
        run: |
          FILES_CHANGED=$(git diff --name-only origin/${{ github.base_ref }}...HEAD | wc -l)
          ADDITIONS=$(git diff --shortstat origin/${{ github.base_ref }}...HEAD | grep -oP '\d+(?= insertion)' || echo 0)
          DELETIONS=$(git diff --shortstat origin/${{ github.base_ref }}...HEAD | grep -oP '\d+(?= deletion)' || echo 0)

          echo "## PR Analytics" >> $GITHUB_STEP_SUMMARY
          echo "- Files Changed: $FILES_CHANGED" >> $GITHUB_STEP_SUMMARY
          echo "- Additions: $ADDITIONS" >> $GITHUB_STEP_SUMMARY
          echo "- Deletions: $DELETIONS" >> $GITHUB_STEP_SUMMARY

          if [ "$FILES_CHANGED" -gt 20 ]; then
            echo "⚠️ Large PR detected. Consider splitting into smaller PRs." >> $GITHUB_STEP_SUMMARY
          fi

      - name: Detect Branch Strategy
        run: |
          BRANCH_NAME="${{ github.head_ref }}"
          if [[ $BRANCH_NAME == feature/* ]]; then
            echo "MERGE_STRATEGY=squash" >> $GITHUB_ENV
          elif [[ $BRANCH_NAME == bugfix/* ]]; then
            echo "MERGE_STRATEGY=squash" >> $GITHUB_ENV
          elif [[ $BRANCH_NAME == hotfix/* ]]; then
            echo "MERGE_STRATEGY=merge" >> $GITHUB_ENV
          else
            echo "MERGE_STRATEGY=rebase" >> $GITHUB_ENV
          fi
          echo "Recommended merge strategy: $MERGE_STRATEGY" >> $GITHUB_STEP_SUMMARY

      - name: Validate Commit Messages
        run: |
          COMMITS=$(git log --oneline origin/${{ github.base_ref }}...HEAD)
          echo "## Recent Commits" >> $GITHUB_STEP_SUMMARY
          echo '```' >> $GITHUB_STEP_SUMMARY
          echo "$COMMITS" >> $GITHUB_STEP_SUMMARY
          echo '```' >> $GITHUB_STEP_SUMMARY

          # Check for conventional commits
          if ! echo "$COMMITS" | grep -qE '^[a-f0-9]+ (feat|fix|docs|style|refactor|test|chore)(\(.+\))?:'; then
            echo "⚠️ Some commits don't follow conventional commit format" >> $GITHUB_STEP_SUMMARY
          fi

  run-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Environment
        run: |
          # Project-specific setup will be added here
          echo "Setting up ${PROJECT_TYPE} environment"

      - name: Run Tests
        run: ${TEST_COMMAND}

      - name: Run Linter
        run: ${LINT_COMMAND}

  security-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run Security Scan
        run: |
          # Project-specific security scans
          echo "Running security analysis for ${PROJECT_TYPE}"
```

#### Step 7: Create Branch Cleanup Workflow

Create `.github/workflows/branch-cleanup.yml`:

```yaml
name: Branch Cleanup

on:
  pull_request:
    types: [closed]
  schedule:
    - cron: '0 2 * * 0'  # Weekly Sunday 2 AM

permissions:
  contents: write

jobs:
  cleanup:
    runs-on: ubuntu-latest
    steps:
      - name: Delete Merged Branch
        if: github.event.pull_request.merged == true
        env:
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          BRANCH="${{ github.head_ref }}"
          if [[ ! "$BRANCH" =~ ^(main|master|develop|release/.*)$ ]]; then
            gh api --method DELETE repos/${{ github.repository }}/git/refs/heads/$BRANCH || true
            echo "Deleted merged branch: $BRANCH"
          fi

      - name: Cleanup Stale Branches
        if: github.event_name == 'schedule'
        env:
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          # List branches older than 30 days with no activity
          git fetch --all
          for branch in $(git branch -r --format='%(refname:short)' | grep -v 'HEAD\|main\|master\|develop'); do
            LAST_COMMIT_DATE=$(git log -1 --format=%at $branch)
            CURRENT_DATE=$(date +%s)
            DAYS_OLD=$(( ($CURRENT_DATE - $LAST_COMMIT_DATE) / 86400 ))

            if [ $DAYS_OLD -gt 30 ]; then
              echo "Branch $branch is $DAYS_OLD days old - considering for deletion"
              # Add deletion logic with approval
            fi
          done
```

### Phase 5: Code Review Tool Integration

#### Step 8: Setup CodeRabbit (if selected)

If `--code-reviewer coderabbit`:

Create `.coderabbit.yaml`:

```yaml
# CodeRabbit Configuration

# Review behavior
reviews:
  profile: chill  # Options: chill, assertive
  request_changes_workflow: false
  high_level_summary: true
  poem: false
  review_status: true
  collapse_walkthrough: false

# Auto-review settings
auto_review:
  enabled: true
  drafts: false

# Language settings
language: en-US

# Chat settings
chat:
  auto_reply: true

# Path-based rules
path_instructions:
  - path: "src/**/*.js"
    instructions: "Focus on ES6+ best practices and async/await patterns"
  - path: "test/**/*"
    instructions: "Ensure comprehensive test coverage and clear assertions"
  - path: "docs/**/*"
    instructions: "Check for clarity, accuracy, and completeness"

# Custom prompts
early_access: false
```

Output instructions:

```
✅ CodeRabbit configuration created

Next steps:
1. Install CodeRabbit GitHub App: https://github.com/apps/coderabbit-ai
2. Grant repository access
3. CodeRabbit will automatically review new PRs
```

#### Step 9: Setup Sourcery (if selected)

If `--code-reviewer sourcery`:

Create `.sourcery.yaml`:

```yaml
# Sourcery Configuration

python_version: "3.10"

refactor:
  skip_default_patterns: false
  max_complexity: 10

rules:
  - id: no-long-functions
    description: Functions should be less than 50 lines
    pattern: |
      def $FUNC(...):
        $BODY
    condition: len($BODY) > 50

  - id: require-docstrings
    description: Public functions must have docstrings
    pattern: |
      def $FUNC(...):
        $BODY
    condition: not has_docstring($FUNC)

github:
  request_review: author
  sourcery_branch: sourcery/improvements
```

Output instructions:

```
✅ Sourcery configuration created

Next steps:
1. Install Sourcery GitHub App: https://github.com/apps/sourcery-ai
2. Grant repository access
3. Sourcery will provide real-time suggestions
```

### Phase 6: Branch Protection Rules

#### Step 10: Configure Branch Protection

```bash
# Apply branch protection to main/master
gh api -X PUT repos/$REPO_NAME/branches/$DEFAULT_BRANCH/protection \
  -f required_status_checks='{"strict":true,"contexts":["pr-analysis","run-tests","security-scan"]}' \
  -f enforce_admins=false \
  -f required_pull_request_reviews='{"required_approving_review_count":1,"dismiss_stale_reviews":true}' \
  -f restrictions=null \
  -f required_linear_history=true \
  -f allow_force_pushes=false \
  -f allow_deletions=false

echo "✅ Branch protection configured for $DEFAULT_BRANCH"
```

### Phase 7: Documentation

#### Step 11: Create Workflow Documentation

Create `docs/workflows/PR_WORKFLOW.md`:

```markdown
# Pull Request Workflow Guide

## Overview

This repository uses automated PR workflows powered by GitHub Copilot and custom agents.

## Workflow Steps

1. **Create Feature Branch**
   \`\`\`bash
   git checkout -b feature/your-feature-name
   \`\`\`

2. **Make Changes and Commit**
   Follow conventional commits:
   \`\`\`bash
   git commit -m "feat: add new feature"
   git commit -m "fix: resolve bug in authentication"
   \`\`\`

3. **Push and Create PR**
   \`\`\`bash
   git push origin feature/your-feature-name
   gh pr create --fill
   \`\`\`

4. **Automated Review**
   - GitHub Copilot agents review your code
   - ${CODE_REVIEWER} provides secondary analysis
   - CI/CD runs tests and security scans

5. **Address Review Comments**
   Use Claude Code slash commands:
   \`\`\`bash
   /resolve-gh-review-comment <comment_url>
   /bulk-resolve-comments <pr_number> --priority all
   \`\`\`

6. **Merge**
   After approval and passing checks, merge is automated based on branch type

## Available Commands

- \`/resolve-gh-review-comment\` - Fix single review comment
- \`/bulk-resolve-comments\` - Fix multiple comments
- \`/analyze-pr-metrics\` - View PR statistics

## Branch Naming

- \`feature/*\` - New features (squash merge)
- \`bugfix/*\` - Bug fixes (squash merge)
- \`hotfix/*\` - Production fixes (merge commit)
- \`chore/*\` - Maintenance (rebase merge)
```

### Phase 8: Validation

#### Step 12: Verify Setup

```bash
echo "## Setup Validation" > setup-report.md

# Check workflows
if [ -d ".github/workflows" ]; then
  WORKFLOW_COUNT=$(find .github/workflows -name "*.yml" | wc -l)
  echo "✅ Workflows: $WORKFLOW_COUNT files" >> setup-report.md
else
  echo "❌ Workflows directory missing" >> setup-report.md
fi

# Check agents
if [ -d ".github/agents" ]; then
  AGENT_COUNT=$(find .github/agents -name "*.agent.md" | wc -l)
  echo "✅ Custom Agents: $AGENT_COUNT configured" >> setup-report.md
else
  echo "❌ Agents directory missing" >> setup-report.md
fi

# Check branch protection
PROTECTED=$(gh api repos/$REPO_NAME/branches/$DEFAULT_BRANCH/protection 2>/dev/null && echo "enabled" || echo "disabled")
echo "✅ Branch Protection: $PROTECTED" >> setup-report.md

cat setup-report.md
```

### Phase 9: Commit Setup

#### Step 13: Commit All Configuration

```bash
git add .github/
git add docs/
git add .coderabbit.yaml .sourcery.yaml 2>/dev/null || true

git commit -m "chore: setup GitHub Copilot PR workflow automation

- Add custom Copilot agents for code quality, testing, and security
- Configure GitHub Actions for PR automation
- Setup branch protection rules
- Integrate ${CODE_REVIEWER} code reviewer
- Add workflow documentation

Automated setup completed for ${PROJECT_TYPE} project."

git push origin HEAD
```

## Output Summary

After completion, display:

```
## GitHub PR Workflow Setup Complete! 🎉

### What Was Configured

✅ **Custom Copilot Agents** (3)
   - Code Quality Reviewer
   - Test Coverage Agent
   - Security Reviewer

✅ **GitHub Actions Workflows** (2)
   - PR Automation (analysis, tests, security)
   - Branch Cleanup (auto-delete merged branches)

✅ **Code Review Integration**
   - ${CODE_REVIEWER} configured and ready

✅ **Branch Protection**
   - Main branch protected
   - Required: 1 approval, passing checks
   - Auto-merge enabled

✅ **Documentation**
   - Workflow guide created in docs/

### Next Steps

1. **Test the Workflow**
   \`\`\`bash
   git checkout -b feature/test-pr-workflow
   echo "test" >> README.md
   git commit -am "test: verify PR workflow"
   git push origin feature/test-pr-workflow
   gh pr create --fill
   \`\`\`

2. **Install Code Review Tool**
   - CodeRabbit: https://github.com/apps/coderabbit-ai
   - Sourcery: https://github.com/apps/sourcery-ai

3. **Customize Agents**
   Edit files in \`.github/agents/\` to adjust review focus

4. **Train Team**
   Share \`docs/workflows/PR_WORKFLOW.md\` with team

### Available Commands

- \`/resolve-gh-review-comment\` - Resolve single comment
- \`/bulk-resolve-comments\` - Resolve multiple comments
- \`/analyze-pr-metrics\` - View PR statistics
- \`/create-custom-agent\` - Add new agent

Happy reviewing! 🚀
```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Workflows not running | Check Actions are enabled in repo settings |
| Branch protection fails | Ensure admin access to repository |
| Agent not responding | Verify Copilot is enabled for organization |
| Tests failing | Review test command in workflow config |

## Customization

After setup, you can customize:
- `.github/agents/*.agent.md` - Adjust agent prompts
- `.github/workflows/*.yml` - Modify workflow steps
- `.coderabbit.yaml` - Configure CodeRabbit behavior
- `docs/workflows/PR_WORKFLOW.md` - Update team documentation
