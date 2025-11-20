# Validate Release Package

Validate a release package for completeness, correctness, and quality.

## Command Syntax

```
/validate-release package_path:[path] severity:[critical|standard|extended]
```

## Parameters

- **package_path** (required): Path to the release package directory
- **severity** (required): Validation level
  - `critical`: Must-have checks only
  - `standard`: Critical + recommended checks
  - `extended`: All checks including nice-to-haves

## Workflow

### Step 1: Document Presence Check
Verify all required documents exist:
- [ ] README.md
- [ ] USECASE.md
- [ ] DEPLOY.md
- [ ] TROUBLESHOOTING.md
- [ ] ARCHITECTURE.md
- [ ] API_REFERENCE.md
- [ ] CONFIGURATION.md
- [ ] CHANGELOG.md
- [ ] INDEX.md
- [ ] _MANIFEST.json

### Step 2: Link Validation
- Check all internal links resolve
- Verify external links are accessible
- Validate anchor links

### Step 3: Code Example Testing
- Extract all code blocks
- Check syntax correctness
- Verify imports/dependencies are documented
- Test runnable examples if possible

### Step 4: Reverse Checklist
Apply reverse thinking validation:
- Are all assumptions documented?
- Are failure scenarios covered?
- Are edge cases addressed?
- Are rollback procedures clear?

### Step 5: Consistency Check
- Formatting follows standards
- Visual indicators used correctly
- Version numbers consistent
- Terminology consistent across docs

## Output Format

```json
{
  "status": "PASS|WARN|FAIL",
  "completeness": "95%",
  "documents": {
    "present": 10,
    "missing": 0,
    "incomplete": 1
  },
  "issues": [
    {
      "doc": "DEPLOY.md",
      "line": 45,
      "severity": "critical",
      "issue": "Missing rollback procedure for database migration",
      "suggestion": "Add rollback steps after Step 3"
    }
  ],
  "reverse_findings": [
    "Gap: No documentation for handling SSL certificate renewal",
    "Assumption: User has sudo access - not documented"
  ],
  "code_examples": {
    "total": 25,
    "valid": 24,
    "errors": 1
  },
  "links": {
    "internal": {"total": 50, "valid": 50},
    "external": {"total": 10, "valid": 9, "broken": 1}
  },
  "recommendations": [
    "Add SSL renewal procedure to DEPLOY.md",
    "Fix broken link to external API docs",
    "Document sudo requirement in README.md"
  ]
}
```

## Severity Levels

### Critical (must fix before release)
- Missing required documents
- Broken internal links
- Syntax errors in code examples
- Missing security warnings
- No rollback procedure

### Standard (should fix)
- Incomplete sections
- Broken external links
- Inconsistent formatting
- Missing edge cases

### Extended (nice to have)
- Style improvements
- Additional examples
- Performance tips
- Advanced use cases
