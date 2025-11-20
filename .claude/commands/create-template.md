# Create Template

Generate a new document template with proper structure and guidance.

## Command Syntax

```
/create-template type:[README|USECASE|DEPLOY|TROUBLESHOOTING|ARCHITECTURE] style:[minimal|standard|comprehensive] project_type:[web_app|library|microservice|cli_tool]
```

## Parameters

- **type** (required): Template type to generate
- **style** (required): Level of detail
  - `minimal`: Essential sections only
  - `standard`: Recommended sections
  - `comprehensive`: All possible sections
- **project_type** (required): Type of project for context-specific content

## Workflow

### Step 1: Template Selection
Based on parameters, select appropriate:
- Section structure
- Example content style
- Terminology
- Code examples

### Step 2: Generate Skeleton
Create document with:
- All required sections
- Placeholder content
- Inline guidance comments
- Example snippets

### Step 3: Add Guidance
Include for each section:
- Purpose explanation
- What to include
- What to avoid
- Good/bad examples

### Step 4: Store in Memory
Save template to SERENA memory for:
- Future reuse
- Pattern learning
- Continuous improvement

## Output Format

Generated template with:
- Section headers
- `<!-- GUIDANCE: ... -->` comments
- `[PLACEHOLDER]` markers
- Example content

## Template Structures by Type

### README Template Sections

**Minimal:**
- Title & Description
- Quick Start
- License

**Standard:**
- Title & Description
- Quick Start
- Key Features
- System Requirements
- Documentation Links
- Getting Help
- License

**Comprehensive:**
- All Standard sections plus:
- What's New
- Roadmap
- Contributing
- Acknowledgments
- Detailed Examples

### USECASE Template Sections

**Minimal:**
- When to Use
- Basic Example

**Standard:**
- When to Use / Don't Use
- Real-World Scenarios (3)
- Performance Expectations
- Next Steps

**Comprehensive:**
- All Standard sections plus:
- Common Patterns
- Comparison with Alternatives
- Advanced Scenarios
- Integration Examples

### DEPLOY Template Sections

**Minimal:**
- Prerequisites
- Installation Steps
- Verification

**Standard:**
- Pre-Deployment Checklist
- Environment Setup
- Installation Steps
- Post-Deployment Verification
- Rollback Procedure

**Comprehensive:**
- All Standard sections plus:
- Multiple Environment Guides
- CI/CD Integration
- Monitoring & Maintenance
- Scaling Procedures
- Disaster Recovery

### TROUBLESHOOTING Template Sections

**Minimal:**
- Quick Diagnosis
- Common Errors (5)

**Standard:**
- Quick Diagnosis
- Error Code Reference (10+)
- Common Issues Matrix
- Debugging Techniques
- Support

**Comprehensive:**
- All Standard sections plus:
- Performance Troubleshooting
- Security Issues
- Known Limitations
- Advanced Debugging
- Log Analysis Guide

### ARCHITECTURE Template Sections

**Minimal:**
- System Overview
- Key Components

**Standard:**
- System Overview
- Component Diagram
- Data Flow
- Key Technologies
- Security Model

**Comprehensive:**
- All Standard sections plus:
- Detailed Component Specs
- API Contracts
- Database Schema
- Scaling Strategy
- Performance Characteristics
- Decision Records

## Project Type Adaptations

### web_app
- Focus on frontend/backend separation
- Include API examples
- Database setup
- Session management

### library
- Focus on API documentation
- Import/require examples
- Version compatibility
- Peer dependencies

### microservice
- Focus on containerization
- Service discovery
- Inter-service communication
- Health checks

### cli_tool
- Focus on command syntax
- Arguments and flags
- Exit codes
- Shell integration
