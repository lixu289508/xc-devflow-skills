[English](readme.md) | [中文](readme.zh.md)

# xc-devflow-skills

## Project Overview

xc-devflow-skills is an AI-powered collaborative management system for end-to-end product development. It automates the complete workflow from conception to delivery by defining standardized agent workflows for three core roles: Product Manager (PM), Research & Development (RD), and Quality Assurance (QA).

## Core Features

### 🎯 Three-Role Collaboration System
- **Product Manager (PM)**: Responsible for requirement analysis, documentation, and product planning.
- **Research & Development (RD)**: Responsible for technical design, code implementation, and feature development.
- **Quality Assurance (QA)**: Responsible for test planning, test execution, and bug management.

### 📋 Standardized Workflows
- **PM Workflow**: Research & Analysis → Concept Design → Document Writing → Optimization & Refinement
- **RD Workflow**: Study Requirements → Design Solution → Write Plan → Review Plan → Execute Plan → Archive & Trace
- **QA Workflow**: Read Requirements → Read Development Output → Define Test Plan → Write Test Cases → Execute Tests → Submit Bugs

### 🔄 Review Mechanism
Critical stages include human review checkpoints to ensure quality control:
- PM: Research & Analysis phase requires review
- QA: Test Plan Definition phase requires review
- RD: Root Cause Analysis and Fix Proposal phases require review

### 📁 Standardized Document Management
- Uniform document structure standards
- Task tracking via CSV files
- Requirement documentation in Markdown format

## Directory Structure

```
xc-devflow-skills/
├── pm/                    # Product Manager module
│   ├── CLAUDE.md          # PM role specification
│   ├── prd/               # Requirement documents directory
│   │   └── index.csv      # Requirement index
│   └── .claude/skills/pm/
│       └── SKILL.md       # PM skill configuration
│
├── rd/                    # Development module
│   ├── CLAUDE.md          # RD role specification
│   ├── bugs/              # Bug management
│   │   ├── pending.csv    # Pending bugs
│   │   └── archived.csv   # Archived bugs
│   ├── dev/               # Development tasks directory
│   │   └── .gitkeep
│   └── .claude/skills/
│       ├── rd/            # Requirement development skill
│       └── fix/           # Bug fix skill
│
└── qa/                    # QA module
    ├── CLAUDE.md          # QA role specification
    ├── qa-scheme.md       # Complete QA system specification
    ├── qa-role-requirements.txt  # QA role requirements
    ├── test-results/      # Test results directory
    │   └── .gitkeep
    └── .claude/skills/qa/
        └── SKILL.md       # QA skill configuration
```

## Usage Instructions

### Entry Points

Select the appropriate role entry based on your task:

| Command | Role | Function |
|---------|------|----------|
| `/pm`   | Product Manager | Create or modify requirement documents |
| `/rd`   | Developer | Develop requested features |
| `/fix`  | Developer | Fix bugs |
| `/qa`   | Tester | Execute testing workflow |

### Workflow Details

#### Product Manager (PM)
1. **Research & Analysis** ⏸️ Requires review
2. **Concept Design** ▶️ Auto-executed
3. **Document Writing** ▶️ Auto-executed
4. **Optimization & Refinement** ▶️ Auto-executed

#### Developer (RD)
1. Study requirement documents
2. Design technical solutions
3. Write development plan
4. Review plan (requires manual confirmation)
5. Execute development plan
6. Archive and trace results

#### Tester (QA)
1. Read requirement documents
2. Read development outputs
3. Define test plan ⏸️ Requires review
4. Write test cases/scripts
5. Execute tests
6. Submit bugs + Generate report

## Role Specifications

### Product Manager Guidelines
- Output structured requirement documents (PRD)
- Ensure requirements are clear, testable, and implementable
- Follow document formatting standards strictly

### Developer Guidelines
- Code must tightly align with requirements
- Prohibit introduction of technical debt
- Adhere to file modification rules
- Follow proper service startup/shutdown procedures

### QA Guidelines
- Strictly adhere to defined test scope
- Prohibit introduction of technical debt
- Use CSV files规范 for bug tracking
- Fully document test context

## CSV File Specifications

### Requirement Index (pm/prd/index.csv)
Tracks metadata for all requirement documents.

### Pending Bug Queue (rd/bugs/pending.csv)
Bugs reported by QA; to be processed by RD.

### Archived Bug Records (rd/bugs/archived.csv)
Records of bugs fixed and verified, written by RD.

### Pending Test Queue (qa/pending.csv)
Tasks queued by QA for testing.

## Technology Stack

This is a pure specification project consisting of:
- Markdown-formatted specification documents
- CSV-formatted data files
- Claude AI Agent skill configurations

## License

This project is open-source. Refer to the repository for specific licensing details.

## Contribution Guidelines

1. Follow the specifications outlined in each role’s CLAUDE.md file.
2. Ensure document formatting meets required standards.
3. Validate CSV data format correctness before submission.

---

*This project aims to enhance collaboration efficiency in product development teams through AI-driven automated workflows.*