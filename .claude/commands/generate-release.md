# Generate Release Package

Generate a complete release documentation package for a project.

## Command Syntax

```
/generate-release project:[name] version:[X.Y.Z] source_dir:[path] scope:[module|service|full-app]
```

## Parameters

- **project** (required): Project name
- **version** (required): Semantic version (X.Y.Z)
- **source_dir** (required): Path to developer directory to analyze
- **scope** (required): `module`, `service`, or `full-app`

## Workflow

You are the **Release Package Architect**. Execute this 5-phase workflow:

### Phase 1: Analysis & Discovery
1. Scan and map the directory structure at `$ARGUMENTS.source_dir`
2. Extract version info, dependencies, and configurations
3. Identify all stakeholders (developers, ops, end-users)
4. Perform gap analysis - what documentation is missing?
5. Apply **REVERSE THINKING**: What could go wrong with this release?

### Phase 2: Template Generation
Generate these documents using templates from `templates/release-package/doc-templates/`:
- README.md
- USECASE.md
- DEPLOY.md
- TROUBLESHOOTING.md
- ARCHITECTURE.md
- API_REFERENCE.md
- CONFIGURATION.md
- CHANGELOG.md

### Phase 3: Content Development
1. Write detailed sections with project-specific content
2. Add working code examples (test them!)
3. Include configuration guides with all options
4. Document edge cases discovered in Phase 1

### Phase 4: Reverse Validation
Apply reverse thinking checklist:
- [ ] What assumptions are we making?
- [ ] What could break during deployment?
- [ ] What error scenarios aren't documented?
- [ ] What questions will users ask that we haven't answered?
- [ ] What dependencies could fail?

Update documents with findings.

### Phase 5: Package Assembly
1. Organize all documents in release folder
2. Create INDEX.md navigation
3. Generate _MANIFEST.json with metadata
4. Create version checksums

## Output Structure

```
Release_v{version}/
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

## Success Criteria

- All documents present and complete
- All code examples are syntactically correct
- All internal links work
- Completeness score >= 95%
- Zero critical gaps identified

## Visual Standards

Use these indicators consistently:
- ⚠️ Warnings and critical items
- ✅ Required steps / success
- ℹ️ Notes and context
- 🎯 Best practices
- ❌ Common mistakes / anti-patterns
