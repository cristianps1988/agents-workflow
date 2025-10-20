# Project Context for AI Agents

**Entry point for all AI agents. Keep under 500 tokens.**

---

## Project Overview

<!-- Replace with your project description (2-3 sentences) -->

[Your application name] is a [brief description of what it does]. It serves [target users] by [main value proposition].

**Tech Stack**: [e.g., React + TypeScript + Supabase]

---

## 🔴 CRITICAL - READ FIRST

**BEFORE doing anything else**, you MUST read:

`.claude/knowledge/critical-constraints.md`

This document (~200 tokens) contains non-negotiable architectural rules. Violating these rules is unacceptable.

---

## Available Specialized Agents

**When working on features, you can delegate to these specialized agents:**

<!-- Add your agents here as you create them. Examples:
- **UI/Frontend** → `.claude/agents/ui-architect.md`
- **Database** → `.claude/agents/database-engineer.md`
- **Business Logic** → `.claude/agents/domain-architect.md`
- **Refactoring** → `.claude/agents/migration-agent.md`
- **Research/Audit** → `.claude/agents/ux-researcher.md`
-->

**Agents in this project:**
- [List your agents here as you create them in `.claude/agents/`]
- Format: **{Task Type}** → `.claude/agents/{agent-name}.md`

**How to use agents:**
- Read the agent file to understand its role and capabilities
- Use the Task tool to invoke: `Launch {agent-name} with session_id="{id}" to {task}`
- Agent creates plan in `.claude/plans/`, then you execute it

---

## Workflow Protocol

### For New Features (Automatic Orchestration)

**Parent Agent Process:**
1. **Create session file** automatically with unique session_id
2. **Analyze task** and determine which specialized agents are needed
3. **Invoke specialized agents** to create implementation plans
4. **Execute plans** step-by-step
5. **Update session context** after each phase (append-only)

**Session files**: `.claude/tasks/context_session_{id}.md` (append-only logs)

### For Trivial Changes

Implement directly (typos, simple edits) - no session needed.

---

## Session Context Protocol

**When session_id is provided:**

1. Read `.claude/tasks/context_session_{id}.md` FIRST
2. Understand previous decisions and progress
3. Continue from where previous work left off
4. **Append** your entry at the end (NEVER overwrite)

**Entry format**: See `.claude/tasks/README.md` for full protocol.

---

## Documentation Map

**Load strategically - don't read everything upfront!**

### Always Read First
- `.claude/knowledge/critical-constraints.md` (~200 tokens) - Non-negotiable rules

### Read If Session Exists
- `.claude/tasks/context_session_{id}.md` (~300-500 tokens) - Session history

### Load As Needed (Use Grep for sections)
- `.claude/knowledge/architecture-patterns.md` (~4000 tokens) - Architecture rules
- `.claude/knowledge/business-rules.md` (~2000 tokens) - Domain rules
- `.claude/knowledge/context-strategy.md` (~3500 tokens) - Context loading strategy
- `.claude/knowledge/file-structure.md` (~500 tokens) - Naming conventions
- `.claude/knowledge/tech-stack.md` (~300 tokens) - Technologies, commands

**Strategy**: Use Grep to search specific sections instead of reading full files.

**Example**:
```
❌ Read: architecture-patterns.md (4000 tokens)
✅ Grep: pattern="## Repository Pattern", path="architecture-patterns.md", -A=30 (200 tokens)
```

---

## Key Constraints (Summary)

**Full details in `.claude/knowledge/critical-constraints.md`**

- ✅ Use repository pattern for data access (no direct DB imports)
- ✅ Externalize all text to text maps (no hardcoded strings)
- ✅ Follow architecture dependency rules strictly
- ✅ Agents create plans, parent executes
- ✅ Session context is append-only (never overwrite)

---

## MCP Configuration

**Available MCP Servers**: Defined in `.mcp.json`

<!-- List your MCP servers with token counts -->
- Example: **shadcn** (~4.7k tokens), **playwright** (~14k tokens)

**Strategy**: Enable only what the current task needs in `.claude/settings.local.json`

---

## For Agents: Pre-Work Checklist

Before starting work:

- [ ] Read `.claude/knowledge/critical-constraints.md`?
- [ ] Read session context if `session_id` provided?
- [ ] Understand my role (check `.claude/agents/{my-name}.md` if specialized agent)?
- [ ] Know which MCP tools I have access to?
- [ ] Will append to session context (not overwrite)?
- [ ] Will create plan in `.claude/plans/` (not implement directly)?

If any ❌, STOP and review documentation.

---

**Token Budget Goal**: ~400-500 tokens for this file. All details are in `.claude/knowledge/` docs.
