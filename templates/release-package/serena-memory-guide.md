# SERENA MCP Memory Management Guide

Integrate SERENA memory system for persistent context across sessions.

---

## Overview

SERENA MCP provides:
- **Memory persistence** - Save templates and findings
- **Pattern recognition** - Learn from previous releases
- **Context retrieval** - Access historical data
- **Continuous improvement** - Each release improves the next

---

## Part 1: Memory Initialization

### Initialize Project Memory

```yaml
SERENA_CONTEXT:
  project: "[Project Name]"
  version: "[X.Y.Z]"

  TEMPLATES_LIBRARY:
    - README.md (stored)
    - USECASE.md (stored)
    - DEPLOY.md (stored)
    - TROUBLESHOOTING.md (stored)
    - ARCHITECTURE.md (stored)

  REVERSE_FINDINGS:
    - Critical Gaps: []
    - Assumptions Validated: []
    - Edge Cases Identified: []
    - Recommendations: []

  PREVIOUS_ISSUES:
    - Issue 1: [What happened] → [Root cause] → [Fixed by]

  CUSTOM_COMMANDS:
    - generate-release: [Last used date]
    - validate-release: [Last used date]
    - reverse-check: [Last used date]
    - update-docs: [Last used date]
    - create-template: [Last used date]

  QUALITY_SCORES:
    - Last validation: [Date]
    - Completeness: [%]
    - Issues found: [#]
    - Status: [PASS/WARN/FAIL]
```

---

## Part 2: Memory Operations

### Store New Finding

```
[SERENA: Store Finding]
@claude "Save this reverse-analysis finding: SSL certificates expire without warning"
→ Stores in REVERSE_FINDINGS for future reference
```

### Retrieve Previous Issues

```
[SERENA: Load Memory]
@claude "Retrieve all previous issues for this project"
→ Returns list of past problems and solutions
```

### Update Command History

```
[SERENA: Update Command]
@claude "Log that 'generate-release' was used with version 2.1.0"
→ Updates CUSTOM_COMMANDS with timestamp
```

### Get Improvement Suggestions

```
[SERENA: Suggest Improvements]
@claude "Based on previous releases, what improvements should we make?"
→ Analyzes patterns and recommends optimizations
```

### Store Quality Score

```
[SERENA: Store Score]
@claude "Record validation score: 95% completeness, 2 issues, status PASS"
→ Updates QUALITY_SCORES for trending
```

---

## Part 3: Query Examples

### Find Related Issues

```
@claude "What issues have we had with database deployments?"
→ Searches PREVIOUS_ISSUES for database-related entries
```

### Get Template History

```
@claude "Show me how our README template has evolved"
→ Returns version history of README.template.md
```

### Analyze Patterns

```
@claude "What are the most common reverse-check findings?"
→ Aggregates REVERSE_FINDINGS and ranks by frequency
```

### Cross-Project Learning

```
@claude "What deployment issues did Project A have that we should avoid?"
→ Retrieves lessons learned from other projects
```

---

## Part 4: Quick Start

### 5-Minute Setup

1. **Initialize memory for project**
   ```
   @claude "Initialize SERENA memory for project MyApp version 1.0.0"
   ```

2. **Store existing templates**
   ```
   @claude "Store current templates in SERENA memory"
   ```

3. **Record any known issues**
   ```
   @claude "Record known issue: Database migrations can timeout on large datasets"
   ```

### 10-Minute Validation

1. **Load context**
   ```
   @claude "Load all SERENA context for MyApp"
   ```

2. **Run validation with history**
   ```
   /validate-release package_path:/releases/v1.0.0 severity:extended
   ```

3. **Store new findings**
   ```
   @claude "Store these validation findings in SERENA memory"
   ```

4. **Update quality score**
   ```
   @claude "Update quality score to 97% completeness, status PASS"
   ```

---

## Part 5: Reverse Thinking Checklist

Before each release, verify:

### Documentation Completeness
- [ ] All required documents exist
- [ ] Each document has all sections
- [ ] Code examples are tested
- [ ] Links are validated

### Assumption Validation
- [ ] System requirements documented
- [ ] Dependencies listed with versions
- [ ] User permissions specified
- [ ] Network requirements stated

### Risk Identification
- [ ] Failure modes documented
- [ ] Recovery procedures included
- [ ] Rollback steps verified
- [ ] Monitoring configured

### Edge Case Coverage
- [ ] Concurrent operations handled
- [ ] Resource limits documented
- [ ] Error scenarios covered
- [ ] Timeout behaviors specified

---

## Part 6: Team Workflow

### Release Day Timeline

**T-24 hours: Pre-Release**
```
/reverse-check focus:all package_path:/releases/vX.Y.Z
@claude "Store reverse-check findings in SERENA"
```

**T-4 hours: Final Validation**
```
/validate-release package_path:/releases/vX.Y.Z severity:extended
@claude "Update quality scores in SERENA"
```

**T-0: Release**
```
@claude "Mark release vX.Y.Z as shipped in SERENA"
```

**T+1 day: Post-Release**
```
@claude "Record any post-release issues in SERENA"
@claude "What lessons should we capture from this release?"
```

---

## Part 7: Success Metrics

### Track These Metrics

| Metric | Target | How to Measure |
|--------|--------|----------------|
| Completeness | >= 95% | `/validate-release` score |
| Critical Issues | 0 | Validation report |
| Assumption Gaps | < 3 | `/reverse-check` findings |
| Edge Cases Missed | < 5 | Post-release issues |

### Improvement Tracking

```
@claude "Show quality score trend for last 5 releases"
→ Returns:
  v1.0.0: 88% (3 critical)
  v1.1.0: 92% (1 critical)
  v1.2.0: 95% (0 critical)
  v1.3.0: 97% (0 critical)
  v2.0.0: 98% (0 critical)
```

### Continuous Improvement

After each release:
1. Store all findings in SERENA
2. Update templates based on gaps found
3. Add new edge cases to checklists
4. Improve reverse-check focus areas

---

## Part 8: Advanced Usage

### Custom Memory Schemas

```yaml
# Extended schema for enterprise use
SERENA_CONTEXT_EXTENDED:
  compliance:
    - SOC2 requirements met
    - GDPR data handling documented
    - Security review completed

  stakeholders:
    - Development: [Names]
    - Operations: [Names]
    - Security: [Names]

  approvals:
    - Technical review: [Date, Approver]
    - Security review: [Date, Approver]
    - Release approval: [Date, Approver]
```

### Cross-Project Patterns

```
@claude "What patterns work well across all our microservice projects?"
→ Aggregates successful patterns from multiple projects
```

### Automated Triggers

```
# Set up SERENA to remind about common issues
@claude "Remind me about SSL certificate renewal when working on deployment docs"
```

---

*End of SERENA Memory Guide*
*Ready to use with Claude Code + SERENA MCP*
