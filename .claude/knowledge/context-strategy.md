# Context Strategy

**How to load context efficiently - maximize signal, minimize tokens.**

**Purpose**: Teach AI agents the strategy for loading documentation to minimize token usage while maintaining high-quality outputs.

---

## Instructions for Customization

**This document contains generic examples and patterns.** As you create your own specialized agents and documentation structure, update the examples in this document to reflect:

1. **Your agent names**: Replace generic examples ("Agent Planning New Component/Feature") with your actual agent names (e.g., "UI Architect", "Database Engineer")
2. **Your documentation structure**: Update file paths and token counts to match your actual docs
3. **Your task types**: Customize the decision tree based on the types of tasks your agents handle

**The core principles remain the same** - this document teaches the strategy, you provide the specifics.

---

## Core Principle

> "Find the smallest set of high-signal tokens that maximize the likelihood of your desired outcome."
>
> — Anthropic's Prompt Engineering Guide

**Don't load everything upfront. Load what you need, when you need it.**

---

## Decision Tree

```
Start: New Agent Task
↓
ALWAYS READ: critical-constraints.md (~200 tokens)
↓
Session ID provided?
├─ YES: Read .claude/tasks/context_session_{id}.md (~300-500 tokens)
│   └─ TOTAL SO FAR: ~500-700 tokens ✅
└─ NO: Continue (TOTAL: ~200 tokens)
↓
What type of task?
├─ UI Task
│   ├─ READ: file-structure.md (~500 tokens) if creating new files
│   ├─ GREP: architecture-patterns.md for "## Presentation Layer" (~200 tokens)
│   └─ GREP: tech-stack.md for UI libraries (~100 tokens)
│   └─ TOTAL: ~500-1000 tokens ✅
│
├─ Database Task
│   ├─ GREP: business-rules.md for specific entity rules (~200 tokens)
│   ├─ GREP: architecture-patterns.md for "## Dependency Rule" (~150 tokens)
│   └─ READ: tech-stack.md for database info (~100 tokens)
│   └─ TOTAL: ~500-850 tokens ✅
│
├─ Domain/Service Task
│   ├─ READ: Specific entity file (~200 tokens)
│   ├─ GREP: architecture-patterns.md for "## Repository Pattern" (~300 tokens)
│   ├─ GREP: business-rules.md for validation rules (~200 tokens)
│   └─ TOTAL: ~700-1000 tokens ✅
│
└─ Refactoring Task
    ├─ READ: critical-constraints.md (~200 tokens)
    ├─ GREP: architecture-patterns.md for specific layer (~300 tokens)
    ├─ READ: Specific files being refactored (~500 tokens)
    └─ TOTAL: ~1000-1200 tokens ✅
```

**Optimal Budget**: 500-1200 tokens total context

**Avoid**: Loading all knowledge docs = ~7000+ tokens ❌

---

## Context Loading Strategies

### Strategy 1: Grep First (Most Common)

**When**: Looking for specific information in large documents

**How**: Use Grep with pattern, read matched sections only

**Example**:

```markdown
❌ BAD: Read full file
Read: file=".claude/knowledge/architecture-patterns.md"
Result: ~4000 tokens

✅ GOOD: Grep for specific section
Grep: pattern="## Repository Pattern",
      path=".claude/knowledge/architecture-patterns.md",
      output_mode="content",
      -A=30
Result: ~200 tokens (20x savings!)
```

**Common Grep Patterns**:
- `## Repository Pattern` - Get repository implementation guide
- `## User Roles` - Get role definitions from business-rules.md
- `## Dependency Rule` - Get layer dependency rules
- `## [Entity] Status Transitions` - Get state machine for entity

---

### Strategy 2: Targeted Read (When You Know Location)

**When**: You know the exact section location (line numbers)

**How**: Use Read with offset/limit

**Example**:

```markdown
❌ BAD: Read full file
Read: file=".claude/knowledge/business-rules.md"
Result: ~2000 tokens

✅ GOOD: Read specific lines
Read: file=".claude/knowledge/business-rules.md",
      offset=100,
      limit=50
Result: ~300 tokens
```

**When to use**: If you've read the file before and know where the section is.

---

### Strategy 3: Multiple Targeted Searches (Complex Tasks)

**When**: Need information from multiple places

**How**: Series of Grep commands for specific sections

**Example**:

```markdown
❌ BAD: Read everything
Read: architecture-patterns.md (4000 tokens)
Read: business-rules.md (2000 tokens)
Read: file-structure.md (500 tokens)
TOTAL: 6500 tokens

✅ GOOD: Grep specific sections
Grep: "## Repository Pattern" in architecture-patterns.md (~200 tokens)
Grep: "## Validation Rules" in business-rules.md (~200 tokens)
Read: file-structure.md (500 tokens)
TOTAL: 900 tokens (7x savings!)
```

---

### Strategy 4: Lazy Loading (Load on Demand)

**When**: Unclear what you'll need until you start working

**How**: Load minimal context first, then load more as needed

**Example Workflow**:

```markdown
Step 1: Start with minimal context
- Read: critical-constraints.md (200 tokens)
- Read: session context (300 tokens)
TOTAL: 500 tokens

Step 2: Research what exists (no loading yet)
- Grep: Find existing similar components (files_with_matches mode)
- Glob: Find relevant files

Step 3: Load only what's needed based on research
- Read: 2-3 existing similar files (~600 tokens)
- Grep: Specific pattern from architecture (~200 tokens)
TOTAL: 1300 tokens

Final: ~1300 tokens vs ~7000 if loaded everything upfront
```

---

## Token Budget Examples

### Example 1: Agent Planning New Component/Feature

**Optimal Path**:
```
1. Read critical-constraints.md             200 tokens
2. Read context_session_{id}.md             300 tokens
3. Grep file-structure.md for relevant      150 tokens
   naming conventions
4. Grep architecture-patterns.md for        200 tokens
   specific pattern needed
5. Read 2 similar existing files            400 tokens

TOTAL: 1250 tokens ✅
```

**Heavy Path (AVOID)**:
```
1. Read critical-constraints.md             200 tokens
2. Read architecture-patterns.md (full)    4000 tokens
3. Read business-rules.md (full)           2000 tokens
4. Read file-structure.md (full)            500 tokens
5. Read tech-stack.md (full)                300 tokens

TOTAL: 7000 tokens ❌ (5.6x more expensive!)
```

---

### Example 2: Agent Planning Data Layer Changes

**Optimal Path**:
```
1. Read critical-constraints.md             200 tokens
2. Read context_session_{id}.md             300 tokens
3. Grep business-rules.md for entity        250 tokens
   specific rules
4. Grep business-rules.md for validation    200 tokens
5. Read existing related files              400 tokens

TOTAL: 1350 tokens ✅
```

---

### Example 3: Agent Refactoring Code

**Optimal Path**:
```
1. Read critical-constraints.md             200 tokens
2. Read context_session_{id}.md             300 tokens
3. Grep architecture-patterns.md for        300 tokens
   relevant layer or pattern
4. Read files being refactored              600 tokens

TOTAL: 1400 tokens ✅
```

---

## Document Token Reference

**Always Load**:
| Document | Tokens | Strategy |
|----------|--------|----------|
| critical-constraints.md | ~200 | ALWAYS read in full |
| context_session_{id}.md | ~300-500 | Read if session_id provided |

**Lazy Load (Use Grep)**:
| Document | Full Size | Grep Section | When to Load |
|----------|-----------|--------------|--------------|
| architecture-patterns.md | ~4000 | ~200-300 | Need architecture rules for specific layer |
| business-rules.md | ~2000 | ~200-500 | Need rules for specific entity or validation |
| context-strategy.md | ~3500 | Rarely | Only if agent confused about context loading |
| file-structure.md | ~500 | ~100-200 | Need naming conventions or directory structure |
| tech-stack.md | ~300 | ~100 | Need commands or library versions |

**Project Docs**:
| Document | Full Size | Strategy |
|----------|-----------|----------|
| PROJECT.md / PRD.md | ~5000-10000 | Use business-rules.md instead (compressed version) |
| README.md | ~1000-3000 | Reference only if needed |

---

## Grep Pattern Examples

### Finding Architecture Information

```bash
# Get repository pattern implementation guide
Grep: pattern="## Repository Pattern",
      path=".claude/knowledge/architecture-patterns.md",
      -A=50,
      output_mode="content"

# Get dependency rules
Grep: pattern="## Dependency Rule",
      path=".claude/knowledge/architecture-patterns.md",
      -A=20,
      output_mode="content"

# Get state management strategy
Grep: pattern="## State Management",
      path=".claude/knowledge/architecture-patterns.md",
      -A=40,
      output_mode="content"
```

---

### Finding Business Rules

```bash
# Get user roles and permissions
Grep: pattern="## User Roles",
      path=".claude/knowledge/business-rules.md",
      -A=30,
      output_mode="content"

# Get entity status transitions
Grep: pattern="Status Transitions",
      path=".claude/knowledge/business-rules.md",
      -A=25,
      output_mode="content"

# Get validation rules
Grep: pattern="## Validation Rules",
      path=".claude/knowledge/business-rules.md",
      -A=40,
      output_mode="content"
```

---

### Finding File Conventions

```bash
# Get file naming conventions
Grep: pattern="## File Naming",
      path=".claude/knowledge/file-structure.md",
      -A=15,
      output_mode="content"

# Get import patterns
Grep: pattern="## Import Patterns",
      path=".claude/knowledge/file-structure.md",
      -A=20,
      output_mode="content"
```

---

## Context Optimization Checklist

Before loading any document, ask:

- [ ] **Do I really need this?** Is it required for the current task?
- [ ] **Can I Grep instead?** Do I need a specific section vs the whole file?
- [ ] **Have I loaded session context?** Essential for understanding decisions
- [ ] **Am I loading duplicates?** Is this info already in another loaded doc?
- [ ] **Can I infer this?** Is this something I should already know?

If answer is "yes" to any optimization opportunity, use that instead.

---

## When to Load Full Documents

**Rarely load full documents. But if you must:**

1. **critical-constraints.md** - Always load full (only ~200 tokens)
2. **file-structure.md** - Sometimes load full if creating many new files (~500 tokens)
3. **tech-stack.md** - Sometimes load full if need multiple commands (~300 tokens)

**Never load full**:
- architecture-patterns.md (~4000 tokens) - Use Grep
- business-rules.md (~2000 tokens) - Use Grep
- context-strategy.md (~3500 tokens) - Reference only
- PROJECT.md/PRD.md (~5000+ tokens) - Use business-rules.md instead

---

## Special Case: Research Tasks

**When researching codebase** (finding patterns, understanding structure):

**Don't load docs first!** Research first, then load only what's needed.

**Optimal Flow**:
```
1. Use Glob to find relevant files
   Example: pattern="**/*{Component}.tsx"

2. Use Grep to find patterns
   Example: pattern="export.*function.*Component"

3. Read 2-3 example files to understand patterns

4. THEN load docs if you need clarification
   Example: Grep architecture-patterns.md for specific pattern

5. Create plan based on actual codebase + docs
```

**Why**: Codebase shows what actually exists. Docs show what should exist. Combine both.

---

## Instructions for Your Project

**To use this strategy:**

1. **Agents should follow the decision tree** at the top of this document
2. **Default to Grep** for large documents
3. **Load session context** early if available
4. **Monitor token usage** - aim for <1500 tokens total context
5. **Research first, load later** for exploratory tasks

**Common Mistakes to Avoid**:
- ❌ Loading all docs upfront "just in case"
- ❌ Reading full files when Grep would work
- ❌ Not using session context (loses decisions)
- ❌ Loading PROJECT.md instead of business-rules.md
- ❌ Loading context-strategy.md (meta-document, rarely needed)

**Success Metrics**:
- ✅ Total context < 1500 tokens for most tasks
- ✅ Using Grep for 80%+ of large document access
- ✅ Session context loaded early and used
- ✅ High-quality outputs despite low token usage

---

## Advanced: Hybrid Retrieval

**Combine multiple strategies for complex tasks:**

```markdown
Example: Implementing new feature with database + domain + UI

Phase 1: Planning (Load Minimal)
- Read: critical-constraints.md (200 tokens)
- Read: session context (300 tokens)
- Grep: business-rules.md for feature rules (250 tokens)
TOTAL: 750 tokens

Phase 2: Database Design
- Grep: architecture-patterns.md for data layer (200 tokens)
- Read: existing migration files (400 tokens)
TOTAL: +600 = 1350 tokens

Phase 3: Domain Design
- Grep: architecture-patterns.md for repository pattern (300 tokens)
- Read: existing entity files (300 tokens)
TOTAL: +600 = 1950 tokens

Phase 4: UI Design
- Grep: file-structure.md for naming (150 tokens)
- Read: similar components (400 tokens)
TOTAL: +550 = 2500 tokens

Spread across 4 separate agent invocations = ~600 tokens per invocation ✅
Much better than loading 7000 tokens upfront!
```

---

## Summary

**Golden Rules**:
1. **Always load critical-constraints.md first** (~200 tokens)
2. **Load session context if available** (~300 tokens)
3. **Use Grep for large documents** (save 10-20x tokens)
4. **Research codebase before loading docs**
5. **Total budget: aim for <1500 tokens**

**Remember**: Token efficiency = faster responses + lower costs + better KV-cache hits

---

**Token Budget**: This document is ~3500 tokens. Agents should Grep for specific strategies, not read it in full.