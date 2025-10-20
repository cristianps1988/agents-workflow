# Session Context Protocol

**Append-only logs for persistent context across agent invocations.**

---

## Instructions for Your Project

**This document explains the session protocol.** The examples use specific technologies (React, TypeScript, shadcn/ui, Supabase) for illustration, but the protocol works with **any tech stack**.

**What to customize**:
- ✅ Replace example file paths with your project structure
- ✅ Replace example component/feature names with yours
- ✅ Replace example technologies with your stack
- ✅ Keep the core protocol (append-only, token budgets, status values)

**Don't change**:
- ❌ The append-only principle
- ❌ Entry format structure
- ❌ Status values (🟢🟡✅⚠️❌)
- ❌ Token budget guidelines

---

## What Are Sessions?

Sessions are **append-only log files** that maintain context across multiple agent invocations and development sessions.

**Purpose**:
- Track agent actions and decisions
- Maintain implementation progress
- Document blockers and errors
- Preserve rationale for key decisions
- Enable agents to continue where others left off

**Why Append-Only?**

1. **KV-Cache Optimization**: Stable prefix = faster responses, lower costs
   - Claude's KV-cache reuses computation for unchanged content
   - Appending (vs overwriting) keeps the prefix stable
   - Results in faster agent responses and lower token costs

2. **Audit Trail**: Complete history of work
   - See what was tried, what worked, what didn't
   - Understand decision timeline
   - Learn from past attempts

3. **Error Learning**: Failed attempts inform future tries
   - Document what didn't work and why
   - Prevent repeating mistakes
   - Build institutional knowledge

4. **Context Reuse**: Agents continue from last state
   - No need to re-explain decisions
   - Agents understand previous work
   - Seamless handoffs between agents

---

## When to Create Sessions

### Create a Session For:

✅ **New Feature Development**
- Requires multiple steps (planning + implementation)
- Involves multiple agents (UI + database + domain)
- Takes more than one work session

✅ **Complex Refactoring**
- Multi-file changes
- Architectural migrations
- Pattern replacements across codebase

✅ **Multi-Step Tasks**
- Research + design + implementation
- Tasks spanning multiple days
- Tasks requiring review/feedback cycles

---

### Don't Create Sessions For:

❌ **Trivial Changes**
- Typo fixes
- Simple formatting
- One-line changes

❌ **One-Off Tasks**
- Single-file edits
- Documentation updates
- Configuration tweaks

❌ **Exploratory Work**
- Initial codebase exploration
- Research without implementation
- Proof-of-concept experiments (unless planning to productionize)

---

## Creating a Session

### 1. Generate Session ID

```bash
# Generate unique session ID with timestamp
session_id="session_$(date +%s)"

# Example output: session_1729012345
```

### 2. Copy Template

```bash
# Copy the template
cp .claude/tasks/context_session_template.md \
   .claude/tasks/context_session_${session_id}.md

# Example: creates context_session_1729012345.md
```

### 3. Fill Initial Information

Edit the new file:

```markdown
# Session Context: User Profile Feature

**Session ID**: session_1729012345
**Created**: 2025-10-19
**Status**: 🟢 Active

**Objective**: Implement user profile page with view and edit modes

**Related Files**:
- features/user-profile/pages/ProfilePage.tsx (new)
- features/user-profile/components/* (new)
```

### 4. Provide Session ID to Agents

When launching agents, include the session ID:

```
Launch ui-architect with session_id="session_1729012345"
to design user profile page
```

Agents will:
- Read the session context file
- Understand previous decisions
- Append their work to the session
- Continue where previous agent left off

---

## Entry Format

Each agent or developer appends a new entry using this format:

```markdown
---
## [YYYY-MM-DD HH:MM] {agent-name}: {Action Title}

**Task**: {One-line description}

**Status**: {Status emoji + text}

**Plan Location**: {Path to plan if created}

**Key Decisions**:
- {Decision 1 with brief rationale}
- {Decision 2 with brief rationale}
- {Decision 3 with brief rationale}

**Files Created/Modified**:
- {file path}: {what changed}

**Next Steps**:
- {Step 1}
- {Step 2}

**Blockers** (if any):
- {Blocker description}

---
```

---

## Status Values

| Status | Emoji | Meaning | When to Use |
|--------|-------|---------|-------------|
| **Active** | 🟢 | Currently working | Task in progress right now |
| **Paused** | 🟡 | Temporarily stopped | Waiting for something, context switch |
| **Completed** | ✅ | Work finished | Task done successfully |
| **Blocked** | ⚠️ | Cannot proceed | Dependency, decision needed, external blocker |
| **Failed** | ❌ | Encountered error | Unrecoverable error, needs intervention |

---

## Token Budget

**Target**: ~300-500 tokens per entry

### Include:

✅ **Task Summary** (1-2 sentences)
- What was done in this entry

✅ **Key Decisions** (3-5 bullets)
- Important choices made
- Brief rationale for each

✅ **Status and Next Steps**
- Current state
- What should happen next

✅ **Plan Location** (if applicable)
- Link to implementation plan
- Don't duplicate plan content

✅ **Important File Paths**
- Files created or modified
- Brief description of changes

---

### Exclude:

❌ **Full Code Blocks**
- Link to files instead
- Use file paths, not code

❌ **Detailed Implementation Steps**
- Put in plans, not session context
- Keep session high-level

❌ **Debug Logs**
- Summarize the issue
- Include only critical error info

❌ **Exploration Notes**
- Only final decisions
- Not the research process

---

## Best Practices

### 1. Never Overwrite

**❌ WRONG**:
```markdown
# Edit existing entry
## [2025-10-19 10:00] ui-architect: Design Profile Page
Status: ✅ Completed  # ← Editing this breaks KV-cache
```

**✅ CORRECT**:
```markdown
# Original entry stays unchanged
## [2025-10-19 10:00] ui-architect: Design Profile Page
Status: 🟢 Active

# New entry appended
---
## [2025-10-19 14:00] parent-agent: Profile Design Complete
Status: ✅ Completed (continuing from ui-architect work)
```

---

### 2. Concise Summaries

**❌ TOO VERBOSE** (~1000 tokens):
```markdown
**Key Decisions**:
- We decided to use a multi-step form because it provides better UX
  for complex data entry. The alternative was a single long form, but
  user testing showed that users find long forms overwhelming. We
  considered accordion-style forms, but they don't work well on mobile.
  After reviewing 5 different patterns and consulting with the design
  team, we chose a wizard-style multi-step form with progress indicator.
  The first step collects basic info, second step collects detailed info,
  and third step is review. Each step validates before proceeding...
  [continues for many more lines]
```

**✅ CONCISE** (~200 tokens):
```markdown
**Key Decisions**:
- Multi-step form (3 steps: basic → details → review) for better UX
- React Hook Form + Zod for validation (already in tech stack)
- Progress indicator at top (improves completion rates)
- Text maps by form section (client, job, review)
```

---

### 3. Link to Plans

**❌ DON'T DUPLICATE**:
```markdown
**Implementation Steps**:
1. Create text map at shared/constants/text/profile.text-map.ts
   with the following structure:
   export const profileTextMap = {
     buttons: {
       edit: "Edit Profile",
       save: "Save Changes"
     }
   }
2. Create ProfileView component with props:
   interface ProfileViewProps {
     user: User;
     onEdit: () => void;
   }
   [20 more detailed steps...]
```

**✅ LINK INSTEAD**:
```markdown
**Plan Location**: .claude/plans/ui-user-profile-plan.md

**Next Steps**:
- Implement ProfileView component (see plan section 2.1)
- Implement ProfileEdit component (see plan section 2.2)
- Create text map (see plan section 5)
```

---

### 4. Update Status Changes

When a task moves from Active → Completed, append a new entry:

```markdown
---
## [2025-10-19 16:00] parent-agent: Profile Feature Complete

**Task**: Finalize user profile implementation

**Status**: ✅ Completed

**Summary**: All components implemented per ui-architect plan

**Completed**:
- ProfileView component with display mode
- ProfileEdit component with form validation
- Text map created and registered
- Integration tests passing

**Files Modified**:
- features/user-profile/components/ProfileView.tsx (new)
- features/user-profile/components/ProfileEdit.tsx (new)
- shared/constants/text/user-profile.text-map.ts (new)
- shared/lib/get-text-map.ts (registered new map)

---
```

---

### 5. Document Blockers

If blocked, be specific about what's needed:

**❌ VAGUE**:
```markdown
**Status**: ⚠️ Blocked

**Blockers**:
- Can't continue
```

**✅ SPECIFIC**:
```markdown
**Status**: ⚠️ Blocked

**Blockers**:
- Waiting for user repository implementation (dependency)
- Need clarification on admin vs worker permissions for profile editing
- Database migration #015 must run before this can be tested

**Next Steps After Unblocking**:
- Continue with ProfileEdit component
- Add permission checks based on clarification
```

---

## Example Session

**Note**: This example uses React, TypeScript, shadcn/ui, and Supabase. **Adapt to your stack:**
- Replace `features/work-orders/` with your directory structure
- Replace `WorkOrderForm`, `ClientSelector`, etc. with your component names
- Replace `React Hook Form`, `shadcn/ui` with your libraries
- Replace `supabase/migrations/` with your database migration path

Full example showing how a session evolves:

```markdown
# Session Context: Work Order Creation Feature

**Session ID**: session_1729012345
**Created**: 2025-10-19
**Status**: ✅ Completed

**Objective**: Implement work order creation form for admins

---

## [2025-10-19 09:00] Initial Session Setup

**Task**: Session created for work order creation feature

**Status**: 🟢 Active

**Next Steps**:
- Launch ui-architect to design form
- Launch database-engineer to design schema (parallel)

---

## [2025-10-19 10:00] ui-architect: Design Work Order Form

**Task**: Create UI architecture plan for work order creation

**Status**: ✅ Plan Ready

**Plan Location**: .claude/plans/ui-work-order-form-plan.md

**Key Decisions**:
- Multi-step form (client selection → job details → review)
- React Hook Form for form state management
- shadcn/ui Form components for UI
- Text externalized to work-order-form.text-map.ts

**Components Designed**:
- WorkOrderForm (main component)
- ClientSelector (step 1)
- JobDetailsForm (step 2)
- ReviewStep (step 3)

**Next Steps**:
- Wait for database schema approval
- Implement components per plan
- Create text map

---

## [2025-10-19 10:30] database-engineer: Design Work Order Schema

**Task**: Create database schema for work orders

**Status**: ✅ Plan Ready

**Plan Location**: .claude/plans/db-work-order-schema-plan.md

**Key Decisions**:
- Table: work_orders with UUID primary key
- Status enum: draft, received, assigned, in_progress, completed, cancelled
- RLS policies for admin (full access) and worker (limited read)
- Audit fields: created_at, updated_at, created_by

**Next Steps**:
- Review schema with team
- Create migration file
- Run migration
- Generate TypeScript types

---

## [2025-10-19 14:00] parent-agent: Database Migration Executed

**Task**: Created and executed work order schema migration

**Status**: ✅ Completed

**Files Created**:
- supabase/migrations/20251019_create_work_orders.sql

**Next Steps**:
- Generate TypeScript types from database
- Begin UI implementation

---

## [2025-10-19 15:00] parent-agent: UI Implementation Started

**Task**: Implementing work order form components

**Status**: 🟢 Active (2/4 components done)

**Progress**:
- ✅ Created text map (work-order-form.text-map.ts)
- ✅ Created ClientSelector component
- ✅ Created JobDetailsForm component
- ⏳ Creating ReviewStep component (in progress)
- ⏳ Creating WorkOrderForm (pending)

---

## [2025-10-19 17:00] parent-agent: Work Order Form Complete

**Task**: Finalize work order creation feature

**Status**: ✅ Completed

**Completed**:
- All components implemented per plan
- Text map created and integrated
- Form validation working
- Integration with repository pattern verified
- Tests passing (18/18)

**Files Created/Modified**:
- features/work-orders/components/WorkOrderForm.tsx (new)
- features/work-orders/components/ClientSelector.tsx (new)
- features/work-orders/components/JobDetailsForm.tsx (new)
- features/work-orders/components/ReviewStep.tsx (new)
- shared/constants/text/work-order-form.text-map.ts (new)
- shared/lib/get-text-map.ts (registered new text map)

**Session Status**: ✅ Completed - Ready to archive

---
```

---

## Archiving Sessions

After a session is complete and some time has passed (e.g., 30 days), archive it to prevent "context rot".

### Create Archive Directory

```bash
mkdir -p .claude/tasks/archive
```

### Move Completed Sessions

```bash
# Move old completed sessions
mv .claude/tasks/context_session_1729012345.md \
   .claude/tasks/archive/
```

### When to Archive

- Session status is ✅ Completed
- At least 30 days have passed
- Feature is stable (no recent changes)
- No active references from other sessions

**Don't archive**:
- 🟢 Active sessions
- ⚠️ Blocked sessions (might resume)
- Recent completions (< 30 days)

---

## Troubleshooting

### Problem: Session File Getting Too Large

**Symptoms**: Session file > 5000 lines, agents take long time to load

**Solutions**:
1. **Keep entries concise**: Enforce 300-500 token limit
2. **Link to plans**: Don't duplicate plan content
3. **Summarize errors**: Don't paste full stack traces
4. **Archive if appropriate**: Move to archive if truly complete

---

### Problem: Agents Not Reading Session Context

**Symptoms**: Agents don't seem aware of previous decisions

**Solutions**:
1. **Verify session_id provided**: Must pass session_id when launching agent
2. **Check CLAUDE.md**: Should instruct agents to read session context
3. **Check agent definition**: Should have step to read session context
4. **Verify file path**: Make sure file exists at .claude/tasks/context_session_{id}.md

---

### Problem: KV-Cache Not Optimizing

**Symptoms**: Slow responses even with stable context

**Solutions**:
1. **Verify append-only**: Check no one is editing/overwriting entries
2. **Check prefix stability**: First ~70% of file should be unchanged between invocations
3. **Keep entries at end**: Always append new entries at the very end
4. **Avoid reformatting**: Don't change whitespace or formatting of old entries

---

## Summary

**Golden Rules**:
1. ✅ **Create sessions** for multi-step, non-trivial work
2. ✅ **Append only** - never overwrite existing entries
3. ✅ **Keep entries concise** - ~300-500 tokens each
4. ✅ **Link to plans** - don't duplicate plan content
5. ✅ **Provide session_id** when launching agents
6. ✅ **Archive when done** - prevent context rot

**Benefits**:
- Faster agent responses (KV-cache optimization)
- Better context continuity (agents know previous work)
- Audit trail (understand decision timeline)
- Error learning (failed attempts documented)

---

**Protocol Version**: 1.0
**Last Updated**: 2025-10-19