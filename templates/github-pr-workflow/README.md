# GitHub Copilot PR Workflow Templates

Complete automation system for GitHub pull request management using GitHub Copilot, custom agents, and intelligent code review tools.

## Overview

This template collection provides a fully integrated PR workflow solution that:
- **Automates code review** with GitHub Copilot agents and secondary reviewers
- **Resolves review comments** automatically using Claude Code slash commands
- **Tracks PR metrics** and team performance
- **Manages branches** with automatic cleanup
- **Enforces quality** through CI/CD and automated checks

## Quick Start

### 1. Initialize Your Repository

```bash
/setup-pr-workflow --code-reviewer coderabbit --branch-strategy gitflow
```

This command will:
- Create GitHub Actions workflows
- Generate custom Copilot agents
- Configure branch protection rules
- Set up code review tool integration

### 2. Create a Pull Request

```bash
git checkout -b feature/new-feature
# Make your changes
git commit -m "feat: add new feature"
git push origin feature/new-feature
gh pr create --fill
```

### 3. Automated Review Process

- GitHub Copilot agents automatically review your code
- CodeRabbit/Sourcery provides secondary analysis
- CI/CD runs tests and security scans
- Metrics are tracked automatically

### 4. Resolve Review Comments

```bash
# Single comment
/resolve-gh-review-comment https://github.com/org/repo/pull/123#discussion_r456789

# Multiple comments
/bulk-resolve-comments 123 --priority critical --auto-resolve
```

### 5. Analyze Performance

```bash
/analyze-pr-metrics --period 30d
```

## Available Templates

### GitHub Actions Workflows

Located in `workflow-templates/`:

#### 1. PR Automation (`pr-automation.yml`)
- **Purpose**: Comprehensive PR analysis, testing, and validation
- **Triggers**: PR opened, updated, review comments
- **Features**:
  - PR size analysis and warnings
  - Branch strategy detection
  - Commit message validation
  - Conflict detection
  - Multi-language test support (Node, Python, Rust, Go)
  - Security scanning

#### 2. Branch Cleanup (`branch-cleanup.yml`)
- **Purpose**: Automatic branch lifecycle management
- **Triggers**: PR merged, weekly schedule
- **Features**:
  - Auto-delete merged branches
  - Stale branch detection (30+ days)
  - Cleanup report generation
  - Protected branch preservation

### Custom Copilot Agents

Located in `agent-templates/`:

#### 1. Code Quality Reviewer (`code-quality-reviewer.agent.md`)
- **Focus**: Maintainability, best practices, code smells
- **Severity Levels**: Critical, High, Medium, Low
- **Languages**: JavaScript, Python, Java, Go, Rust
- **Features**:
  - SOLID principles enforcement
  - Anti-pattern detection
  - Performance analysis
  - Documentation review

#### 2. Security Auditor (`security-auditor.agent.md`)
- **Focus**: OWASP Top 10, vulnerabilities, compliance
- **Checks**: Injection flaws, auth issues, data protection
- **Standards**: GDPR, HIPAA, PCI DSS, SOC 2
- **Features**:
  - CWE/CVE tracking
  - Secret detection
  - Security header validation
  - Automated security scans

#### 3. Test Coverage Agent (`test-coverage-agent.agent.md`)
- **Focus**: Test completeness, quality, coverage gaps
- **Metrics**: Line coverage, branch coverage, edge cases
- **Features**:
  - Missing test detection
  - Test quality assessment
  - Test template generation
  - Coverage goal tracking (80% target)

### Configuration Templates

Located in `config-templates/`:

#### CodeRabbit Configuration (`.coderabbit.yaml`)
- Review profile settings (chill/assertive)
- Path-based review rules
- Auto-review configuration
- Chat settings

#### Sourcery Configuration (`.sourcery.yaml`)
- Refactoring rules
- Complexity limits
- Security checks
- GitHub integration settings

## Slash Commands

### Command Reference

| Command | Purpose | Time |
|---------|---------|------|
| `/resolve-gh-review-comment` | Fix single review comment | 5-10 min |
| `/bulk-resolve-comments` | Fix multiple comments | 15-30 min |
| `/setup-pr-workflow` | Initialize repository | 10-15 min |
| `/create-custom-agent` | Generate new agent | 5-10 min |
| `/analyze-pr-metrics` | Performance analytics | 5 min |

### Detailed Command Documentation

See `.claude/commands/` directory for full documentation of each command:
- `resolve-gh-review-comment.md` - Single comment resolution workflow
- `bulk-resolve-comments.md` - Batch comment processing
- `setup-pr-workflow.md` - Complete repository setup
- `create-custom-agent.md` - Custom agent generation
- `analyze-pr-metrics.md` - PR analytics and reporting

## Workflow Sequence

```
1. PR Created
   ↓
2. GitHub Actions: PR Analysis
   - Size check
   - Branch strategy detection
   - Commit validation
   ↓
3. Automated Review
   - Copilot agents review
   - CodeRabbit/Sourcery analysis
   - Security scanning
   ↓
4. CI/CD Validation
   - Tests run
   - Linters execute
   - Coverage calculated
   ↓
5. Comment Resolution
   - Developer or automation fixes issues
   - Replies posted with commit refs
   - Threads resolved
   ↓
6. Approval & Merge
   - Human approval required
   - Automatic merge strategy applied
   - Branch auto-deleted
   ↓
7. Metrics Update
   - Performance tracked
   - Reports generated
```

## Branch Naming Conventions

| Pattern | Type | Merge Strategy |
|---------|------|----------------|
| `feature/*` | New features | Squash merge |
| `bugfix/*` | Bug fixes | Squash merge |
| `hotfix/*` | Production fixes | Merge commit |
| `release/*` | Release branches | Merge commit |
| `chore/*` | Maintenance | Rebase merge |

## Integration Options

### Code Review Tools

**CodeRabbit** (Recommended)
- **Speed**: 5 seconds per review
- **Features**: AST analysis, agentic chat, learning
- **Privacy**: SOC2 Type II certified
- **Pricing**: $12-30/dev/month + free tier
- **Setup**: Install GitHub App, configure `.coderabbit.yaml`

**Sourcery AI** (Alternative)
- **Speed**: Real-time IDE feedback
- **Features**: Security scanning, IDE integration
- **Languages**: 30+ supported
- **Pricing**: Free + enterprise options
- **Setup**: Install GitHub App, configure `.sourcery.yaml`

### MCP Server Integration

**SERENA Memory Persistence**
- Store templates and patterns
- Track quality scores over time
- Learn from previous PRs
- Continuous improvement

## Success Metrics

### Target Performance Indicators

| Metric | Target | Industry Avg |
|--------|--------|--------------|
| Average Merge Time | < 3 days | 5-7 days |
| Time to First Review | < 24 hours | 2-3 days |
| Test Coverage | > 80% | 60-70% |
| Code Review Comments | 5-10 per PR | 15-20 |
| PR Size | < 300 lines | 400-500 lines |
| Stale PRs (>14 days) | < 5% | 20-30% |

### Tracked Metrics

The system automatically tracks:
- PR cycle time (creation to merge)
- Review efficiency (time to first review, rounds)
- Team throughput (PRs merged per day)
- Code churn (additions vs deletions)
- Automation adoption rate
- Defect escape rate

## Security & Compliance

### Security Features

- ✅ CodeQL security scanning
- ✅ Dependency vulnerability checking
- ✅ Secret detection (hardcoded credentials)
- ✅ OWASP Top 10 compliance checks
- ✅ Branch protection enforcement
- ✅ Required approvals before merge

### Governance

- **Copilot Restrictions**: Can only push to `copilot/*` branches
- **Approval Requirements**: Human review required for all PRs
- **Audit Trail**: All changes tracked in commits
- **Branch Protection**: Main/master branches protected
- **Permission Model**: Write access required for PR approval

## Customization

### Adjusting Agent Behavior

Edit agent files in `.github/agents/`:

```markdown
---
name: Your Custom Agent
description: Specialized review focus
model: claude-opus
tools:
  - file_access
  - github
---

# Custom review instructions here
```

### Modifying Workflows

Edit workflow files in `.github/workflows/`:

```yaml
# Adjust test commands
- name: Run Tests
  run: your-custom-test-command

# Add custom checks
- name: Custom Check
  run: your-custom-check
```

### Configuring Review Tools

**CodeRabbit** (`.coderabbit.yaml`):
```yaml
reviews:
  profile: assertive  # or chill

path_instructions:
  - path: "src/critical/**"
    instructions: "Extra scrutiny for critical code"
```

**Sourcery** (`.sourcery.yaml`):
```yaml
refactor:
  max_complexity: 10  # Adjust complexity threshold
```

## Troubleshooting

### Common Issues

| Issue | Solution |
|-------|----------|
| Workflows not running | Check Actions enabled in repo settings |
| Copilot not responding | Verify Copilot enabled for organization |
| Branch protection fails | Ensure admin access to repository |
| Tests failing | Review test command in workflow |
| Comments not resolving | Check GitHub token permissions |
| Metrics incomplete | Ensure workflows have run for 30+ days |

### Debug Commands

```bash
# Check workflow status
gh run list

# View workflow logs
gh run view <run-id> --log

# Check branch protection
gh api repos/{owner}/{repo}/branches/main/protection

# List Copilot agents
ls .github/agents/
```

## Best Practices

### For Teams

1. **Start Small**: Begin with one agent and expand
2. **Train Team**: Share documentation and examples
3. **Set Expectations**: Define review SLAs
4. **Monitor Metrics**: Track and improve over time
5. **Iterate**: Gather feedback and adjust

### For Developers

1. **Small PRs**: Keep changes focused (< 300 lines)
2. **Clear Descriptions**: Explain the "why" not just "what"
3. **Self-Review**: Review your own code first
4. **Address Feedback**: Respond to all comments
5. **Test Locally**: Ensure tests pass before pushing

### For Reviewers

1. **Timely Reviews**: Respond within 24 hours
2. **Constructive Feedback**: Explain reasoning
3. **Acknowledge Good Work**: Positive reinforcement
4. **Prioritize**: Focus on critical issues first
5. **Use Automation**: Let agents handle routine checks

## Examples

### Example 1: Feature Development

```bash
# Create feature branch
git checkout -b feature/user-authentication

# Make changes and commit
git commit -m "feat: add JWT authentication"

# Push and create PR
git push origin feature/user-authentication
gh pr create --fill

# Automated workflow runs:
# - PR analysis
# - Copilot review
# - CodeRabbit analysis
# - Tests

# Resolve any comments
/bulk-resolve-comments <pr-number> --priority all

# Merge after approval
# Branch automatically deleted
```

### Example 2: Hotfix

```bash
# Create hotfix branch
git checkout -b hotfix/security-patch

# Apply fix
git commit -m "fix: patch security vulnerability CVE-2024-1234"

# Push
git push origin hotfix/security-patch
gh pr create --title "Security Hotfix" --body "Critical security patch"

# Expedited review
# Merge commit strategy applied
# Branch preserved for audit trail
```

## Contributing

### Adding New Agents

```bash
/create-custom-agent my-agent --role custom
```

Edit the generated file in `.github/agents/my-agent.agent.md`

### Improving Workflows

1. Fork and modify workflow templates
2. Test in a separate repository
3. Submit PR with improvements
4. Document changes in commit message

## Resources

- [GitHub Copilot Documentation](https://docs.github.com/copilot)
- [GitHub Actions Documentation](https://docs.github.com/actions)
- [CodeRabbit Documentation](https://docs.coderabbit.ai)
- [Sourcery Documentation](https://docs.sourcery.ai)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [Conventional Commits](https://www.conventionalcommits.org/)

## License

MIT License - See repository LICENSE file

---

## Getting Help

- Check command documentation in `.claude/commands/`
- Review workflow logs in GitHub Actions
- Consult agent configurations in `.github/agents/`
- Use `/analyze-pr-metrics` to identify bottlenecks

---

*Built with context engineering principles for AI-assisted development*
