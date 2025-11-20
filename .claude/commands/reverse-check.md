# Reverse-Check Analysis

Perform reverse thinking analysis to identify risks, gaps, and failure modes.

## Command Syntax

```
/reverse-check focus:[deployment|security|scalability|all] package_path:[path]
```

## Parameters

- **focus** (required): Area to analyze
  - `deployment`: Installation and deployment risks
  - `security`: Security vulnerabilities and concerns
  - `scalability`: Performance and scaling issues
  - `all`: Comprehensive analysis
- **package_path** (required): Path to release package

## Reverse Thinking Methodology

Instead of asking "How does this work?", ask:
- "What could go wrong?"
- "What are we assuming?"
- "What happens when X fails?"
- "What question haven't we answered?"

## Workflow

### Step 1: Risk Identification
For each document, identify:
- What could break?
- What dependencies could fail?
- What user errors are likely?
- What environment differences could cause issues?

### Step 2: Assumption Challenge
List all implicit assumptions:
- System requirements
- User knowledge level
- Network conditions
- Available permissions
- External service availability

### Step 3: Edge Case Discovery
Identify undocumented scenarios:
- Concurrent operations
- Resource exhaustion
- Partial failures
- Recovery scenarios
- Upgrade paths

### Step 4: Failure Mode Analysis
For each critical operation:
- What triggers failure?
- How is it detected?
- What's the impact?
- How to recover?

### Step 5: Prevention Planning
Generate preventive measures:
- Pre-flight checks
- Monitoring alerts
- Automated validation
- Fallback procedures

## Output Format

```markdown
# REVERSE ANALYSIS REPORT

## Executive Summary
- Critical Risks Found: X
- Undocumented Assumptions: Y
- Edge Cases Not Covered: Z
- Overall Risk Level: LOW|MEDIUM|HIGH|CRITICAL

---

## Critical Risks

### Risk 1: [Title]
- **Description:** [What could go wrong]
- **Probability:** Low|Medium|High
- **Impact:** Low|Medium|High|Critical
- **Prevention:** [How to prevent]
- **Detection:** [How to detect if it happens]
- **Recovery:** [How to recover]

### Risk 2: [Title]
...

---

## Undocumented Assumptions

| # | Assumption | Valid? | Action Required |
|---|------------|--------|-----------------|
| 1 | User has Node.js 14+ | Yes | Document in README |
| 2 | Database allows remote connections | Maybe | Add verification step |
| 3 | SSL certificates are valid | Unknown | Add expiry check |

---

## Edge Cases Not Covered

### Edge Case 1: [Scenario]
- **Trigger:** [What causes this]
- **Current Behavior:** [What happens now]
- **Expected Behavior:** [What should happen]
- **Mitigation:** [How to handle]

---

## Failure Mode Analysis

| Operation | Failure Mode | Detection | Impact | Recovery Time |
|-----------|--------------|-----------|--------|---------------|
| Database connection | Timeout | Health check | High | 1-5 min |
| File upload | Disk full | Monitoring | Medium | 10 min |
| API call | Rate limited | Error logs | Low | Auto-retry |

---

## Prevention Recommendations

### Immediate Actions (Before Release)
1. [Action] - Priority: Critical
2. [Action] - Priority: High

### Short-term Actions (Within 1 Week)
1. [Action]
2. [Action]

### Long-term Improvements
1. [Action]
2. [Action]

---

## Updated Documentation Required

| Document | Section | Change Needed |
|----------|---------|---------------|
| DEPLOY.md | Pre-flight | Add disk space check |
| TROUBLESHOOTING.md | Errors | Add E003 SSL expiry |
| README.md | Requirements | Document Node version |
```

## Focus Area Details

### Deployment Focus
- Installation failures
- Configuration errors
- Permission issues
- Network problems
- Resource constraints

### Security Focus
- Authentication bypass
- Data exposure
- Injection vulnerabilities
- Insecure defaults
- Missing encryption

### Scalability Focus
- Performance bottlenecks
- Resource exhaustion
- Concurrency issues
- Database limitations
- Network saturation
