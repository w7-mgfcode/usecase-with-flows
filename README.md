# Templates & Use Cases with Workflows

A comprehensive collection of templates, use cases, and workflows for AI-assisted development using context engineering principles.

## Overview

This repository provides:
- **Production-ready templates** - Tested and proven document structures
- **Custom commands** - Reusable automation for Claude Code
- **Reverse thinking methodology** - Identify risks before they become problems
- **SERENA MCP integration** - Memory persistence for continuous improvement

## Quick Start

### 1. Generate a Release Package

```
/generate-release project:MyProject version:1.0.0 source_dir:/path/to/code scope:full-app
```

### 2. Validate Before Shipping

```
/validate-release package_path:/path/to/Release_v1.0.0 severity:standard
```

### 3. Check for Risks

```
/reverse-check focus:all package_path:/path/to/Release_v1.0.0
```

## Repository Structure

```
usecase-with-flows/
├── .claude/
│   └── commands/                    # Custom slash commands
│       ├── generate-release.md      # Generate documentation package
│       ├── validate-release.md      # Quality assurance
│       ├── reverse-check.md         # Risk analysis
│       ├── update-docs.md           # Incremental updates
│       └── create-template.md       # Create new templates
├── templates/
│   └── release-package/             # Release documentation template
│       ├── README.md                # Template overview
│       ├── implementation-guide.md  # Detailed usage
│       ├── serena-memory-guide.md   # Memory management
│       └── doc-templates/           # Actual templates
│           ├── README.template.md
│           ├── USECASE.template.md
│           ├── DEPLOY.template.md
│           └── TROUBLESHOOTING.template.md
├── CLAUDE.md                        # Global ruleset & conventions
└── README.md                        # This file
```

## Core Templates

### Release Package Template

Generate professional release documentation with:
- README, USECASE, DEPLOY, TROUBLESHOOTING guides
- Architecture and API documentation
- Configuration and changelog
- Reverse thinking validation

**Location:** `templates/release-package/`

**Quick Start:**
```
/generate-release project:MyApp version:1.0.0 source_dir:/dev/myapp scope:full-app
```

## Available Commands

| Command | Description | Time |
|---------|-------------|------|
| `/generate-release` | Create complete documentation package | 30-60 min |
| `/validate-release` | Quality assurance and validation | 10-15 min |
| `/reverse-check` | Risk and gap analysis | 15-20 min |
| `/update-docs` | Incremental documentation updates | 5-10 min |
| `/create-template` | Generate new template skeletons | 10-15 min |

## Key Features

### Reverse Thinking Methodology

Instead of only asking "How does this work?", we also ask:
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

## Workflow

```
START
  │
  ▼
[Load Context] → Previous templates, issues, patterns
  │
  ▼
[Phase 1: Analysis] → Map structure, identify gaps
  │
  ▼
[Phase 2: Generate] → Populate templates
  │
  ▼
[Phase 3: Develop] → Write content, add examples
  │
  ▼
[Phase 4: Validate] → Reverse thinking, risk check
  │
  ▼
[Phase 5: Assemble] → Package and deliver
  │
  ▼
SUCCESS
```

## Usage Examples

### Generate Full Release Package

```
/generate-release project:UserAuth version:2.0.0 source_dir:/home/dev/auth scope:microservice
```

Output:
```
Release_v2.0.0/
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

### Validate with Extended Checks

```
/validate-release package_path:/releases/v2.0.0 severity:extended
```

### Security-Focused Risk Analysis

```
/reverse-check focus:security package_path:/releases/v2.0.0
```

## Documentation

- **[CLAUDE.md](./CLAUDE.md)** - Global conventions and standards
- **[templates/release-package/](./templates/release-package/)** - Release package template
  - [Implementation Guide](./templates/release-package/implementation-guide.md)
  - [SERENA Memory Guide](./templates/release-package/serena-memory-guide.md)

## Contributing

### Adding New Templates

1. Create directory under `templates/`
2. Add README.md with overview
3. Create `doc-templates/` subdirectory
4. Add implementation guide
5. Test with `/validate-release`

### Improving Existing Content

1. Use `/reverse-check` to identify gaps
2. Update templates
3. Test changes
4. Document in commit message

## Quality Standards

All templates must achieve:
- Completeness score >= 95%
- Zero critical issues
- All code examples syntactically correct
- All internal links working

## License

MIT License - See LICENSE file

---

## Getting Help

- Check [CLAUDE.md](./CLAUDE.md) for conventions
- See template-specific guides in `templates/`
- Use `/reverse-check` to identify gaps

---

*Built with context engineering principles for AI-assisted development*
