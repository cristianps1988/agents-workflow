# Critical Constraints

**Non-negotiable rules that MUST be followed in all code. Keep this document ~200-300 tokens.**

**Purpose**: Every AI agent reads this FIRST to understand fundamental architectural rules and prevent common anti-patterns.

---

## How to Use This Document

1. **Define 5-10 critical rules** for your project
2. **Use ❌/✅ format** for clarity
3. **Include code examples** showing correct vs incorrect patterns
4. **Keep it concise** (~200-300 tokens total)
5. **Update** as you discover new critical patterns

---

## Template Structure

Below is a template with examples. **Replace with your project's actual critical rules.**

---

## 1. [Your Critical Rule #1 - Example: No Barrel File Imports]

❌ **NEVER**: Use barrel file imports (index.ts that re-exports everything)
✅ **ALWAYS**: Direct imports from specific files

**Why**: Barrel files break tree-shaking, increase bundle size, and slow down builds.

**Example**:

```typescript
// ❌ WRONG: Barrel import
import { User, UserService, useUser } from '@/features/users';

// ✅ CORRECT: Direct imports
import { User } from '@/features/users/entities/user.entity';
import { UserService } from '@/features/users/services/user-service';
import { useUser } from '@/features/users/hooks/use-user';
```

---

## 2. [Your Critical Rule #2 - Example: Repository Pattern Required]

❌ **NEVER**: Direct database/API calls in components or UI layer
✅ **ALWAYS**: Use repository pattern via custom hooks

**Why**: Enables testing, swappable backends, separation of concerns.

**Example**:

```typescript
// ❌ WRONG: Direct database access in component
import { supabase } from '@/lib/supabase';

function WorkOrderList() {
  const { data } = await supabase
    .from('work_orders')
    .select('*');
}

// ✅ CORRECT: Via repository hook
import { useWorkOrders } from '@/features/work-orders/hooks/use-work-orders';

function WorkOrderList() {
  const { data } = useWorkOrders();
}
```

**Correct Architecture Flow**:
```
Component → Hook → Repository Interface (core/) → Implementation (infrastructure/)
```

---

## 3. [Your Critical Rule #3 - Example: No Hardcoded Strings]

❌ **NEVER**: Hardcoded user-facing text in components
✅ **ALWAYS**: Externalize all text to text maps

**Why**: Enables future i18n, centralized text management, consistency.

**Example**:

```typescript
// ❌ WRONG: Hardcoded strings
<Button>Save Changes</Button>
<p>Are you sure you want to delete this item?</p>

// ✅ CORRECT: From text map
import { getTextMap } from '@/lib/get-text-map';

const text = getTextMap();

<Button>{text.common.saveChanges}</Button>
<p>{text.common.deleteConfirmation}</p>
```

**Text Map Structure**:
```typescript
// shared/constants/text/common.text-map.ts
export const commonTextMap = {
  saveChanges: "Save Changes",
  cancel: "Cancel",
  deleteConfirmation: "Are you sure you want to delete this item?"
} as const;
```

---

## 4. [Your Critical Rule #4 - Example: State Management Separation]

❌ **NEVER**: Use Zustand for server state (data from backend)
✅ **ALWAYS**: React Query for server state, Zustand only for client/UI state

**Why**: React Query handles caching, refetching, optimistic updates better.

**Example**:

```typescript
// ❌ WRONG: Zustand for server data
const useWorkOrderStore = create((set) => ({
  workOrders: [],
  fetchWorkOrders: async () => {
    const data = await api.getWorkOrders();
    set({ workOrders: data });
  }
}));

// ✅ CORRECT: React Query for server data
const useWorkOrders = () => {
  return useQuery({
    queryKey: ['workOrders'],
    queryFn: () => workOrderRepository.findAll()
  });
};

// ✅ CORRECT: Zustand for client/UI state only
const useUIStore = create((set) => ({
  sidebarOpen: true,
  theme: 'light',
  toggleSidebar: () => set((state) => ({ sidebarOpen: !state.sidebarOpen }))
}));
```

**Decision Matrix**:
- **Server State** (backend data) → React Query
- **Client State** (UI state, local preferences) → Zustand
- **Local State** (component-only) → useState
- **Form State** (complex forms) → React Hook Form

---

## 5. [Your Critical Rule #5 - Example: No Business Logic in Components]

❌ **NEVER**: Complex business logic, validations, or calculations in components
✅ **ALWAYS**: Extract to services or custom hooks

**Why**: Testability, reusability, separation of concerns.

**Example**:

```typescript
// ❌ WRONG: Business logic in component
function WorkOrderForm() {
  const handleSubmit = (data) => {
    // Complex validation logic
    if (data.amount < 0) throw new Error('...');
    if (data.date > new Date()) throw new Error('...');
    // Complex calculation
    const total = data.amount + (data.amount * 0.15) + data.materials;
    // Direct API call
    await api.createWorkOrder({ ...data, total });
  };
}

// ✅ CORRECT: Business logic in service
// services/work-order-service.ts
class WorkOrderService {
  validate(data: WorkOrderInput): ValidationResult {
    // Validation logic here
  }

  calculateTotal(data: WorkOrderInput): number {
    // Calculation logic here
  }

  async create(data: WorkOrderInput): Promise<WorkOrder> {
    const validation = this.validate(data);
    if (!validation.valid) throw new Error(validation.error);

    const total = this.calculateTotal(data);
    return await this.repository.create({ ...data, total });
  }
}

// Component
function WorkOrderForm() {
  const { createWorkOrder } = useWorkOrderService();

  const handleSubmit = async (data) => {
    await createWorkOrder(data); // Service handles everything
  };
}
```

---

## Additional Critical Rules (Add Your Own)

## 6. [Your Rule - Example: TypeScript Strict Mode]

❌ **NEVER**: Use `any` type
✅ **ALWAYS**: Proper TypeScript types

```typescript
// ❌ WRONG
const processData = (data: any) => { ... }

// ✅ CORRECT
interface UserData {
  id: string;
  name: string;
}
const processData = (data: UserData) => { ... }
```

---

## 7. [Your Rule - Example: Package Manager Consistency]

❌ **NEVER**: Mix package managers (npm, yarn, pnpm, bun)
✅ **ALWAYS**: Use [your chosen package manager] exclusively

```bash
# ❌ WRONG
npm install some-package

# ✅ CORRECT (example: pnpm)
pnpm add some-package
```

---

## Instructions for Your Project

**To customize this document:**

1. **Replace example rules** with your actual critical constraints
2. **Keep it concise**: Target 200-300 tokens total
3. **Prioritize**: Include only the MOST critical rules (5-10 maximum)
4. **Add examples**: Show ❌ wrong and ✅ correct code
5. **Explain why**: Brief reason for each rule
6. **Update regularly**: Add new rules as you discover critical patterns

**What makes a rule "critical"?**
- Violating it causes bugs or architectural issues
- It's a common mistake AI agents make
- It's fundamental to your architecture
- It's non-negotiable (not a preference)

**What NOT to include**:
- Detailed architecture explanations (put in architecture-patterns.md)
- Naming conventions (put in file-structure.md)
- Technology choices (put in tech-stack.md)
- Business rules (put in business-rules.md)

This document should be **quick to read** (~1 minute) and **immediately actionable**.

---

## Verification Checklist for Agents

Before proceeding with any task, verify:

- [ ] I have read this entire document
- [ ] I understand all critical rules
- [ ] I will follow these rules in all code I plan or review
- [ ] I will flag violations of these rules if I find them
- [ ] If any rule is unclear, I will ask for clarification before proceeding

---

**Token Budget**: Keep under 300 tokens when used by agents (remove instructions section when finalizing).