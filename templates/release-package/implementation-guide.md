# Release Package: Implementation Guide

Complete guide for using the release package generation system.

---

## Part 1: Custom Command Syntax

### How to Invoke Commands

**Format:**
```
@claude [command-name]
parameter1: [value]
parameter2: [value]
```

Or use slash commands:
```
/command-name parameter1:[value] parameter2:[value]
```

---

## Command Reference

### Command 1: Generate Release Package

**Syntax:**
```
/generate-release project:[name] version:[X.Y.Z] source_dir:[path] scope:[module|service|full-app]
```

**Example:**
```
/generate-release project:UserAuth version:2.1.0 source_dir:/home/dev/auth-service scope:microservice
```

**Claude will:**
1. Scan and map the directory structure
2. Extract version, dependencies, configs
3. Apply reverse thinking to identify gaps
4. Populate all templates with project-specific content
5. Generate complete package

**Output:**
```
Release_v2.1.0/
├── README.md
├── USECASE.md
├── DEPLOY.md
├── TROUBLESHOOTING.md
├── ARCHITECTURE.md
├── API_REFERENCE.md
├── CONFIGURATION.md
├── CHANGELOG.md
├── INDEX.md
└── _MANIFEST.json
```

---

### Command 2: Validate Release

**Syntax:**
```
/validate-release package_path:[path] severity:[critical|standard|extended]
```

**Example:**
```
/validate-release package_path:/releases/v2.1.0 severity:standard
```

**Claude will:**
1. Check all documents are present
2. Validate all internal links
3. Test all code examples for syntax
4. Run reverse checklist
5. Generate validation report

**Output:**
```json
{
  "status": "PASS|WARN|FAIL",
  "completeness": "95%",
  "issues": [
    {"doc": "DEPLOY.md", "line": 45, "severity": "critical", "issue": "..."}
  ],
  "reverse_findings": ["Gap 1", "Gap 2"],
  "recommendations": ["Fix 1", "Fix 2"]
}
```

---

### Command 3: Reverse-Check Analysis

**Syntax:**
```
/reverse-check focus:[deployment|security|scalability|all] package_path:[path]
```

**Example:**
```
/reverse-check focus:security package_path:/releases/v2.1.0
```

**Claude will:**
1. Ask "What could go wrong?" for each scenario
2. Identify undocumented assumptions
3. Find edge cases and failure modes
4. Generate risk matrix
5. Suggest preventive measures

**Output:**
```markdown
# REVERSE ANALYSIS REPORT

## Critical Risks (5 found)
- Risk 1: [Description] → Prevention: [Action]

## Undocumented Assumptions (3 found)
- Assumption 1: [What] → Valid?: [Yes/No]

## Edge Cases Not Covered (4 found)
- Edge Case 1: [Scenario] → Mitigation: [How]

## Failure Mode Analysis
| Scenario | Probability | Impact | Detection | Recovery |
```

---

### Command 4: Update Documentation

**Syntax:**
```
/update-docs package_path:[path] changes:[changelog|new_feature|breaking_change] docs_to_update:[README|DEPLOY|ALL]
```

**Example:**
```
/update-docs package_path:/releases/v2.1.0 changes:new_feature docs_to_update:ALL
```

**Claude will:**
1. Parse changelog
2. Identify affected documents
3. Update content consistently
4. Maintain formatting standards
5. Generate diff report

---

### Command 5: Create Template

**Syntax:**
```
/create-template type:[README|USECASE|DEPLOY|TROUBLESHOOTING|ARCHITECTURE] style:[minimal|standard|comprehensive] project_type:[web_app|library|microservice|cli_tool]
```

**Example:**
```
/create-template type:DEPLOY style:comprehensive project_type:microservice
```

**Claude will:**
1. Generate skeleton with all necessary sections
2. Include inline guidance
3. Provide examples and anti-examples
4. Store in SERENA memory for reuse

---

## Part 2: Execution Flowchart

```
┌─────────────────────────────────────────────┐
│  START: Release Package Generation          │
└─────────────────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────┐
│  [SERENA: Load Memory]                      │
│  • Previous templates                       │
│  • Past issues & solutions                  │
│  • Custom commands                          │
└─────────────────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────┐
│  PHASE 1: Analysis & Discovery              │
│  ✓ Map directory structure                  │
│  ✓ Identify stakeholders & needs            │
│  ✓ Gap analysis (what's missing?)           │
│  ✓ REVERSE THINKING: What could go wrong?   │
└─────────────────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────┐
│  PHASE 2: Generate Templates                │
│  ✓ Populate README from templates           │
│  ✓ Populate USECASE from templates          │
│  ✓ Populate DEPLOY from templates           │
│  ✓ Populate TROUBLESHOOTING from templates  │
│  ✓ Populate ARCHITECTURE from templates     │
└─────────────────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────┐
│  PHASE 3: Content Development               │
│  ✓ Write detailed sections                  │
│  ✓ Add code examples                        │
│  ✓ Include configuration guides             │
│  ✓ Document edge cases                      │
└─────────────────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────┐
│  PHASE 4: REVERSE VALIDATION                │
│  ✓ Apply reverse checklist                  │
│  ✓ Identify risks (what could break?)       │
│  ✓ Challenge assumptions                    │
│  ✓ Find failure modes                       │
│  ✓ Update docs with findings                │
└─────────────────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────┐
│  QUALITY ASSURANCE                          │
│  ✓ Cross-validate all documents             │
│  ✓ Test all examples                        │
│  ✓ Verify all links work                    │
│  ✓ Check formatting consistency             │
│  ✓ Score completeness: Goal = 95%+          │
└─────────────────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────┐
│  PACKAGE ASSEMBLY                           │
│  ✓ Organize all documents                   │
│  ✓ Create INDEX.md navigation               │
│  ✓ Generate _MANIFEST.json                  │
│  ✓ Create version checksums                 │
└─────────────────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────┐
│  DELIVERY & STORAGE                         │
│  ✓ Package ready for distribution           │
│  ✓ Store templates in SERENA                │
│  ✓ Save reverse findings to memory          │
│  ✓ Document decisions & rationale           │
│  ✓ Prepare for next release                 │
└─────────────────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────┐
│  SUCCESS ✓                                  │
│  Release Package Complete & Validated       │
└─────────────────────────────────────────────┘
```

---

## Part 3: Quick Reference

### When to Use Each Command

| Situation | Command | Time |
|-----------|---------|------|
| Starting new release | `/generate-release` | 30-60 min |
| Before shipping | `/validate-release` | 10-15 min |
| Identify risks | `/reverse-check` | 15-20 min |
| Incremental updates | `/update-docs` | 5-10 min |
| New project type | `/create-template` | 10-15 min |

### Severity Levels

| Level | When to Use | Checks |
|-------|-------------|--------|
| `critical` | Quick pre-release | Must-have only |
| `standard` | Normal release | Critical + recommended |
| `extended` | Major release | All checks |

### Project Types

| Type | Focus Areas |
|------|-------------|
| `web_app` | Frontend/backend, APIs, database |
| `library` | API docs, imports, compatibility |
| `microservice` | Containers, service discovery |
| `cli_tool` | Commands, flags, exit codes |

---

## Part 4: Best Practices

### Do's

- Run `/validate-release` before every release
- Use `/reverse-check` when adding new features
- Keep templates updated based on findings
- Store lessons learned in SERENA memory
- Test all code examples

### Don'ts

- Don't skip validation steps
- Don't ignore reverse thinking findings
- Don't commit .env files or secrets
- Don't release with broken links
- Don't assume - document everything

### Quality Checklist

Before any release:

- [ ] All documents generated
- [ ] Validation passes with no critical issues
- [ ] Reverse check completed
- [ ] All code examples tested
- [ ] All links verified
- [ ] Version numbers consistent
- [ ] Security items flagged with ⚠️

---

*End of Implementation Guide*
