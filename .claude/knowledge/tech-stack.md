# Tech Stack

**Technologies, versions, commands, and library usage.**

**Purpose**: Quick reference for AI agents to know what technologies are used, their versions, and common commands.

---

## How to Use This Document

Document your project's tech stack including:

1. **Core Technologies**: Framework, language, build tools
2. **Key Dependencies**: UI libraries, state management, testing, etc.
3. **Development Tools**: Package manager, linter, formatter
4. **Commands**: Common development commands
5. **Important Notes**: Version constraints, tool preferences

**Token Size**: ~300-500 tokens

**Agents**: Read this when they need to know library versions, commands, or tool usage

---

## Template Structure

Below is a **template with placeholders**. Replace all examples with your actual tech stack.

---

## Core Stack

- **Framework**: [e.g., React 18.3, Vue 3.4, Next.js 14, etc.]
- **Language**: [e.g., TypeScript 5.6, JavaScript ES2023, Python 3.11]
- **Build Tool**: [e.g., Vite 5.4, Webpack 5, esbuild, etc.]
- **Package Manager**: **[e.g., pnpm, npm, yarn, bun]**
- **Runtime Version**: [e.g., Node 20.x LTS, Python 3.11, etc.]

⚠️ **CRITICAL**: Document if a specific package manager is required (e.g., "ONLY use pnpm, never npm/yarn")

---

## Key Dependencies

### [Category 1] (e.g., UI & Styling)

- **[Library Name]**: [purpose] - [version]
- **[Library Name]**: [purpose] - [version]
- **[Library Name]**: [purpose] - [version]

**Usage Notes**:
- [Important usage note 1]
- [Important usage note 2]

**Example**:
```
### UI & Styling
- **Component Library**: shadcn/ui (headless components) - latest
- **Styling**: Tailwind CSS - 3.4.0
- **Icons**: lucide-react - 0.263.1

Usage Notes:
- Use Tailwind utilities for styling (no custom CSS unless necessary)
- Use component library for all UI primitives
```

---

### [Category 2] (e.g., State Management)

- **[State Type]**: [Library] - [version]
- **[State Type]**: [Library] - [version]

**Decision Matrix** (optional):
| State Type | Library | Use For |
|------------|---------|---------|
| [Type 1] | [Library 1] | [Purpose] |
| [Type 2] | [Library 2] | [Purpose] |

**Example**:
```
### State Management
- **Server State**: TanStack React Query v5
- **Client State**: Zustand 4.4.0

Decision Matrix:
| State Type | Library | Use For |
|------------|---------|---------|
| Server state | React Query | API data, caching |
| Client state | Zustand | UI state, preferences |
| Local state | useState | Component-only state |
```

---

### [Category 3] (e.g., Data & Backend)

- **[Component]**: [Technology/Service]
- **[Component]**: [Technology/Service]

**Usage Notes**:
- [Critical usage rule]
- [Important pattern to follow]

**Example**:
```
### Data & Backend
- **Database**: PostgreSQL 16
- **ORM**: Prisma 5.x
- **Auth**: Auth0

Usage Notes:
- Always use repository pattern for database access
- Never import database client directly in components
```

---

## Development Tools

### Package Manager

**Package Manager**: **[your-choice]**

⚠️ **CRITICAL**: [Specify if you have strict requirements]

```bash
# ✅ CORRECT
[your-pm] install
[your-pm] add package-name

# ❌ WRONG (if applicable)
[other-pm] install
```

**Example**:
```
Package Manager: pnpm ONLY

⚠️ CRITICAL: Never use npm, yarn, or bun. Always use pnpm.
```

---

### Code Quality

- **Linter**: [e.g., ESLint 8.x, Biome, etc.]
- **Formatter**: [e.g., Prettier 3.x, Biome 1.x]
- **Git Hooks**: [e.g., Husky + lint-staged]

**Formatting Rules**:
- [Rule 1] - e.g., Tabs vs Spaces
- [Rule 2] - e.g., Single vs Double quotes
- [Rule 3] - e.g., Semicolons yes/no
- [Rule 4] - e.g., Line width

---

### [Language] Configuration

**Important Settings**:
```[language-config-format]
{
  "[setting1]": [value],
  "[setting2]": [value],
  "[critical-setting]": [value]
}
```

⚠️ **Critical Rules**:
- [e.g., No `any` types in TypeScript]
- [e.g., Strict mode enabled]

---

## Commands

### Installation

```bash
# Install dependencies
[your-pm] install

# Add dependency
[your-pm] add package-name

# Add dev dependency
[your-pm] add -D package-name

# Remove dependency
[your-pm] remove package-name
```

---

### Development

```bash
# Start dev server
[your-pm] dev

# Build for production
[your-pm] build

# Preview production build
[your-pm] preview
```

---

### Testing

```bash
# Run all tests
[your-pm] test

# Run tests in watch mode
[your-pm] test:watch

# Run tests with coverage
[your-pm] test:coverage
```

---

### Code Quality

```bash
# Format code
[your-pm] format

# Lint code
[your-pm] lint

# Fix lint errors
[your-pm] lint:fix

# Type check (if applicable)
[your-pm] type-check
```

---

### [Custom Category] (Optional)

```bash
# [Custom command 1]
[your-pm] [command]

# [Custom command 2]
[your-pm] [command]
```

**Example for Database commands**:
```bash
# Generate database migration
pnpm db:migrate

# Run database seeds
pnpm db:seed

# Reset database
pnpm db:reset
```

---

## Library-Specific Patterns (Optional)

### [Library Name]

**Setup**:
```[language]
// Show basic setup/configuration
```

**Usage**:
```[language]
// Show common usage pattern
```

**Example**:
```
### React Query

Setup:
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';

const queryClient = new QueryClient();

Usage:
const { data } = useQuery({
  queryKey: ['users'],
  queryFn: fetchUsers
});
```

---

## Important Notes

### Version Constraints

- **[Runtime]**: [Version requirement and reason]
- **[Framework]**: [Version requirement and reason]
- **[Critical Dependency]**: [Version requirement and reason]

**Example**:
```
- Node.js: Use 20.x LTS (compatibility with all dependencies)
- React: v18+ required for concurrent features
- TypeScript: 5.x for latest features
```

---

### Tool Preferences

⚠️ **[Tool Category]**: **[Your Strict Choice]** (never [alternatives])

⚠️ **[Tool Category]**: **[Your Strict Choice]** (NOT [alternatives])

**Example**:
```
⚠️ Package Manager: ALWAYS use pnpm (never npm, yarn, bun)
⚠️ Component Library: shadcn/ui (NOT Material-UI, Chakra)
⚠️ State Management: React Query for server state ONLY (NO Redux)
```

---

### Environment Variables

**File**: `.env` or `.env.local`

**Required Variables**:
```bash
# [Category 1]
[VAR_NAME_1]=your-value
[VAR_NAME_2]=your-value

# [Category 2]
[VAR_NAME_3]=your-value
```

**Access in Code**:
```[language]
// Show how to access env vars in your language/framework
```

**Example for Vite**:
```bash
VITE_API_URL=http://localhost:3000
VITE_API_KEY=your-api-key
```

Access:
```typescript
const apiUrl = import.meta.env.VITE_API_URL;
```

---

## Troubleshooting

### Common Issues

**Issue**: [Description of common problem]
**Solution**:
```bash
[commands to fix]
```

**Issue**: [Another common problem]
**Solution**: [How to resolve]

**Example**:
```
Issue: Package installation fails
Solution:
bash
rm -rf node_modules
rm package-lock.json
npm install


Issue: Type errors after dependency update
Solution: Regenerate types from database schema
```

---

## Instructions for Your Project

**To customize this document:**

1. **Replace ALL placeholders** with your actual tech stack
2. **List actual dependencies** with current versions
3. **Document commands** from your package.json scripts
4. **Add library-specific patterns** you use frequently
5. **Document environment variables** that are required
6. **Add troubleshooting** for setup issues you've encountered

**What to include**:
- ✅ Framework and language versions
- ✅ Key dependencies and their purpose
- ✅ Package manager preference (CRITICAL if you have one!)
- ✅ All development commands
- ✅ Code quality tools and their config
- ✅ Library usage patterns
- ✅ Important notes and constraints

**What agents need to know**:
- What package manager to use (CRITICAL)
- What libraries are available
- How to use key libraries correctly
- What commands to run for common tasks
- What patterns and conventions to follow

**Keep this document updated** when you add/remove dependencies or change tools.

---

**Token Budget**: ~300-500 tokens. Agents can read full document or Grep for specific sections (e.g., commands, library usage).