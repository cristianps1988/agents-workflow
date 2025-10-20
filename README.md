<div align="center">

[🇺🇸 English](./README.md) | [🇪🇸 Español](./README.es.md)

# AI-Powered Development Workflow with Claude Code

### Build Production-Grade Applications with AI - The Right Way

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](http://makeapullrequest.com)

**A methodology I developed while building a complex application with AI assistance. It uses specialized agents, context optimization, and architectural enforcement to maintain consistency and quality.**

This approach helped me tackle the challenges of building highly-customized software with AI. I'm sharing it here in case it helps others working on similar projects.

[Getting Started](#prerequisites) • [Core Concepts](#core-concepts) • [Step-by-Step Guide](#step-by-step-implementation) • [Troubleshooting](#common-issues-and-solutions)

---

</div>

## Table of Contents

1. [Introduction](#introduction)
2. [Why This Methodology?](#why-this-methodology)
3. [Prerequisites](#prerequisites)
4. [Core Concepts](#core-concepts)
5. [Step-by-Step Implementation](#step-by-step-implementation)
6. [Common Issues and Solutions](#common-issues-and-solutions)
7. [Resources](#resources)

---

## Introduction

This guide documents a proven methodology for using **Claude Code** and **specialized AI agents** to build software projects with:

- **Higher productivity**: AI writes code aligned with your requirements and policies
- **Fewer errors**: Agents follow strict architectural patterns and constraints
- **Better maintainability**: Consistent code structure and documentation
- **Token efficiency**: Optimized context usage to reduce costs and improve performance

The approach combines:
- **Specialized Agents**: Task-specific agents with clear responsibilities
- **Two-Phase Workflow**: Planning (30% tokens) → Execution (70% tokens)
- **Context Engineering**: Strategic document organization and lazy loading
- **MCP Integration**: Granular tool access per agent
- **Session Protocol**: Append-only logs for KV-cache optimization

---

## Why This Methodology?

### The Problem

When using AI assistants to build complex projects, common challenges include:

1. **Context Bloat**: Loading all documentation upfront consumes excessive tokens
2. **Inconsistent Results**: Agents don't follow project conventions consistently
3. **Architectural Drift**: Code doesn't respect architectural boundaries
4. **Generalist Agents**: One agent trying to do everything leads to lower quality
5. **Lost Context**: No persistent memory of decisions across sessions

### The Solution

This methodology addresses these problems through:

**Context Optimization**
- Critical constraints document (~200 tokens) loaded first
- Lazy loading: Search specific sections instead of full documents
- Session context maintains decisions and history

**Specialized Agents**
- Each agent handles ONE domain (UI, database, domain layer, etc.)
- Agents plan but don't implement → enables review before execution
- Granular MCP access → only tools needed for their specific task

**Two-Phase Workflow**
- Phase 1: Agent creates detailed implementation plan
- Phase 2: Parent agent executes the plan step-by-step
- Benefits: Context reuse, error recovery, incremental progress

**Architectural Enforcement**
- Dependency rules documented and enforced
- Critical constraints prevent common anti-patterns
- Consistent file structure and naming conventions

### Real Results

This methodology was developed and validated through building a **complex, production-grade application** from scratch with highly custom business logic and specific architectural requirements.

**Why Complex Projects Matter**: AI can easily build simple CRUD apps by copying existing patterns. The real challenge is building **highly customized applications** with:
- Custom business logic specific to your domain
- Strict architectural constraints
- Complex state management and data flows
- Multi-layer architectures
- Specific security and validation requirements

**Without this methodology**, AI assistants with simple prompts:
- ❌ Drift from architectural patterns
- ❌ Create inconsistent code across features
- ❌ Violate business rules
- ❌ Repeat mistakes
- ❌ Lose context between sessions

**With this methodology**, proven results:
- ✅ **Token reduction**: ~70% less context usage vs loading everything
- ✅ **Consistency**: All code follows architectural patterns across 100+ files
- ✅ **Parallel work**: Multiple agents can plan different features simultaneously
- ✅ **Quality**: Fewer bugs, better alignment with complex requirements
- ✅ **Maintainability**: Clear patterns, documented decisions, audit trail

---

## Prerequisites

### 1. Install Claude Code

Claude Code is Anthropic's official CLI for AI-assisted development.

**Installation:**

```bash
# macOS/Linux
brew install anthropics/claude/claude

# Or using npm
npm install -g @anthropic/claude-code

# Verify installation
claude --version
```

**Authentication:**

```bash
# Login with your Anthropic account
claude auth login
```

For detailed installation instructions, visit: https://docs.claude.com/claude-code

### 2. Basic Concepts

Before starting, you should understand:

- **Git basics**: This methodology uses git repositories
- **Command line**: Basic terminal/shell navigation
- **Your tech stack**: Framework/libraries you'll use (React, Vue, etc.)
- **Markdown**: Documentation is written in Markdown format

### 3. Your Project Idea

Have a clear idea of:
- What application you want to build
- Main features and functionality
- User roles (if applicable)
- Critical business rules

---

## Core Concepts

### 1. Token Budget Management

**Problem**: Large context = expensive + slower responses

**Solution**: Load only what's needed, when it's needed

**Strategy**:
- Always load `critical-constraints.md` first (~200 tokens)
- Load session context if available (~300-500 tokens)
- Use Grep to search specific sections (~200 tokens) vs loading full docs (~2000-5000 tokens)
- Avoid loading full documentation upfront

**Example Context Budget**:
```
Optimal path:
  critical-constraints.md:     200 tokens
  session context:             300 tokens
  Grep specific section:       200 tokens
  TOTAL:                       700 tokens ✅

Heavy context (avoid):
  Load all knowledge docs:   7,600 tokens ❌
```

### 2. Two-Phase Workflow (Planning → Execution)

**Phase 1: Planning (~30% of tokens)**
- Specialized agent researches codebase
- Creates detailed implementation plan
- Saves plan to `.claude/plans/{type}-{feature}-plan.md`
- Appends summary to session context

**Phase 2: Execution (~70% of tokens)**
- Parent agent (or you) reads the plan
- Implements step-by-step
- Updates session context with progress

**Benefits**:
- Plans can be reviewed before implementation
- Plans are reusable if execution fails
- Parallel planning: Multiple agents plan different features simultaneously
- KV-cache optimization: Stable context prefix

### 3. Specialized Agents

**Key Principle**: One agent, one responsibility

Instead of one generalist agent doing everything, create specialized agents for:
- **UI Design**: Plan components, layouts (no implementation)
- **Database Design**: Schema, migrations, RLS policies (no execution)
- **Domain Layer**: Entities, repositories, services (no code writing)
- **Refactoring**: Analyze anti-patterns, create migration plan
- **Research**: Audit UI/UX, accessibility, performance

**Benefits**:
- Higher quality (agent is expert in its domain)
- Granular MCP access (UI agent gets shadcn MCP, DB agent doesn't need it)
- Parallel execution possible
- Clear separation of concerns

### 4. Append-Only Session Context

**Problem**: Overwriting session files breaks KV-cache (stable prefix needed)

**Solution**: Session files are append-only logs

Each agent appends a new entry:
```markdown
---
## [2025-10-19 14:30] ui-architect: Design Work Order Form

**Task**: Create form for admin work order creation

**Status**: ✅ Plan Ready

**Plan Location**: .claude/plans/ui-work-order-form-plan.md

**Key Decisions**:
- Use shadcn/ui Form components
- Multi-step form (client selection → job details → review)
- Text externalized to work-order-form.text-map.ts

---
```

**Benefits**:
- Maintains KV-cache efficiency (stable prefix)
- Audit trail of all decisions
- Context for future agents
- Error learning (failed attempts documented)

### 5. MCP Configuration Strategy

**Problem**: Loading all MCP servers in all agents wastes tokens

**Solution**: Granular MCP access per agent type

**Example**:
- UI Agent: shadcn MCP (~4.7k tokens) ✅, Playwright MCP ❌
- Database Agent: Supabase MCP ✅, shadcn MCP ❌
- UX Audit Agent: shadcn + Playwright (~20k tokens) ✅

**Configuration**: `.mcp.json` defines available servers, `.claude/settings.local.json` enables/disables per session

### 6. Critical Constraints First

**Every agent must read `.claude/knowledge/critical-constraints.md` FIRST**

This document (~200 tokens) contains non-negotiable rules that prevent common mistakes:

Example constraints:
- ✅ DO: Use repository pattern for data access
- ❌ DON'T: Direct database imports in components
- ✅ DO: Externalize all text to text maps
- ❌ DON'T: Hardcoded strings in UI

### 7. Architecture Patterns Enforcement

Choose ONE architecture and document it clearly:
- Clean Architecture (domain-driven)
- Hexagonal Architecture
- Feature-based Architecture
- Layered Architecture

**Key**: Document dependency rules and enforce them

Example (Clean Architecture):
```
core/              ← NEVER imports from outer layers
  ↑
infrastructure/    ← CAN import from core/, NEVER from features/
  ↑
features/          ← CAN import from core/ and infrastructure/
```

---

## Step-by-Step Implementation

### Step 0: Define Your Project

Before creating any files, have a conversation with Claude Code to generate your project documentation.

**What to describe**:

1. **Application Overview**
   - What problem does it solve?
   - Who are the users?
   - What are the main features?

2. **User Roles** (if applicable)
   - What types of users exist?
   - What can each role do?

3. **Critical Business Rules**
   - What validations are required?
   - What state transitions exist?
   - What constraints must be enforced?

4. **Tech Stack** (if decided)
   - Frontend framework
   - Backend/Database
   - Key libraries

**Prompt Example**:

```
I want to build a [description of your app].

Users: [describe user types and their capabilities]

Key features: [list main features]

Business rules: [describe critical rules, validations, workflows]

Tech stack: [if decided]

Please create a PROJECT.md document that includes:
- Product Overview
- Features and Functionality
- User Roles and Permissions
- Data Models (high-level entities)
- Business Rules
- Any other relevant sections

Make it comprehensive enough that an AI agent can understand
the full context of what we're building.
```

**Output**: `PROJECT.md` in your repository root

**Then**: Ask Claude Code to extract the essential business rules to `.claude/knowledge/business-rules.md` (optimized version for agents)

---

### Step 1: Create Project Structure

Create the base directory structure for the methodology:

```bash
# Create .claude directory structure
mkdir -p .claude/knowledge
mkdir -p .claude/agents
mkdir -p .claude/tasks
mkdir -p .claude/plans
mkdir -p .claude/reports

# Create .gitkeep files to preserve empty directories
touch .claude/plans/.gitkeep
touch .claude/reports/.gitkeep
```

**Directory Purpose**:
- `.claude/knowledge/`: Token-optimized documentation for agents
- `.claude/agents/`: Specialized agent definitions
- `.claude/tasks/`: Session context files (append-only logs)
- `.claude/plans/`: Implementation plans generated by agents
- `.claude/reports/`: Analysis and audit reports

---

### Step 2: Configure CLAUDE.md

Create `CLAUDE.md` in your repository root. This is the **entry point** for all AI agents.

**Purpose**:
- First file agents read (~800 tokens maximum)
- Points to critical constraints
- Explains workflow and protocols
- References other documentation

**Template**: See `CLAUDE.md` in this repository

**Key Sections** (customize for your project):

1. **Project Overview** (2-3 sentences)
2. **Critical Instructions**
   - ALWAYS read `.claude/knowledge/critical-constraints.md` first
   - Check for session context file if session_id provided
   - Follow workflow protocol for new features
3. **Workflow Summary**
   - When to use agents vs direct implementation
   - How to create session contexts
   - Where plans are stored
4. **Documentation Map**
   - Links to knowledge documents
   - When to use each document
5. **MCP Configuration Notes**
   - What MCP servers are available
   - How to enable/disable

**⚠️ Important**: Keep this file under 1000 tokens. It's loaded in every agent invocation.

---

### Step 3: Create Critical Constraints

Create `.claude/knowledge/critical-constraints.md`

**Purpose**:
- Non-negotiable rules (~200 tokens)
- Loaded by EVERY agent FIRST
- Prevents common anti-patterns

**What to include**:

1. **Architectural Rules** (5-10 critical rules)
   - Dependency rules
   - What NOT to do
   - What patterns to ALWAYS use

2. **Code Standards** (must-follow)
   - Import patterns
   - Naming conventions
   - File organization rules

3. **Always-Do Patterns**
   - Required practices
   - Mandatory checks

**Template**: See `.claude/knowledge/critical-constraints.md`

**Example Structure**:

```markdown
# Critical Constraints

**Rules that MUST be followed in all code. ~200 tokens.**

---

## 1. [Your Critical Rule #1]

❌ **NEVER**: [What not to do]
✅ **ALWAYS**: [What to do instead]

**Example**:
[Code example showing correct pattern]

---

## 2. [Your Critical Rule #2]
...
```

**✅ Obligatory Elements**:
- Must be concise (~200 tokens total)
- Must use ❌/✅ format for clarity
- Must include code examples
- Must cover your most critical architectural rules

---

### Step 4: Configure MCP Servers

Create `.mcp.json` in your repository root.

**Purpose**: Define MCP servers available to agents

**What are MCP servers?**
Model Context Protocol servers provide tools to agents (e.g., UI components, database access, browser automation).

**Template**: See `.mcp.json`

**Example**:

```json
{
  "mcpServers": {
    "your-tool-name": {
      "command": "npx",
      "args": ["package-name", "command"]
    }
  }
}
```

**Common MCP Servers**:
- `shadcn`: UI component library (~4.7k tokens)
- `@playwright/mcp`: Browser automation (~14k tokens)
- `supabase`: Database access
- Custom servers for your specific tools

**Strategy**:
- Define ALL available servers in `.mcp.json`
- Enable/disable per session using `.claude/settings.local.json`
- Only enable what the current agent needs

**Example `.claude/settings.local.json`**:
```json
{
  "enabledMcpjsonServers": ["shadcn"],
  "enableAllProjectMcpServers": false
}
```

**Token Optimization**:
- UI work: Enable only UI-related MCPs
- Database work: Enable only database MCPs
- UX audit: Enable UI + browser automation MCPs

---

### Step 5: Define Your Architecture

Create `.claude/knowledge/architecture-patterns.md`

**Purpose**: Document your chosen architecture and enforce consistency

**What to include**:

1. **Architecture Choice**
   - Which architecture pattern you're using
   - Why you chose it
   - High-level diagram or description

2. **Layer Definitions**
   - What each layer is responsible for
   - What each layer contains
   - Examples of files in each layer

3. **Dependency Rules** (CRITICAL)
   - What can import from what
   - What is forbidden
   - Visual representation

4. **Patterns and Practices**
   - Repository pattern
   - Service pattern
   - Presenter pattern (if applicable)
   - State management strategy

5. **Code Examples**
   - Before/after examples
   - Correct vs incorrect patterns

**Template**: See `.claude/knowledge/architecture-patterns.md`

**Example (Clean Architecture)**:

```markdown
# Architecture Patterns

## Dependency Rule

```
core/              ← Framework-agnostic, NEVER imports from outer layers
  ↑
infrastructure/    ← Implementations, CAN import from core/
  ↑
features/          ← UI layer, CAN import from core/ and infrastructure/
  ↑
app/               ← App setup, CAN import from all layers
```

## Layers

### Domain Layer (core/)
**Responsibility**: Business logic, framework-agnostic

**Contains**:
- Entities: `core/entities/{name}.entity.ts`
- Repository interfaces: `core/repositories/{name}-repository.interface.ts`
- Services: `core/services/{name}-service.ts`

**Rules**:
- ❌ NO framework imports (React, Supabase, etc.)
- ✅ Pure TypeScript/JavaScript
- ✅ Platform-agnostic

### Infrastructure Layer
**Responsibility**: External implementations

**Contains**:
- API implementations: `infrastructure/api/{name}-api.ts`
- Repository implementations (implements interfaces from core/)

**Rules**:
- ✅ CAN import from core/
- ✅ CAN use external libraries (Supabase, Axios, etc.)
- ❌ NEVER import from features/ or app/

[Continue with other layers...]
```

**Note**: Adapt to YOUR chosen architecture. This is just an example.

---

### Step 6: Document Business Rules

You already created this in Step 0 when Claude Code extracted it from PROJECT.md.

**File**: `.claude/knowledge/business-rules.md`

**Purpose**:
- Essential business logic (~500-2000 tokens)
- Optimized version of PROJECT.md
- Agents read this via Grep for specific sections

**What to include**:

1. **User Roles and Permissions**
   - What each role can/cannot do
   - Access control rules

2. **Entity States and Transitions**
   - Valid states for your entities
   - Allowed transitions
   - Who can change states

3. **Validation Rules**
   - Required fields
   - Format validations
   - Business constraints

4. **Data Visibility Rules**
   - What data each role can see
   - Filtering rules
   - Privacy constraints

**Template**: See `.claude/knowledge/business-rules.md`

**Example**:

```markdown
# Business Rules

**Essential business logic for AI agents.**

---

## User Roles

### Admin
- **Can**: Create items, manage users, view all data
- **Cannot**: Delete without authorization

### Standard User
- **Can**: View own items, create items
- **Cannot**: See other users' data, modify system settings

---

## [Entity] Status Transitions

```
draft → submitted → approved → completed
  ↓         ↓           ↓
cancelled cancelled cancelled
```

| Status | Meaning | Who Can Set |
|--------|---------|-------------|
| draft | Initial state | User (creator) |
| submitted | Ready for review | User |
| approved | Approved by admin | Admin only |
| completed | Work done | User or Admin |
| cancelled | Cancelled | Admin only |

---

[Continue with other rules...]
```

---

### Step 7: Configure Context Strategy

Create `.claude/knowledge/context-strategy.md`

**Purpose**: Teach agents how to load context efficiently

**What to include**:

1. **Decision Tree**
   - When to load what documents
   - How to choose between full read vs Grep
   - Token budgets for different scenarios

2. **Lazy Loading Strategy**
   - Grep-first approach
   - Targeted reads with offset/limit
   - When to load full documents (rarely)

3. **Context Budget Examples**
   - Optimal paths (~500-1000 tokens)
   - Heavy paths to avoid (~5000+ tokens)

4. **Document Token Counts**
   - Reference table of all docs and their sizes

**Template**: See `.claude/knowledge/context-strategy.md`

**Example**:

```markdown
# Context Strategy

**How to load context efficiently.**

---

## Decision Tree

```
Start: New Agent Task
↓
ALWAYS READ critical-constraints.md (~200 tokens)
↓
Session ID provided?
├─ YES: Read .claude/tasks/context_session_{id}.md (~300 tokens)
└─ NO: Continue
↓
Need architecture info?
├─ YES: Grep for specific layer/pattern (~200 tokens)
└─ NO: Continue
↓
Need business rules?
├─ YES: Grep for specific entity/rule (~200 tokens)
└─ NO: Continue
```

---

## Lazy Loading Patterns

### Pattern 1: Grep First
**When**: Looking for specific information
**How**: Use Grep with pattern, read matched sections only

Example:
```
Grep: pattern="## Repository Pattern",
      file="architecture-patterns.md",
      -A=30
Result: ~200 tokens vs 4000 for full file ✅
```

### Pattern 2: Targeted Read
**When**: You know the section location
**How**: Use Read with offset/limit

Example:
```
Read: file="business-rules.md",
      offset=100,
      limit=50
Result: ~300 tokens vs 2000 for full file ✅
```

---

## Context Budget Examples

### Optimal Path (UI Agent)
- critical-constraints.md: 200 tokens
- session context: 300 tokens
- Grep file-structure.md: 150 tokens
- **TOTAL: 650 tokens** ✅

### Heavy Path (AVOID)
- Load all knowledge docs: 7600 tokens ❌

---

## Token Reference Table

| Document | Size | Load Strategy |
|----------|------|---------------|
| critical-constraints.md | 200 | Always full read |
| business-rules.md | 2000 | Grep sections only |
| architecture-patterns.md | 4000 | Grep sections only |
| context-strategy.md | 3500 | Rarely needed |
| file-structure.md | 500 | Read if needed |
| tech-stack.md | 300 | Read if needed |
```

---

### Step 8: Define File Structure Conventions

Create `.claude/knowledge/file-structure.md`

**Purpose**: Document naming conventions and file organization

**What to include**:

1. **File Naming Conventions**
   - Components
   - Pages
   - Hooks
   - Services
   - Utilities
   - Tests

2. **Directory Structure**
   - Where each type of file goes
   - Grouping strategy (by feature, by type, etc.)

3. **Import Patterns**
   - Absolute vs relative imports
   - Import ordering
   - Barrel files (use or avoid?)

**Template**: See `.claude/knowledge/file-structure.md`

**Example**:

```markdown
# File Structure

**Naming conventions and file organization.**

---

## File Naming

| Type | Convention | Example |
|------|-----------|---------|
| React Components | PascalCase.tsx | `UserProfile.tsx` |
| Pages | PascalCase + Page.tsx | `UserProfilePage.tsx` |
| Hooks | use-kebab-case.ts | `use-user-profile.ts` |
| Services | kebab-case-service.ts | `user-service.ts` |
| Utilities | kebab-case.ts | `format-date.ts` |
| Types | kebab-case.types.ts | `user.types.ts` |
| Tests | {filename}.test.ts | `user-service.test.ts` |

---

## Directory Structure

```
src/
├── features/           # Feature-based modules
│   └── user-profile/
│       ├── components/      # Feature-specific components
│       ├── hooks/           # Feature-specific hooks
│       ├── pages/           # Feature pages
│       └── utils/           # Feature utilities
├── shared/             # Shared across features
│   ├── components/          # Reusable components
│   ├── hooks/               # Reusable hooks
│   ├── utils/               # Shared utilities
│   └── types/               # Shared types
└── core/               # Business logic (if using Clean Arch)
    ├── entities/
    ├── services/
    └── repositories/
```

---

## Import Patterns

### ✅ DO: Direct Imports
```typescript
import { User } from '@/core/entities/user.entity';
import { UserService } from '@/core/services/user-service';
```

### ❌ DON'T: Barrel Files (if that's your rule)
```typescript
// Avoid index.ts that re-exports everything
import { User } from '@/core/entities'; // ❌
```

### Import Ordering
```typescript
// 1. External dependencies
import React from 'react';
import { useQuery } from '@tanstack/react-query';

// 2. Internal modules (absolute imports)
import { User } from '@/core/entities/user.entity';
import { useUserService } from '@/hooks/use-user-service';

// 3. Relative imports
import { UserCard } from './UserCard';

// 4. Styles
import './styles.css';
```
```

---

### Step 9: Document Tech Stack

Create `.claude/knowledge/tech-stack.md`

**Purpose**: Reference for versions, commands, and library usage

**What to include**:

1. **Core Technologies**
   - Framework and version
   - Language and version
   - Build tool

2. **Key Dependencies**
   - UI libraries
   - State management
   - Data fetching
   - Form handling
   - Routing
   - Testing

3. **Development Tools**
   - Package manager (npm, pnpm, yarn)
   - Linter/Formatter
   - Testing framework

4. **Commands Reference**
   - Install dependencies
   - Run dev server
   - Build production
   - Run tests
   - Format code

**Template**: See `.claude/knowledge/tech-stack.md`

**Example**:

```markdown
# Tech Stack

**Technologies, versions, and commands.**

---

## Core Stack

- **Framework**: React 18.3.1
- **Language**: TypeScript 5.6.0
- **Build Tool**: Vite 5.4.0
- **Package Manager**: **pnpm ONLY** (NOT npm/yarn/bun)

---

## Key Dependencies

### UI
- **shadcn/ui**: Component library (headless components)
- **Tailwind CSS**: Utility-first CSS

### State Management
- **React Query**: Server state (data fetching, caching)
- **Zustand**: Client state (UI state, auth token)

### Forms
- **React Hook Form**: Form state management
- **Zod**: Schema validation

### Routing
- **React Router**: Client-side routing

---

## Development Tools

- **Formatter**: Biome (tabs, double quotes)
- **Testing**: Vitest + React Testing Library
- **Git Hooks**: Husky + lint-staged

---

## Commands

```bash
# Install dependencies
pnpm install

# Development server
pnpm dev

# Build for production
pnpm build

# Run tests
pnpm test

# Format code
pnpm format

# Lint
pnpm lint
```

---

## Important Notes

⚠️ **ALWAYS use pnpm**, never npm/yarn/bun
⚠️ Use Biome for formatting (NOT Prettier)
```

---

### Step 10: Create Your First Specialized Agent

**Two templates available**:

1. **`.claude/agents/specialized-agent-template.md`** - Generic template with instructions
2. **`.claude/agents/ui-architect.md`** - Example UI agent (customize for your stack)

**To create a new agent**:

1. **Copy a template**: Start with `specialized-agent-template.md`
2. **Fill in frontmatter**:
   ```yaml
   ---
   name: your-agent-name
   description: One-line description. Creates implementation plans for parent agent to execute.
   model: sonnet
   color: cyan
   ---
   ```
3. **Replace placeholders** with your specific content
4. **Keep it concise**: ~100-150 lines (not 500+)
5. **Save to**: `.claude/agents/your-agent-name.md`

**What to customize**:

1. **Agent Identity**
   - Name and specialization
   - What it does / What it doesn't do

2. **Mission Workflow** (5 steps - keep these)
   1. Read context session
   2. Research codebase
   3. Design [agent's domain]
   4. Create plan
   5. Append to context

3. **Project Constraints** (CRITICAL)
   - Your architecture rules
   - Your technology choices
   - Your naming conventions

4. **Allowed Tools**
   - Which MCP servers
   - Which file operations

5. **Plan Template**
   - Structure specific to this agent's domain

**✅ OBLIGATORY Elements** (keep in ALL agents):
- Frontmatter (name, description, model, color)
- 5-step Mission workflow
- Must read `critical-constraints.md` first
- Must create plan in `.claude/plans/`
- Must append (not overwrite) to session context
- Must NOT implement code directly
- Rules section (numbered, clear)

**Template structure overview**:

**Example (UI Architecture Agent)**:

```markdown
# UI Architecture Agent

**Specialized agent for planning UI components and layouts.**

---

## Agent Identity

**Name**: ui-architect

**Purpose**: Design and plan user interface components, layouts, and screens

**What I Do**:
- Research existing UI components and patterns
- Design new component architecture
- Create detailed implementation plans
- Specify component hierarchy and data flow

**What I DON'T Do**:
- ❌ I do NOT write implementation code
- ❌ I do NOT execute the plan
- ❌ I do NOT modify existing components directly

---

## Mission

When invoked, I follow this process:

### 1. Read Context
- Read `.claude/knowledge/critical-constraints.md` (ALWAYS FIRST)
- Read session context file if `session_id` provided: `.claude/tasks/context_session_{id}.md`

### 2. Research Codebase
- Use Grep to find existing components: `pattern="export.*function.*Component"`
- Use Glob to find UI files: `pattern="**/*{Page,Form,Modal,Card}.tsx"`
- Read relevant text maps and hooks
- Check current UI patterns and conventions

### 3. Design Component Architecture
- Define component hierarchy
- Specify props and state
- Identify reusable components
- Plan text externalization (text maps)
- Consider accessibility (ARIA labels, keyboard nav)

### 4. Create Implementation Plan
- Write plan to `.claude/plans/ui-{feature}-plan.md`
- Include: Overview, Components List, Files to Create/Modify, Implementation Steps, Text Maps, Integration Notes

### 5. Update Session Context
- Append entry to `.claude/tasks/context_session_{id}.md` (NEVER overwrite)
- Include: Task summary, Status, Plan location, Key decisions

---

## ✅ OBLIGATORY: Output Protocol

**MUST CREATE**: `.claude/plans/ui-{feature}-plan.md`

**MUST APPEND** (never overwrite): `.claude/tasks/context_session_{id}.md`

**MUST NOT**: Write implementation code or modify source files

---

## ✅ OBLIGATORY: Constraints

### Architectural Rules
1. **Follow component conventions** from `.claude/knowledge/file-structure.md`
2. **All text externalized** to text maps (no hardcoded strings)
3. **Use design system** components (e.g., shadcn/ui) - no custom primitives
4. **Accessibility required**: ARIA labels, keyboard navigation, focus management

### Data Access
- ❌ NO direct database calls in components
- ✅ Use hooks for data fetching
- ✅ Use repository pattern via custom hooks

---

## Allowed Tools

**MCP Servers**:
- `shadcn`: Access to UI component library

**File Operations**:
- Read: To research existing code
- Grep: To search for patterns
- Glob: To find files
- Write: ONLY to create plans in `.claude/plans/`

**NOT Allowed**:
- Edit (no code modification)
- Bash (no command execution)

---

## Output Format

### Plan Structure

```markdown
# UI Implementation Plan: {Feature Name}

## 1. Overview
[Brief description of the UI feature]

## 2. Components
[List of components to create/modify]

### {ComponentName}
- **Purpose**: [What it does]
- **Props**: [TypeScript interface]
- **State**: [Local state needed]
- **Hooks Used**: [Data fetching, form handling, etc.]

## 3. Files

### New Files
- `path/to/Component.tsx` - [Description]

### Modified Files
- `path/to/existing.tsx` - [What changes]

## 4. Implementation Steps
1. [Step-by-step instructions]
2. [...]

## 5. Text Maps
**File**: `shared/constants/text/{feature}.text-map.ts`

```typescript
export const featureTextMap = {
  buttons: {
    submit: "Submit",
    cancel: "Cancel"
  }
} as const;
```

## 6. Accessibility Checklist
- [ ] ARIA labels on interactive elements
- [ ] Keyboard navigation support
- [ ] Focus management
- [ ] Screen reader testing

## 7. Integration Notes
[How this integrates with existing code]

## 8. Notes
[Additional considerations]
```

---

## Example Usage

**Invocation**:
```
Launch UI Architecture Agent with session_id="session_12345"
to design work order creation form
```

**Agent Process**:
1. Reads critical-constraints.md
2. Reads context_session_12345.md
3. Searches for existing forms: Grep pattern="Form.tsx"
4. Designs form architecture
5. Creates plan: .claude/plans/ui-work-order-form-plan.md
6. Appends to session: context_session_12345.md

**Output**: Detailed plan ready for implementation by parent agent
```

**✅ Obligatory Elements for ALL Agents**:
- Must read `critical-constraints.md` first
- Must create plan in `.claude/plans/`
- Must append (not overwrite) to session context
- Must NOT implement code directly
- Must specify allowed tools

**Customize for Your Agents**:
- Database architecture agent
- Domain layer architect
- Refactoring agent
- UX research agent
- etc.

---

### Step 11: Register Your Agents in CLAUDE.md

**⚠️ CRITICAL STEP**: After creating your specialized agents, you **must register them in CLAUDE.md** so the parent agent knows they exist and can invoke them.

**Why this matters**:
- The parent agent reads CLAUDE.md to discover available specialized agents
- Without registration, the parent agent won't know which agents it can delegate to
- This is how the automatic orchestration works

**How to register agents**:

1. **Open `CLAUDE.md`** in your repository root

2. **Find the "Available agents in this project" section** (around line 40-50)

3. **Add your agents to the list**:

```markdown
**Available agents in this project:**
- **UI/Frontend** → `.claude/agents/ui-architect.md`
- **Database** → `.claude/agents/database-engineer.md`
- **Business Logic** → `.claude/agents/domain-architect.md`
- **Refactoring** → `.claude/agents/migration-agent.md`
```

**⚠️ Important**:
- Update this list every time you create a new agent
- Keep the format consistent: `**{Task Type}** → {path-to-agent-file}`
- Use clear task types so the parent agent knows when to use each agent

**Example of parent agent discovery**:

When you say: "Implement the login view"

The parent agent:
1. Reads CLAUDE.md
2. Sees "UI/Frontend → .claude/agents/ui-architect.md"
3. Understands it has a UI specialist available
4. Invokes that agent to create the UI plan

Without this registration, the parent agent would try to do everything itself instead of delegating to specialists.

---

### Step 12: Configure Session Protocol

Create `.claude/tasks/README.md`

**Purpose**: Explain the append-only session protocol

**What to include**:

1. **Session Concept**
   - What sessions are for
   - Why append-only (KV-cache optimization)
   - When to create sessions

2. **Session Lifecycle**
   - Creating a session
   - Active sessions
   - Completing sessions
   - Archiving old sessions

3. **Entry Format**
   - Required fields
   - Status values
   - Token budget per entry

4. **Best Practices**
   - What to include
   - What to omit
   - How to keep entries concise

**Template**: See `.claude/tasks/README.md`

**Example**:

```markdown
# Session Context Protocol

**Append-only logs for persistent context across agent invocations.**

---

## What Are Sessions?

Sessions are **append-only log files** that track:
- Agent actions and decisions
- Implementation progress
- Blockers and errors
- Key decisions and rationale

**Why Append-Only?**
- **KV-Cache Optimization**: Stable prefix = faster responses, lower costs
- **Audit Trail**: Complete history of work
- **Error Learning**: Failed attempts inform future tries
- **Context Reuse**: Agents continue where others left off

---

## When to Create Sessions

**Create a session for**:
- New feature development
- Complex refactoring
- Multi-step tasks requiring multiple agent invocations

**Don't create sessions for**:
- Trivial changes (typo fixes, simple edits)
- One-off tasks
- Exploratory work

---

## Creating a Session

```bash
# Generate session ID
session_id="session_$(date +%s)"

# Copy template
cp .claude/tasks/context_session_template.md \
   .claude/tasks/context_session_${session_id}.md

# Fill in initial information
# Then provide session_id to agents
```

---

## Entry Format

Each agent appends a new entry:

```markdown
---
## [YYYY-MM-DD HH:MM] {agent-name}: {Action Title}

**Task**: {One-line description}

**Status**: {Status emoji + text}

**Plan Location**: {Path to plan if created}

**Key Decisions**:
- {Decision 1}
- {Decision 2}
- {Decision 3}

**Next Steps**:
- {Step 1}
- {Step 2}

---
```

### Status Values

- 🟢 **Active**: Currently working
- 🟡 **Paused**: Temporarily stopped
- ✅ **Completed**: Work finished successfully
- ⚠️ **Blocked**: Waiting on dependency or decision
- ❌ **Failed**: Encountered error, needs intervention

---

## Token Budget

**Target**: ~300-500 tokens per entry

**Include**:
- ✅ Task summary (1-2 sentences)
- ✅ Key decisions (3-5 bullets)
- ✅ Status and next steps
- ✅ Plan location or important file paths

**Exclude**:
- ❌ Full code blocks (link to files instead)
- ❌ Detailed implementation steps (put in plans)
- ❌ Debug logs (summarize the issue)
- ❌ Exploration notes (only final decisions)

---

## Best Practices

1. **Never Overwrite**: Always append new entries at the end
2. **Concise Summaries**: High-level only, details in plans
3. **Link to Plans**: Reference `.claude/plans/{plan}.md` instead of duplicating
4. **Update Status**: Mark status changes (Active → Completed)
5. **Archive Old Sessions**: Move to `.claude/tasks/archive/` after 30 days

---

## Example Session

```markdown
# Session Context: User Profile Feature

**Session ID**: session_1729012345
**Created**: 2025-10-19
**Status**: 🟢 Active

---

## [2025-10-19 10:00] ui-architect: Design User Profile Page

**Task**: Create user profile page with edit functionality

**Status**: ✅ Plan Ready

**Plan Location**: .claude/plans/ui-user-profile-plan.md

**Key Decisions**:
- Split into ProfileView and ProfileEdit components
- Use React Hook Form for edit mode
- Text externalized to user-profile.text-map.ts

**Next Steps**:
- Implement ProfileView component
- Implement ProfileEdit component
- Create text map

---

## [2025-10-19 11:30] parent-agent: Implement Profile Components

**Task**: Execute UI plan for user profile

**Status**: 🟢 Active (2/3 components done)

**Progress**:
- ✅ Created ProfileView component
- ✅ Created ProfileEdit component
- ⏳ Creating user-profile.text-map.ts

---

## [2025-10-19 14:00] parent-agent: Profile Implementation Complete

**Task**: Finalize user profile feature

**Status**: ✅ Completed

**Completed**:
- All components implemented
- Text map created and integrated
- Tests passing
- Accessibility verified

**Files Modified**:
- features/user-profile/components/ProfileView.tsx
- features/user-profile/components/ProfileEdit.tsx
- shared/constants/text/user-profile.text-map.ts

---
```

---

## Archiving

After 30 days or when session is complete:

```bash
# Move to archive
mv .claude/tasks/context_session_12345.md \
   .claude/tasks/archive/
```

This prevents "context rot" from accumulated old sessions.
```

---

**Also Create**: `.claude/tasks/context_session_template.md`

```markdown
# Session Context: {Feature Name}

**Session ID**: {session_id}
**Created**: {YYYY-MM-DD}
**Status**: 🟢 Active | 🟡 Paused | ✅ Completed | ⚠️ Blocked | ❌ Failed

**Objective**: {High-level goal of this session}

---

## [YYYY-MM-DD HH:MM] {agent-name}: {Action Title}

**Task**: {One-line task description}

**Status**: {Status with emoji}

**Key Decisions**:
- {Decision 1}
- {Decision 2}

**Next Steps**:
- {Step 1}
- {Step 2}

---

<!-- Agents: Append new entries below, NEVER overwrite above -->
```

---

### Step 13: Workflow - Putting It All Together

Now that all configuration is in place, here's the complete workflow for building features:

#### The Automatic Orchestration Workflow

The beauty of this methodology is that **the parent agent orchestrates everything automatically**. You don't need to manually create session files or decide which specialized agents to invoke.

**Your Simple Workflow**:

**1. Describe What You Want**

Simply tell Claude Code what feature you want to implement:

```
Implement the login view.

Remember you have specialized sub-agents you can use if needed.
Remember we follow a strict workflow framework.
```

**That's it!** The parent agent automatically:

1. **Creates the session file** with a unique session_id
2. **Analyzes the request** and determines which specialized agents are needed
3. **Invokes specialized agents** (UI architect, database engineer, etc.)
4. **Receives plans** from specialized agents
5. **Updates session context** with plan summaries (append-only)
6. **Executes the plans** step-by-step
7. **Updates session context** after each phase completion (append-only)

**Example of What Happens Behind the Scenes**:

```
You: "Implement the login view"

Parent Agent:
  ↓
  Creates: .claude/tasks/context_session_1729012345.md
  ↓
  Invokes: ui-architect agent
  ↓
  Receives: .claude/plans/ui-login-view-plan.md
  ↓
  Updates: session context with plan summary
  ↓
  Executes: Step 1 of plan (create LoginForm component)
  ↓
  Updates: session context (Step 1 completed)
  ↓
  Executes: Step 2 of plan (create validation logic)
  ↓
  Updates: session context (Step 2 completed)
  ↓
  ... continues until feature is complete
  ↓
  Updates: session context (Feature completed)
```

**Key Reminders to Include in Your Prompts**:

1. **"Remember you have specialized sub-agents you can use if needed"**
   - Reminds the parent agent to delegate to specialists

2. **"Remember we follow a strict workflow framework"**
   - Ensures the agent follows the two-phase workflow
   - Creates plans before implementation
   - Updates session context appropriately

**⚠️ Important: Implement Features Incrementally**

**DON'T** try to build your entire application in one prompt:
```
❌ "Build the entire task management app with authentication,
    dashboard, CRUD operations, and reporting"
```

**DO** implement features one by one:
```
✅ "Implement the login view"
✅ "Implement the user registration flow"
✅ "Implement the task list view"
✅ "Implement task creation functionality"
```

**Why incremental?**
- Better quality per feature
- Easier to review and test
- Easier to fix if something goes wrong
- More maintainable session contexts
- Better token efficiency

#### For Trivial Changes

Skip the workflow for simple changes:
- Typo corrections
- Minor styling tweaks
- Documentation updates
- Simple bug fixes

Just tell Claude Code directly what to fix:
```
Fix the typo in the login button text
```

---

### Step 14: Iterating and Improving

As you use this methodology:

**1. Track What Works**
- Note patterns that improve quality
- Document successful agent configurations
- Update critical-constraints.md with new rules

**2. Optimize Token Usage**
- Monitor which documents consume most tokens
- Refine context-strategy.md
- Extract frequently-used sections to smaller docs

**3. Refine Agents**
- Improve agent instructions based on results
- Add constraints for common mistakes
- Adjust MCP access as needed

**4. Update Documentation**
- Keep business-rules.md current with new rules
- Update architecture-patterns.md when patterns evolve
- Maintain tech-stack.md with version updates

**5. Archive Old Sessions**
- Move completed sessions to archive/ after 30 days
- Prevents context bloat

---

## Common Issues and Solutions

### Issue 1: Agent Doesn't Follow Critical Constraints

**Problem**: Agent violates architectural rules or critical patterns

**Causes**:
- Didn't read critical-constraints.md first
- Constraints not explicit enough
- Conflicting instructions in different docs

**Solutions**:
1. **In CLAUDE.md**, make reading critical-constraints.md the FIRST instruction
2. Add explicit examples to critical-constraints.md (❌ vs ✅)
3. Use strong language: "MUST", "NEVER", "ALWAYS"
4. Add constraint to agent's own definition
5. Review all docs for conflicting guidance

**Example Fix**:

In `CLAUDE.md`:
```markdown
## CRITICAL - READ FIRST

**BEFORE doing anything else**, you MUST read:
`.claude/knowledge/critical-constraints.md`

This document contains non-negotiable rules. Violating these rules is unacceptable.
```

In agent definition:
```markdown
## Mission

### Step 1: Read Critical Constraints (MANDATORY)

Read `.claude/knowledge/critical-constraints.md` IN FULL before proceeding.

Verify you understand all rules. If any rule is unclear, ask before continuing.
```

---

### Issue 2: Excessive Token Usage

**Problem**: Context consumption is too high, slow responses, high costs

**Causes**:
- Loading full documents instead of searching
- All MCP servers enabled simultaneously
- Session context files too large
- Knowledge docs not optimized

**Solutions**:

1. **Optimize MCP Configuration**
   ```json
   // .claude/settings.local.json
   // Enable only what current agent needs
   {
     "enabledMcpjsonServers": ["shadcn"], // Not all servers
     "enableAllProjectMcpServers": false
   }
   ```

2. **Use Grep Instead of Full Reads**
   ```markdown
   ❌ Read: .claude/knowledge/business-rules.md (2000 tokens)

   ✅ Grep: pattern="## User Roles",
           path=".claude/knowledge/business-rules.md",
           -A=20
           (200 tokens)
   ```

3. **Compress Knowledge Docs**
   - Remove examples that can be inferred
   - Use tables instead of paragraphs
   - Link to external docs instead of duplicating

4. **Keep Session Entries Concise**
   - 300-500 tokens per entry maximum
   - Link to plans instead of including details
   - Archive old sessions

5. **Monitor CLAUDE.md Size**
   - Keep under 800-1000 tokens
   - Link to detailed docs instead of including everything

---

### Issue 3: Agents Don't Follow Workflow

**Problem**: Agents implement code directly instead of creating plans

**Causes**:
- Workflow not clear in agent definition
- User instructions override agent protocol
- Agent lacks explicit "DO NOT implement" instruction

**Solutions**:

1. **In Agent Definition**, be extremely explicit:
   ```markdown
   ## What I DON'T Do

   ❌ I do NOT write implementation code
   ❌ I do NOT modify source files
   ❌ I do NOT execute the plan

   I ONLY create implementation plans. The parent agent or developer
   will execute the plan.
   ```

2. **In Allowed Tools**, restrict Write:
   ```markdown
   ## Allowed Tools

   - Read: ✅ Research existing code
   - Grep: ✅ Search patterns
   - Glob: ✅ Find files
   - Write: ✅ ONLY for creating plans in `.claude/plans/`
   - Edit: ❌ NOT allowed
   - Bash: ❌ NOT allowed
   ```

3. **In Mission**, emphasize output:
   ```markdown
   ## Mission Output

   My ONLY output is a markdown plan file at:
   `.claude/plans/{type}-{feature}-plan.md`

   I do NOT implement the plan. I do NOT modify source code.
   ```

---

### Issue 4: Lost Context Between Sessions

**Problem**: New agent invocations don't have context from previous work

**Causes**:
- Session context not provided to agents
- Session entries too vague
- Key decisions not documented

**Solutions**:

1. **Always Provide session_id**:
   ```
   Launch ui-architect with session_id="session_12345" to {task}
   ```

2. **Improve Session Entry Quality**:
   ```markdown
   ❌ BAD:
   **Status**: Done

   ✅ GOOD:
   **Status**: ✅ Completed

   **Key Decisions**:
   - Chose multi-step form over single page (better UX for complex input)
   - Used React Hook Form (already in tech stack)
   - Text maps organized by form section (client, job, review)

   **Files Created**:
   - features/work-orders/components/WorkOrderForm.tsx
   - shared/constants/text/work-order-form.text-map.ts
   ```

3. **Link Plans in Session**:
   ```markdown
   **Plan Location**: .claude/plans/ui-work-order-form-plan.md
   ```

4. **In CLAUDE.md**, instruct agents to check for session:
   ```markdown
   If `session_id` is provided:
   1. Read `.claude/tasks/context_session_{id}.md` FIRST
   2. Understand previous decisions and progress
   3. Continue from where previous agent left off
   4. Append your entry at the end
   ```

---

### Issue 5: Inconsistent Architecture

**Problem**: Code doesn't follow architectural patterns consistently

**Causes**:
- Architecture docs too vague
- Examples missing
- Dependency rules not enforced
- Critical constraints incomplete

**Solutions**:

1. **Add Visual Dependency Diagram** to architecture-patterns.md:
   ```markdown
   ## Dependency Rule

   ```
   core/              ← NEVER imports from outer layers
     ↑
   infrastructure/    ← CAN import core/, NEVER features/
     ↑
   features/          ← CAN import core/ + infrastructure/
   ```

   ❌ FORBIDDEN:
   - core/ importing from infrastructure/
   - core/ importing from features/
   - infrastructure/ importing from features/
   ```

2. **Add Before/After Examples**:
   ```markdown
   ### ❌ WRONG: Direct Database Access in Component

   ```typescript
   // features/WorkOrderList.tsx
   import { supabase } from '@/lib/supabase'; // ❌

   const { data } = await supabase
     .from('work_orders')
     .select('*'); // ❌
   ```

   ### ✅ CORRECT: Repository Pattern via Hook

   ```typescript
   // features/WorkOrderList.tsx
   import { useWorkOrders } from './hooks/use-work-orders'; // ✅

   const { data } = useWorkOrders(); // ✅
   ```
   ```

3. **Add to Critical Constraints**:
   ```markdown
   ## 3. Repository Pattern Required

   ❌ NEVER: Direct database/API calls in components or features
   ✅ ALWAYS: Use repository pattern via custom hooks

   **Correct Flow**:
   Component → Hook → Repository Interface (core/) → Implementation (infrastructure/)
   ```

---

### Issue 6: Plans Too Generic

**Problem**: Agent plans lack detail needed for implementation

**Causes**:
- Agent instructions too vague
- Output format not specified
- Examples missing

**Solutions**:

1. **Define Required Plan Sections** in agent definition:
   ```markdown
   ## Output Format

   Plans MUST include these sections:

   ### 1. Overview (REQUIRED)
   2-3 sentences describing the feature

   ### 2. Components/Entities (REQUIRED)
   Complete list with:
   - Purpose
   - Props/Fields (TypeScript interfaces)
   - Dependencies

   ### 3. Files (REQUIRED)

   #### New Files
   - Exact file path
   - Brief description

   #### Modified Files
   - Exact file path
   - What changes

   ### 4. Implementation Steps (REQUIRED)
   Numbered, sequential steps

   ### 5. Integration Notes (REQUIRED)
   How this integrates with existing code

   [Other sections specific to agent type...]
   ```

2. **Provide Plan Example** in agent definition

3. **In Mission**, emphasize research:
   ```markdown
   ### 2. Research Codebase (THOROUGH)

   Before designing, research:
   - Existing similar components (Grep patterns)
   - Current conventions (read 2-3 examples)
   - Available utilities and hooks
   - Text map structure

   Base your plan on actual codebase patterns, not generic templates.
   ```

---

## Resources

### Official Documentation

- **Claude Code Docs**: https://docs.claude.com/claude-code
- **MCP Documentation**: https://modelcontextprotocol.io
- **Anthropic Prompt Engineering**: https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering

### This Methodology

- **CLAUDE.md**: Entry point for AI agents
- **.claude/knowledge/**: Token-optimized documentation
- **.claude/agents/**: Specialized agent definitions
- **.claude/tasks/**: Session context files

### Recommended Reading

- **Clean Architecture** by Robert C. Martin (if using Clean Architecture)
- **Domain-Driven Design** by Eric Evans (for complex domains)
- **Anthropic's Context Engineering Guide**: Token optimization strategies

### Community

- Share your experience with this methodology
- Contribute improvements via pull requests
- Report issues or suggest enhancements

---

## Next Steps

1. **Start with Step 0**: Define your project with Claude Code
2. **Follow Steps 1-13**: Set up the complete methodology
3. **Create Your First Agent**: Start with one specialized agent
4. **Build a Feature**: Use the workflow to implement something real
5. **Iterate**: Refine based on your experience

Remember: This is a **methodology**, not a rigid framework. Adapt it to your project's needs while keeping the core principles:

- ✅ Token efficiency through lazy loading
- ✅ Specialized agents with clear responsibilities
- ✅ Two-phase workflow (planning → execution)
- ✅ Append-only session contexts
- ✅ Critical constraints enforced
- ✅ Architecture patterns documented

---

## Author

<div align="center">

**Cristian Camilo Peña**
Software Engineer

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue?style=flat&logo=linkedin)](https://www.linkedin.com/in/cristiancamilopena/)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-181717?style=flat&logo=github)](https://github.com/cristianps1988)

</div>

I built this methodology while working on a complex project where simple AI prompts weren't cutting it. After lots of trial and error, I found approaches that worked well for maintaining consistency and quality in AI-assisted development.

This is my attempt to document what worked for me. It's not perfect, and I'm sure there are better ways to do some things. I'm sharing it in case others find it useful for their own projects.

**Feel free to reach out:**
- Connect on [LinkedIn](https://www.linkedin.com/in/cristiancamilopena/)
- Follow on [GitHub](https://github.com/cristianps1988)

I'd love to hear about your experiences - what worked, what didn't, and how you adapted it for your needs.

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

You are free to:
- ✅ Use this methodology in personal or commercial projects
- ✅ Modify and adapt to your needs
- ✅ Share and distribute
- ✅ Create derivative works

---

## Contributing

Contributions are welcome! If you have improvements, suggestions, or found issues:

1. Open an issue to discuss proposed changes
2. Fork the repository
3. Create your feature branch
4. Submit a pull request

---

## Acknowledgments

This methodology was developed through practical experience building real-world applications with Claude Code and AI-assisted development.

Special thanks to:
- Anthropic for Claude Code and the Claude AI platform
- The prompt engineering and context optimization principles from Anthropic's guides
- The developer community for sharing AI-assisted development practices

---

**Happy building with AI! 🚀**