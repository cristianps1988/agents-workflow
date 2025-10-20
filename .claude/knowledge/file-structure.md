# File Structure

**Naming conventions and file organization patterns.**

**Purpose**: Ensure consistent file naming, directory structure, and import patterns across the project.

---

## How to Use This Document

Define your project's conventions for:

1. **File Naming**: How to name different types of files
2. **Directory Structure**: Where files should be located
3. **Import Patterns**: How to import modules (absolute vs relative, ordering)
4. **Grouping Strategy**: Organization approach (by feature, by type, etc.)

**Token Size**: ~500 tokens

**Agents**: Read this when creating new files or organizing code

---

## Template & Example

Below is an example for a React + TypeScript project. **Customize for your stack.**

---

## File Naming Conventions

<!-- Define your file naming strategy below. Include all file types used in your project.

     Consider:
     - What naming convention fits your stack? (PascalCase, camelCase, kebab-case)
     - Where should each type of file live?
     - Are there any special suffixes or patterns? (.test, .spec, .types, .entity)

     Example structure (replace with your actual conventions):
-->

| Type | Convention | Example | Location |
|------|-----------|---------|----------|
| **Components** | YourConvention | `ExampleFileName.ext` | path/to/files/ |

---

## Directory Structure

### Option 1: Feature-Based (Recommended for most projects)

```
src/
├── features/                    # Feature-based modules
│   ├── auth/
│   │   ├── components/          # Auth-specific components
│   │   │   ├── LoginForm.tsx
│   │   │   └── RegisterForm.tsx
│   │   ├── hooks/               # Auth-specific hooks
│   │   │   ├── use-login.ts
│   │   │   └── use-register.ts
│   │   ├── pages/               # Auth pages
│   │   │   ├── LoginPage.tsx
│   │   │   └── RegisterPage.tsx
│   │   └── utils/               # Auth utilities
│   │       └── validate-email.ts
│   │
│   ├── user-profile/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── pages/
│   │   └── utils/
│   │
│   └── work-orders/
│       ├── components/
│       ├── hooks/
│       ├── pages/
│       └── utils/
│
├── shared/                      # Shared across features
│   ├── components/              # Reusable UI components
│   │   ├── Button.tsx
│   │   ├── Modal.tsx
│   │   └── DataTable.tsx
│   ├── hooks/                   # Reusable hooks
│   │   ├── use-disclosure.ts
│   │   └── use-local-storage.ts
│   ├── utils/                   # Shared utilities
│   │   ├── format-date.ts
│   │   └── format-currency.ts
│   ├── constants/               # Constants & configs
│   │   ├── routes.ts
│   │   └── text/                # Text maps
│   │       ├── common.text-map.ts
│   │       └── get-text-map.ts
│   └── types/                   # Shared TypeScript types
│       └── common.types.ts
│
├── core/                        # Domain layer (if using Clean Architecture)
│   ├── entities/                # Domain entities
│   │   ├── user.entity.ts
│   │   └── work-order.entity.ts
│   ├── repositories/            # Repository interfaces
│   │   ├── user-repository.interface.ts
│   │   └── work-order-repository.interface.ts
│   ├── services/                # Business logic services
│   │   ├── user-service.ts
│   │   └── work-order-service.ts
│   └── dtos/                    # Data Transfer Objects
│       ├── create-user.dto.ts
│       └── update-work-order.dto.ts
│
├── infrastructure/              # Infrastructure layer (if using Clean Architecture)
│   ├── api/                     # Repository implementations
│   │   ├── user-api.ts
│   │   └── work-order-api.ts
│   ├── providers/               # Dependency injection
│   │   └── repository-provider.tsx
│   └── supabase/                # Database client
│       └── client.ts
│
└── app/                         # Application setup
    ├── providers/               # React providers
    │   ├── query-provider.tsx
    │   └── theme-provider.tsx
    ├── router/                  # Routing configuration
    │   └── routes.tsx
    └── layout/                  # App layout
        └── AppLayout.tsx
```

---

### Option 2: Type-Based (Alternative)

```
src/
├── components/                  # All components
│   ├── auth/
│   ├── user-profile/
│   └── shared/
├── hooks/                       # All hooks
│   ├── auth/
│   ├── user-profile/
│   └── shared/
├── pages/                       # All pages
│   ├── auth/
│   └── user-profile/
├── services/                    # All services
├── utils/                       # All utilities
└── types/                       # All types
```

**Choose based on your project size and team preferences.**

---

## Import Patterns

### Import Path Strategy

**Best Practice: Absolute Imports with Path Aliases**

```typescript
// tsconfig.json or vite.config.ts - Configure aliases
{
  "compilerOptions": {
    "paths": {
      "@/*": ["./src/*"],
      "@/features/*": ["./src/features/*"],
      "@/shared/*": ["./src/shared/*"],
      "@/core/*": ["./src/core/*"]
    }
  }
}

// Example - replace with your actual imports
import { Entity } from '@/layer/module/entity.file';
import { Component } from '@/shared/components/Component';
import { useHook } from '@/features/module/hooks/use-hook';
```

**❌ AVOID: Relative Imports for Distant Files**

```typescript
// Hard to read and maintain
import { Module } from '../../../../path/to/module.file';
import { Component } from '../../../other/path/Component';
```

**✅ OK: Relative Imports for Nearby Files**

```typescript
// features/module/components/MainComponent.tsx
import { SubComponent } from './SubComponent';     // Same directory
import { useCustom } from '../hooks/use-custom';   // Parent directory
```

---

### Barrel Files (index.ts)

**Two Approaches - Choose One:**

#### Approach 1: ❌ NO Barrel Files (Recommended for better tree-shaking)

```typescript
// ❌ DON'T create index.ts files that re-export

// ✅ Direct imports only
import { EntityA } from '@/layer/entities/entity-a.file';
import { EntityB } from '@/layer/entities/entity-b.file';
```

**Why**: Better tree-shaking, faster builds, clearer dependencies

---

#### Approach 2: ✅ Limited Barrel Files (Only for specific purposes)

```typescript
// ✅ OK: Barrel file for public API of a package
// shared/components/index.ts
export { Button } from './Button';
export { Modal } from './Modal';
export { DataTable } from './DataTable';

// Usage
import { Button, Modal } from '@/shared/components';
```

**Use only if**:
- Package has clear public API
- Files are always used together
- Team agrees on this pattern

**Avoid for**: Entities, Services, Repositories (use direct imports)

---

### Import Ordering

<!-- Define your import ordering strategy here:

     Common approaches:
     1. By source (external → internal → relative)
     2. By type (components → hooks → utils → types → styles)
     3. Alphabetical

     Specify:
     - How many groups?
     - What goes in each group?
     - Any special rules for types/styles?
-->

**Tool Recommendation**: Use ESLint plugin `eslint-plugin-import` to enforce ordering automatically.

---

## Grouping Strategy

### By Feature (Recommended)

**Advantages**:
- Related code lives together
- Easy to understand feature scope
- Can delete entire feature folder
- Scales well with large teams

**Example**:
```
features/
├── auth/          # Everything auth-related
├── dashboard/     # Everything dashboard-related
└── settings/      # Everything settings-related
```

---

### By Type (Alternative)

**Advantages**:
- Easy to find all components/hooks/etc.
- Familiar to developers from other projects

**Example**:
```
components/
hooks/
pages/
services/
```

**Disadvantage**: Related code is scattered across directories

---

## Test Files

<!-- Define your test file organization strategy:

     Option A: Colocated (tests next to source files)
     Option B: Separate __tests__ directories
     Option C: Mirror structure in separate /tests folder

     Document:
     - Which approach you use
     - Test file naming pattern (.test.ext, .spec.ext, Test.ext)
     - Any exceptions (e.g., integration tests separate from unit tests)
-->

**Example structure** (customize for your project):

```
module/
├── source-file.ext
└── source-file.test.ext    ← Define your pattern
```

---

## Special File Types

<!-- Document any special file types specific to your project:

     Examples might include:
     - Constants/configuration files
     - Text/translation maps
     - Type definition files
     - Schema/validation files
     - Mock data files

     For each type, specify:
     - Location in directory structure
     - Naming convention
     - Basic structure/pattern
-->

### [Your Special File Type]

**Location**: `path/to/files/`

**Naming**: `naming-pattern.ext`

**Structure** (if applicable):
```typescript
// Example structure - replace with your pattern
export const config = {
  key: "value"
} as const;
```

---

### Configuration Files

**Root Level**:
```
project-root/
├── .env                    # Environment variables
├── .env.local             # Local overrides (gitignored)
├── tsconfig.json          # TypeScript config
├── vite.config.ts         # Build tool config
├── package.json           # Dependencies
└── .eslintrc.js           # Linter config
```

---

## Instructions for Your Project

**To customize this document:**

1. **Choose naming conventions** that match your stack
2. **Choose directory structure** (feature-based vs type-based)
3. **Define import strategy** (absolute vs relative, barrel files or not)
4. **Document special patterns** specific to your project
5. **Include examples** showing actual file paths from your project

**What to include**:
- ✅ File naming for each type (components, hooks, services, etc.)
- ✅ Directory structure with explanations
- ✅ Import patterns and ordering
- ✅ Test file location strategy
- ✅ Special file types (text maps, configs, etc.)

**What agents need to know**:
- Where to create new files
- How to name them
- How to import them
- How to organize related files

**Update this document** as conventions evolve.

---

**Token Budget**: ~500 tokens. Agents can read full or Grep specific sections.
