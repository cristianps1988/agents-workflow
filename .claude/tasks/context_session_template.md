# Session Context: {Feature Name}

**Session ID**: {session_id}
**Created**: {YYYY-MM-DD}
**Status**: 🟢 Active | 🟡 Paused | ✅ Completed | ⚠️ Blocked | ❌ Failed

**Objective**: {High-level goal of this session - 1-2 sentences}

**Related Files**:
- {Key files that will be created/modified}

---

## Instructions for Agents

**This file is APPEND-ONLY**. When adding new entries:

1. **NEVER overwrite** existing content
2. **ALWAYS append** new entries at the end (below this section)
3. **Use the entry template** provided below
4. **Keep entries concise**: ~300-500 tokens maximum
5. **Link to plans**: Don't duplicate plan content, reference file path

**Why append-only?**
- Preserves KV-cache optimization (stable prefix)
- Maintains audit trail
- Enables error learning

---

## Entry Template

When appending a new entry, use this format:

```markdown
---
## [YYYY-MM-DD HH:MM] {agent-name or your-name}: {Action Title}

**Task**: {One-line description of what was done}

**Status**: 🟢 Active | 🟡 Paused | ✅ Completed | ⚠️ Blocked | ❌ Failed

**Plan Location** (if applicable): {path to .claude/plans/plan-file.md}

**Key Decisions**:
- {Decision 1 with brief rationale}
- {Decision 2 with brief rationale}
- {Decision 3 with brief rationale}

**Files Created/Modified** (if applicable):
- {file path 1}: {what changed}
- {file path 2}: {what changed}

**Next Steps**:
- {Step 1}
- {Step 2}

**Blockers** (if any):
- {Blocker description}

---
```

---

## Status Legend

- 🟢 **Active**: Currently working on this
- 🟡 **Paused**: Temporarily stopped (waiting, context switch)
- ✅ **Completed**: Work finished successfully
- ⚠️ **Blocked**: Cannot proceed (dependency, decision needed, error)
- ❌ **Failed**: Encountered unrecoverable error

---

## Token Budget Guidelines

**Keep each entry concise** (~300-500 tokens):

**Include**:
- ✅ Task summary (1-2 sentences)
- ✅ Key decisions (3-5 bullets)
- ✅ Status and next steps
- ✅ Plan location (link, don't duplicate)
- ✅ Important file paths

**Exclude**:
- ❌ Full code blocks (link to files)
- ❌ Detailed implementation steps (put in plans)
- ❌ Debug logs (summarize the issue)
- ❌ Exploration notes (only final decisions)

---

<!-- ========================================= -->
<!-- APPEND NEW ENTRIES BELOW THIS LINE       -->
<!-- DO NOT MODIFY ANYTHING ABOVE THIS LINE   -->
<!-- ========================================= -->

## [YYYY-MM-DD HH:MM] {agent-name}: Initial Session Setup

**Task**: Session created for {feature name}

**Status**: 🟢 Active

**Objective**: {Restate the session objective}

**Next Steps**:
- Launch appropriate agent (ui-architect, database-engineer, etc.)
- Research existing patterns
- Create implementation plan

---

<!-- Future entries will be appended below -->