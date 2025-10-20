# Business Rules

**Essential business logic and domain rules for AI agents.**

**Purpose**: Document domain-specific rules, validations, user roles, and entity behaviors that must be enforced across all layers (database, domain, UI).

---

## How to Use This Document

This is a **compressed, token-optimized version** extracted from your full PROJECT.md/PRD.md.

**What to include**:
1. **User Roles and Permissions**: Who can do what
2. **Entity States and Transitions**: Valid states and allowed transitions
3. **Validation Rules**: Required fields, formats, constraints
4. **Data Visibility Rules**: What each role can see
5. **Business Constraints**: Unique rules specific to your domain

**Token Size**: Typically 500-2000 tokens (optimized)

**Full Context**: Refer to `PROJECT.md` or `PRD.md` for complete details

**Agent Strategy**: Agents use Grep to load specific sections as needed

---

## Template Structure

Below is a **template with examples**. Replace all examples with your actual business rules.

---

## User Roles

### [Role Name 1] (e.g., Admin)
**Can**:
- [Action 1] (e.g., Create and manage users)
- [Action 2] (e.g., Access financial reports)
- [Action 3]

**Cannot**:
- [Restriction 1] (e.g., Delete user accounts without approval)

---

### [Role Name 2] (e.g., Standard User)
**Can**:
- [Action 1] (e.g., View own data)
- [Action 2] (e.g., Create items)

**Cannot**:
- [Restriction 1] (e.g., Access other users' data)
- [Restriction 2] (e.g., Modify system settings)

---

## [Entity Name] Status Transitions

Example: Order/Task/Document Status Flow

```
draft → submitted → approved → completed
  ↓         ↓           ↓
cancelled cancelled cancelled
```

### State Definitions

| Status | Meaning | Who Can Set | Rules |
|--------|---------|-------------|-------|
| **draft** | Initial state | Creator | Editable |
| **submitted** | Ready for review | User | Read-only for user |
| **approved** | Approved by authority | Admin | Cannot revert |
| **completed** | Finished | User or Admin | Final state |
| **cancelled** | Cancelled | Admin | From any state |

### Transition Rules

**draft → submitted**:
- All required fields must be filled
- Creator only

**submitted → approved**:
- Admin only
- Must meet approval criteria

**approved → completed**:
- User or Admin
- Optional completion notes

**any → cancelled**:
- Admin only
- Must provide reason

---

## Validation Rules

### [Entity Name] Validation

**Required Fields**:
- field1 (type, constraint) - e.g., title (string, 10-200 chars)
- field2 (type, constraint) - e.g., amount (number, > 0)
- field3 (type, constraint)

**Optional Fields**:
- field4 (type, constraint, default)
- field5 (type, constraint)

**Constraints**:
- [Constraint 1] - e.g., endDate must be after startDate
- [Constraint 2] - e.g., amount + tax = total
- [Constraint 3]

---

## Data Visibility Rules

### [View Context 1] (e.g., Public View)

**Users see LIMITED data**:

Visible:
- field1
- field2 (truncated)
- field3

Hidden:
- sensitive_field1
- sensitive_field2
- sensitive_field3

---

### [View Context 2] (e.g., Owner View)

**Users see MORE data**:

Additional visible:
- field4
- field5

Still hidden:
- admin_field1
- financial_field1

---

### [Admin View]

**Admins see EVERYTHING**:
- All fields
- Audit logs
- System metadata

---

## Business Constraints

### Constraint 1: [Name]

**Rule**: [Description of the rule]

**Enforcement**:
- Database: [How enforced at DB level]
- Application: [How enforced in business logic]
- UI: [How enforced in UI]

**Example**: A user can only have ONE active subscription at a time

---

### Constraint 2: [Name]

**Rule**: [Description]

**Enforcement**:
- [Layer 1]: [How]
- [Layer 2]: [How]

**Example**: Total cannot exceed budget limit

---

## Domain-Specific Rules

### [Your Domain Rule 1]

**Rule**: [Specific rule for your domain]

**Enforcement**: [How it's enforced]

**Example**: Appointments must be scheduled at least 24 hours in advance

---

### [Your Domain Rule 2]

**Rule**: [Another specific rule]

**Enforcement**: [How it's enforced]

**Example**: Users can only edit items they created

---

## Instructions for Your Project

**To customize this document:**

1. **Start from PROJECT.md/PRD.md**: Extract essential rules only
2. **Replace all examples** with your actual business rules
3. **Keep it concise**: Target 500-2000 tokens (agents use Grep for sections)
4. **Include enough detail**: Agents should understand rules without reading full PRD
5. **Update regularly**: As business rules evolve

**What to include**:
- ✅ User roles and what they can/can't do
- ✅ Entity states and allowed transitions
- ✅ Validation rules (required fields, constraints)
- ✅ Data visibility rules by role
- ✅ Critical business constraints

**What NOT to include** (put in other files):
- ❌ UI/UX details (belongs in UI plans)
- ❌ Database schema details (belongs in database plans)
- ❌ Implementation details (belongs in architecture-patterns.md)
- ❌ Tech stack (belongs in tech-stack.md)

**Agents will Grep for sections**, so use clear headings:
- `## User Roles`
- `## [Entity] Status Transitions`
- `## Validation Rules`
- `## Data Visibility Rules`
- etc.

---

## Example Agent Usage

**When agents need specific info, they use Grep**:

```bash
# Database Agent needs user roles
Grep: pattern="## User Roles", path="business-rules.md", -A=20

# Domain Agent needs validation rules
Grep: pattern="## Validation Rules", path="business-rules.md", -A=30

# UI Agent needs visibility rules
Grep: pattern="## Data Visibility Rules", path="business-rules.md", -A=40
```

---

**Token Budget**: 500-2000 tokens. Full details in PROJECT.md/PRD.md.