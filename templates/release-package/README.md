# Release Package Template

Generate professional, comprehensive release documentation packages with reverse thinking methodology.

## Overview

This template provides a complete system for generating release documentation that:
- Identifies gaps and risks before they become problems
- Maintains consistency across all documentation
- Integrates with SERENA MCP for memory persistence
- Uses reverse thinking to challenge assumptions

## Quick Start

### 1. Generate a Release Package

```
/generate-release project:MyProject version:1.0.0 source_dir:/path/to/code scope:full-app
```

### 2. Validate Before Shipping

```
/validate-release package_path:/path/to/Release_v1.0.0 severity:critical
```

### 3. Check for Risks

```
/reverse-check focus:deployment package_path:/path/to/Release_v1.0.0
```

## Available Commands

| Command | Purpose | Time |
|---------|---------|------|
| `/generate-release` | Create full documentation package | 30-60 min |
| `/validate-release` | Quality assurance check | 10-15 min |
| `/reverse-check` | Risk and gap analysis | 15-20 min |
| `/update-docs` | Incremental updates | 5-10 min |
| `/create-template` | Generate new templates | 10-15 min |

## Workflow

```
START
  ↓
[Load SERENA Memory] → Previous templates, issues, patterns
  ↓
[Phase 1: Analysis] → Map structure, identify gaps, reverse thinking
  ↓
[Phase 2: Templates] → Populate from doc-templates/
  ↓
[Phase 3: Content] → Write sections, add examples
  ↓
[Phase 4: Validation] → Reverse checklist, risk matrix
  ↓
[Phase 5: Assembly] → Package, index, manifest
  ↓
SUCCESS
```

## Output Structure

```
Release_v1.0.0/
├── README.md           # Quick start and overview
├── USECASE.md          # Real-world scenarios
├── DEPLOY.md           # Production deployment
├── TROUBLESHOOTING.md  # Problems and solutions
├── ARCHITECTURE.md     # System internals
├── API_REFERENCE.md    # API documentation
├── CONFIGURATION.md    # All options
├── CHANGELOG.md        # Version history
├── INDEX.md            # Navigation
└── _MANIFEST.json      # Package metadata
```

## Key Features

### Reverse Thinking Methodology

Instead of "How does this work?", we ask:
- What could go wrong?
- What are we assuming?
- What happens when X fails?
- What questions haven't we answered?

### SERENA MCP Integration

- **Memory persistence** - Templates and findings saved across sessions
- **Pattern recognition** - Learn from previous releases
- **Continuous improvement** - Each release improves the next

### Visual Standards

Consistent indicators across all documents:
- ⚠️ Warnings and critical items
- ✅ Required steps / success
- ℹ️ Notes and context
- ❌ Common mistakes

## Template Library

Located in `doc-templates/`:

- **README.template.md** - Project overview and quick start
- **USECASE.template.md** - Scenarios and examples
- **DEPLOY.template.md** - Production deployment guide
- **TROUBLESHOOTING.template.md** - Error codes and solutions

## Success Criteria

A complete release package should achieve:
- All documents present
- All code examples syntactically correct
- All internal links working
- Completeness score >= 95%
- Zero critical gaps

## Documentation

- [Implementation Guide](./implementation-guide.md) - Detailed usage instructions
- [SERENA Memory Guide](./serena-memory-guide.md) - Memory management system

## Contributing

To improve this template:
1. Use `/reverse-check` to identify gaps
2. Update templates in `doc-templates/`
3. Test with `/validate-release`
4. Document changes in this README
