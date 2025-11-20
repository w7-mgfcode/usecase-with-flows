# CLAUDE.md - Global Ruleset

Project conventions, standards, and guidelines for AI-assisted development.

---

## Project Overview

This repository contains templates and use cases with workflows for generating professional documentation and release packages. It uses context engineering principles to provide comprehensive guidance for AI assistants.

---

## Repository Structure

```
usecase-with-flows/
├── .claude/
│   └── commands/          # Custom slash commands
├── templates/             # Core template collections
│   └── release-package/   # Release documentation template
├── CLAUDE.md              # This file - global rules
└── README.md              # Repository documentation
```

---

## Coding Standards

### General Principles

1. **Clarity over cleverness** - Write code that is easy to understand
2. **Document assumptions** - Never assume, always document
3. **Test everything** - All code examples must be syntactically correct
4. **Consistency** - Follow established patterns

### Documentation Standards

1. **Use visual indicators consistently:**
   - ⚠️ Warnings and critical items
   - ✅ Required steps / success states
   - ℹ️ Notes and contextual information
   - ❌ Common mistakes / anti-patterns

2. **Structure content progressively:**
   - Start with summary/overview
   - Follow with details
   - End with next steps

3. **Include working examples:**
   - All code blocks must be syntactically correct
   - Include expected output
   - Show both good and bad examples when helpful

### Markdown Formatting

- Use ATX-style headers (`#`, `##`, `###`)
- Use fenced code blocks with language specifiers
- Use tables for structured comparisons
- Use horizontal rules (`---`) to separate major sections

---

## Command Patterns

### Slash Command Format

All custom commands follow this structure:

```markdown
# Command Name

Brief description.

## Command Syntax

\`\`\`
/command-name parameter1:[value] parameter2:[value]
\`\`\`

## Parameters

- **parameter1** (required): Description
- **parameter2** (optional): Description

## Workflow

1. Step 1
2. Step 2
3. Step 3

## Output Format

Description of expected output.
```

### Command Naming

- Use lowercase with hyphens: `generate-release`, `validate-release`
- Use verbs: `generate`, `validate`, `check`, `update`, `create`
- Be specific: `reverse-check` not just `check`

---

## Testing Requirements

### Before Committing

1. **Validate all code examples** - Check syntax
2. **Test all links** - Ensure internal links resolve
3. **Check formatting** - Consistent markdown
4. **Run commands** - Test slash commands work

### Quality Gates

- All documents must pass `/validate-release` with severity `standard`
- Zero critical issues allowed
- Completeness score >= 95%

---

## Workflow Guidelines

### Creating New Templates

1. Identify the use case
2. Research best practices
3. Create initial structure
4. Apply reverse thinking
5. Add examples and guidance
6. Validate and iterate

### Updating Existing Templates

1. Document the change reason
2. Update affected sections
3. Maintain consistency
4. Test all examples
5. Update version/date

### Reverse Thinking Process

Always ask:
- What could go wrong?
- What are we assuming?
- What happens when X fails?
- What questions haven't we answered?

---

## SERENA MCP Integration

### Memory Persistence

Store in SERENA memory:
- Templates and their versions
- Reverse thinking findings
- Previous issues and solutions
- Quality scores over time

### Pattern Learning

- Retrieve patterns from previous projects
- Apply lessons learned
- Continuously improve templates

---

## File Organization

### Template Directories

Each template collection should have:
```
template-name/
├── README.md              # Overview and quick start
├── implementation-guide.md # Detailed usage
├── doc-templates/         # Actual templates
│   ├── FILE1.template.md
│   └── FILE2.template.md
└── [other guides]
```

### Naming Conventions

- Templates: `NAME.template.md`
- Guides: `NAME-guide.md`
- Commands: `command-name.md`

---

## Security Guidelines

### Never Include

- API keys or secrets
- Passwords or credentials
- Personal information
- Internal URLs

### Always Include

- Security warnings where relevant
- Permission requirements
- Encryption recommendations
- Audit logging suggestions

---

## Contributing

### Adding New Templates

1. Create directory under `templates/`
2. Add README.md with overview
3. Create doc-templates subdirectory
4. Add implementation guide
5. Test with validation commands

### Improving Existing Content

1. Use `/reverse-check` to identify gaps
2. Update templates
3. Test with `/validate-release`
4. Document changes
5. Update CLAUDE.md if adding new patterns

---

## Quick Reference

### Common Commands

```bash
# Generate release package
/generate-release project:Name version:1.0.0 source_dir:/path scope:full-app

# Validate package
/validate-release package_path:/path severity:standard

# Check for risks
/reverse-check focus:all package_path:/path

# Update documentation
/update-docs package_path:/path changes:new_feature docs_to_update:ALL

# Create new template
/create-template type:README style:standard project_type:web_app
```

### Visual Indicators

| Indicator | Usage |
|-----------|-------|
| ⚠️ | Critical warnings |
| ✅ | Success / required |
| ℹ️ | Information / notes |
| ❌ | Mistakes / don't do |

---

*End of CLAUDE.md*
