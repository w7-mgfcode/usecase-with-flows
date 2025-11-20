# Update Documentation

Incrementally update release documentation based on changes.

## Command Syntax

```
/update-docs package_path:[path] changes:[changelog|new_feature|breaking_change] docs_to_update:[README|DEPLOY|ALL]
```

## Parameters

- **package_path** (required): Path to release package
- **changes** (required): Type of change
  - `changelog`: Version update with changelog entry
  - `new_feature`: New functionality added
  - `breaking_change`: Breaking change requiring migration
- **docs_to_update** (required): Which documents to update
  - Specific doc name (e.g., `README`, `DEPLOY`)
  - `ALL`: Update all affected documents

## Workflow

### Step 1: Parse Changes
1. Read changelog or change description
2. Identify affected components
3. Determine documentation impact
4. List sections requiring updates

### Step 2: Impact Analysis
For each change, determine:
- Which documents are affected?
- What sections need updating?
- Are new sections needed?
- Do examples need updating?

### Step 3: Content Update
1. Update affected sections
2. Maintain formatting standards
3. Update version numbers
4. Update "last modified" dates
5. Add migration guides for breaking changes

### Step 4: Cross-Reference Check
1. Ensure consistency across documents
2. Update all internal references
3. Verify external links still valid
4. Update INDEX.md if needed

### Step 5: Generate Diff Report

## Output Format

```markdown
# Documentation Update Report

## Changes Processed
- Type: [changelog|new_feature|breaking_change]
- Version: X.Y.Z → X.Y.Z+1
- Date: YYYY-MM-DD

## Documents Updated

### README.md
**Sections Modified:**
- What's New in vX.Y.Z (added)
- System Requirements (updated)

**Diff:**
\`\`\`diff
- Node.js ≥ 14.0
+ Node.js ≥ 16.0

+ ### What's New in v1.2.0
+ - Feature A: [Description]
+ - Improved: Performance optimization
\`\`\`

### DEPLOY.md
**Sections Modified:**
- Step 2: Configure Environment (updated)

**Diff:**
\`\`\`diff
+ # New in v1.2.0
+ FEATURE_A_ENABLED=true
\`\`\`

### CHANGELOG.md
**Sections Modified:**
- Added v1.2.0 entry

## Migration Guide (if breaking change)

### Breaking Change: [Description]

**What Changed:**
[Old behavior] → [New behavior]

**Migration Steps:**
1. [Step 1]
2. [Step 2]
3. [Step 3]

**Code Changes Required:**
\`\`\`diff
- oldFunction()
+ newFunction(options)
\`\`\`

## Verification Checklist

- [ ] All version numbers updated consistently
- [ ] All affected docs modified
- [ ] Examples updated and tested
- [ ] Migration guide complete (if applicable)
- [ ] Internal links verified
- [ ] INDEX.md updated
```

## Change Type Details

### Changelog Update
- Update CHANGELOG.md
- Update version in README.md
- Update "What's New" section
- Verify no breaking changes missed

### New Feature
- Add feature to README.md features list
- Create use case in USECASE.md
- Add configuration in CONFIGURATION.md
- Document in API_REFERENCE.md if applicable
- Add troubleshooting entries

### Breaking Change
- Add migration guide
- Update all affected code examples
- Add deprecation warnings
- Update system requirements if changed
- Highlight in CHANGELOG.md with ⚠️
