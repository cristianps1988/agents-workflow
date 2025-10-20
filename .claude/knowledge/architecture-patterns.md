# Architecture Patterns

**Document your chosen architecture, dependency rules, and core patterns.**

**Purpose**: Ensure all code follows consistent architectural principles and agents understand the system structure.

---

## How to Use This Document

This document should contain:

1. **Architecture Choice**: Which pattern you're using (Clean, Hexagonal, Feature-based, etc.)
2. **Layer Definitions**: What each layer contains and is responsible for
3. **Dependency Rules**: What can import from what (CRITICAL)
4. **Core Patterns**: Repository, Service, Presenter patterns etc.
5. **State Management**: How you handle different types of state
6. **Before/After Examples**: Show correct vs incorrect implementations

**Token Size**: Typically 2000-4000 tokens. Agents use Grep to load specific sections.

---

## Template & Example

Below is an example using **Clean Architecture** adapted for React. **Replace with your chosen architecture.**

---

## Architecture Choice: Clean Architecture (Adapted for React)

**What is it?**
Clean Architecture organizes code in concentric layers where:
- Inner layers (core) contain business logic
- Outer layers (infrastructure, UI) contain implementations
- Dependencies point INWARD only

**Why this architecture?**
- Framework-agnostic business logic (testable, portable)
- Swappable infrastructure (change database/framework easily)
- Clear separation of concerns
- Enforced dependency rules

**Alternative Architectures** (choose what fits your project):
- Hexagonal Architecture (Ports & Adapters)
- Feature-based Architecture (domain modules)
- Layered Architecture (traditional MVC)
- Vertical Slice Architecture

---

## Dependency Rule (CRITICAL)

**The most important rule in this architecture:**

```
core/              ← Framework-agnostic, NEVER imports from outer layers
  ↑
infrastructure/    ← Implementations, CAN import from core/, NEVER from features/
  ↑
features/          ← UI layer, CAN import from core/ and infrastructure/
  ↑
app/               ← App setup, CAN import from all layers
```

### ❌ FORBIDDEN Imports

```typescript
// core/ importing from outer layers
// core/services/work-order-service.ts
import { supabase } from '@/infrastructure/supabase'; // ❌ FORBIDDEN
import { WorkOrderCard } from '@/features/work-orders/components'; // ❌ FORBIDDEN

// infrastructure/ importing from features/
// infrastructure/api/work-order-api.ts
import { useWorkOrders } from '@/features/work-orders/hooks'; // ❌ FORBIDDEN
```

### ✅ ALLOWED Imports

```typescript
// infrastructure/ importing from core/
// infrastructure/api/work-order-api.ts
import { IWorkOrderRepository } from '@/core/repositories/work-order-repository.interface'; // ✅
import { WorkOrder } from '@/core/entities/work-order.entity'; // ✅

// features/ importing from core/ and infrastructure/
// features/work-orders/hooks/use-work-orders.ts
import { WorkOrder } from '@/core/entities/work-order.entity'; // ✅
import { useRepositories } from '@/infrastructure/providers/repository-provider'; // ✅
```

---

## Layers

### 1. Domain Layer (core/)

**Location**: `src/core/`

**Responsibility**: Business logic, framework-agnostic

**Contains**:
- **Entities**: Domain models (e.g., User, WorkOrder, Invoice)
- **Repository Interfaces**: Contracts for data access
- **Services**: Business logic and orchestration
- **DTOs**: Data Transfer Objects for input/output

**Rules**:
- ❌ NO framework imports (React, Supabase, Axios, etc.)
- ❌ NO UI code
- ❌ NO infrastructure dependencies
- ✅ Pure TypeScript/JavaScript
- ✅ Platform-agnostic (could run in Node.js, browser, mobile)

**File Structure**:
```
src/core/
├── entities/
│   ├── user.entity.ts
│   └── work-order.entity.ts
├── repositories/
│   ├── user-repository.interface.ts
│   └── work-order-repository.interface.ts
├── services/
│   ├── user-service.ts
│   └── work-order-service.ts
└── dtos/
    ├── create-user.dto.ts
    └── update-work-order.dto.ts
```

**Example Entity**:
```typescript
// core/entities/work-order.entity.ts
export interface WorkOrder {
  id: string;
  title: string;
  description: string;
  status: WorkOrderStatus;
  assignedTo: string | null;
  createdAt: Date;
  updatedAt: Date;
}

export enum WorkOrderStatus {
  RECEIVED = 'received',
  ASSIGNED = 'assigned',
  IN_PROGRESS = 'in_progress',
  COMPLETED = 'completed',
  CANCELLED = 'cancelled'
}
```

**Example Repository Interface**:
```typescript
// core/repositories/work-order-repository.interface.ts
import { WorkOrder } from '../entities/work-order.entity';
import { CreateWorkOrderDTO, UpdateWorkOrderDTO } from '../dtos';

export interface IWorkOrderRepository {
  findAll(filters?: WorkOrderFilters): Promise<WorkOrder[]>;
  findById(id: string): Promise<WorkOrder | null>;
  create(data: CreateWorkOrderDTO): Promise<WorkOrder>;
  update(id: string, data: UpdateWorkOrderDTO): Promise<WorkOrder>;
  delete(id: string): Promise<void>;
}
```

**Example Service**:
```typescript
// core/services/work-order-service.ts
import { WorkOrder } from '../entities/work-order.entity';
import { IWorkOrderRepository } from '../repositories/work-order-repository.interface';

export class WorkOrderService {
  constructor(private repository: IWorkOrderRepository) {}

  async getAvailableWorkOrders(): Promise<WorkOrder[]> {
    // Business logic: Only return non-assigned, non-cancelled orders
    const allOrders = await this.repository.findAll();
    return allOrders.filter(
      order => order.status === 'received' && !order.assignedTo
    );
  }

  async assignWorker(orderId: string, workerId: string): Promise<WorkOrder> {
    // Business logic: Validate and assign
    const order = await this.repository.findById(orderId);
    if (!order) throw new Error('Work order not found');
    if (order.assignedTo) throw new Error('Already assigned');

    return await this.repository.update(orderId, {
      assignedTo: workerId,
      status: 'assigned'
    });
  }
}
```

---

### 2. Infrastructure Layer (infrastructure/)

**Location**: `src/infrastructure/`

**Responsibility**: External implementations (database, APIs, external services)

**Contains**:
- **API Implementations**: Implement repository interfaces
- **Database Clients**: Supabase, Prisma, etc.
- **External Services**: Third-party APIs
- **Providers**: Dependency injection setup

**Rules**:
- ✅ CAN import from `core/`
- ✅ CAN use external libraries (Supabase, Axios, etc.)
- ❌ NEVER import from `features/` or `app/`

**File Structure**:
```
src/infrastructure/
├── api/
│   ├── work-order-api.ts         # Implements IWorkOrderRepository
│   └── user-api.ts               # Implements IUserRepository
├── providers/
│   └── repository-provider.tsx   # Dependency injection
└── supabase/
    └── client.ts                 # Supabase client setup
```

**Example API Implementation**:
```typescript
// infrastructure/api/work-order-api.ts
import { IWorkOrderRepository } from '@/core/repositories/work-order-repository.interface';
import { WorkOrder } from '@/core/entities/work-order.entity';
import { CreateWorkOrderDTO } from '@/core/dtos';
import { supabase } from '../supabase/client';

export class WorkOrderApi implements IWorkOrderRepository {
  async findAll(): Promise<WorkOrder[]> {
    const { data, error } = await supabase
      .from('work_orders')
      .select('*');

    if (error) throw error;

    // Map snake_case database to camelCase entity
    return data.map(this.mapToEntity);
  }

  async findById(id: string): Promise<WorkOrder | null> {
    const { data, error } = await supabase
      .from('work_orders')
      .select('*')
      .eq('id', id)
      .single();

    if (error) throw error;
    return data ? this.mapToEntity(data) : null;
  }

  async create(dto: CreateWorkOrderDTO): Promise<WorkOrder> {
    // Map camelCase DTO to snake_case database
    const { data, error } = await supabase
      .from('work_orders')
      .insert(this.mapToDatabase(dto))
      .select()
      .single();

    if (error) throw error;
    return this.mapToEntity(data);
  }

  private mapToEntity(row: any): WorkOrder {
    return {
      id: row.id,
      title: row.title,
      description: row.description,
      status: row.status,
      assignedTo: row.assigned_to,
      createdAt: new Date(row.created_at),
      updatedAt: new Date(row.updated_at)
    };
  }

  private mapToDatabase(entity: Partial<WorkOrder>): any {
    return {
      title: entity.title,
      description: entity.description,
      status: entity.status,
      assigned_to: entity.assignedTo
    };
  }
}
```

**Example Provider** (Dependency Injection):
```typescript
// infrastructure/providers/repository-provider.tsx
import React, { createContext, useContext } from 'react';
import { IWorkOrderRepository } from '@/core/repositories/work-order-repository.interface';
import { WorkOrderApi } from '../api/work-order-api';
import { supabase } from '../supabase/client';

interface Repositories {
  workOrderRepository: IWorkOrderRepository;
}

const RepositoryContext = createContext<Repositories | null>(null);

export function RepositoryProvider({ children }: { children: React.ReactNode }) {
  const repositories: Repositories = {
    workOrderRepository: new WorkOrderApi(supabase)
  };

  return (
    <RepositoryContext.Provider value={repositories}>
      {children}
    </RepositoryContext.Provider>
  );
}

export const useRepositories = () => {
  const context = useContext(RepositoryContext);
  if (!context) throw new Error('useRepositories must be within RepositoryProvider');
  return context;
};
```

---

### 3. Presentation Layer (features/)

**Location**: `src/features/`

**Responsibility**: UI components, pages, React-specific code

**Contains**:
- **Components**: React components for the feature
- **Pages**: Full page components
- **Hooks**: Custom React hooks (data fetching, mutations)
- **Utils**: Feature-specific utilities

**Rules**:
- ✅ CAN import from `core/` and `infrastructure/`
- ✅ React, UI libraries allowed
- ❌ NO business logic (delegate to services)

**File Structure**:
```
src/features/
└── work-orders/
    ├── components/
    │   ├── WorkOrderCard.tsx
    │   ├── WorkOrderForm.tsx
    │   └── WorkOrderList.tsx
    ├── hooks/
    │   ├── use-work-orders.ts         # Query hook
    │   └── use-create-work-order.ts   # Mutation hook
    ├── pages/
    │   ├── WorkOrdersPage.tsx
    │   └── WorkOrderDetailPage.tsx
    └── utils/
        └── work-order-formatters.ts
```

**Example Query Hook**:
```typescript
// features/work-orders/hooks/use-work-orders.ts
import { useQuery } from '@tanstack/react-query';
import { useRepositories } from '@/infrastructure/providers/repository-provider';

export const useWorkOrders = () => {
  const { workOrderRepository } = useRepositories();

  return useQuery({
    queryKey: ['workOrders'],
    queryFn: () => workOrderRepository.findAll()
  });
};
```

**Example Mutation Hook**:
```typescript
// features/work-orders/hooks/use-create-work-order.ts
import { useMutation, useQueryClient } from '@tanstack/react-query';
import { useRepositories } from '@/infrastructure/providers/repository-provider';
import { CreateWorkOrderDTO } from '@/core/dtos';

export const useCreateWorkOrder = () => {
  const { workOrderRepository } = useRepositories();
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: (data: CreateWorkOrderDTO) => workOrderRepository.create(data),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['workOrders'] });
    }
  });
};
```

**Example Component**:
```typescript
// features/work-orders/components/WorkOrderList.tsx
import { useWorkOrders } from '../hooks/use-work-orders';
import { WorkOrderCard } from './WorkOrderCard';
import { getTextMap } from '@/lib/get-text-map';

export function WorkOrderList() {
  const { data: workOrders, isLoading } = useWorkOrders();
  const text = getTextMap();

  if (isLoading) return <div>{text.common.loading}</div>;

  return (
    <div>
      <h1>{text.workOrders.listTitle}</h1>
      {workOrders?.map(order => (
        <WorkOrderCard key={order.id} workOrder={order} />
      ))}
    </div>
  );
}
```

---

### 4. Shared Layer (shared/)

**Location**: `src/shared/`

**Responsibility**: Reusable code across features

**Contains**:
- **Components**: Reusable UI components (buttons, modals, etc.)
- **Hooks**: Reusable React hooks
- **Utils**: Utilities, helpers
- **Constants**: Text maps, configuration
- **Types**: Shared TypeScript types

**File Structure**:
```
src/shared/
├── components/
│   ├── Button.tsx
│   ├── Modal.tsx
│   └── DataTable.tsx
├── hooks/
│   ├── use-disclosure.ts
│   └── use-local-storage.ts
├── utils/
│   ├── format-date.ts
│   └── validation.ts
├── constants/
│   └── text/
│       ├── common.text-map.ts
│       └── get-text-map.ts
└── types/
    └── common.types.ts
```

---

### 5. Application Layer (app/)

**Location**: `src/app/`

**Responsibility**: App-level setup and configuration

**Contains**:
- **Providers**: React Query, Theme, Auth providers
- **Routing**: Router configuration
- **Layout**: App shell, navigation

**Rules**:
- ✅ CAN import from all layers
- ✅ Setup and configuration code

**File Structure**:
```
src/app/
├── providers/
│   ├── query-provider.tsx
│   ├── theme-provider.tsx
│   └── auth-provider.tsx
├── router/
│   └── routes.tsx
└── layout/
    ├── AppLayout.tsx
    └── Navigation.tsx
```

---

## State Management Strategy

### Decision Matrix

| State Type | Tool | When to Use | Example |
|------------|------|-------------|---------|
| **Server State** | React Query | Data from backend (fetched, cached) | User list, work orders |
| **Client State** | Zustand | UI state, local preferences | Sidebar open, theme |
| **Local State** | useState | Component-only state | Form input, modal open |
| **Form State** | React Hook Form | Complex forms with validation | Multi-step forms |

### ❌ WRONG: Zustand for Server State

```typescript
// DON'T DO THIS
const useWorkOrderStore = create((set) => ({
  workOrders: [],
  loading: false,
  fetchWorkOrders: async () => {
    set({ loading: true });
    const data = await api.getWorkOrders();
    set({ workOrders: data, loading: false });
  }
}));
```

### ✅ CORRECT: React Query for Server State

```typescript
// DO THIS
const useWorkOrders = () => {
  return useQuery({
    queryKey: ['workOrders'],
    queryFn: () => workOrderRepository.findAll()
  });
};
```

### ✅ CORRECT: Zustand for Client/UI State

```typescript
// DO THIS
const useUIStore = create((set) => ({
  sidebarOpen: true,
  theme: 'light',
  toggleSidebar: () => set((state) => ({ sidebarOpen: !state.sidebarOpen })),
  setTheme: (theme) => set({ theme })
}));
```

---

## Core Patterns

### Repository Pattern

**Purpose**: Abstract data access, enable testing, swappable backends

**4-Step Implementation**:

**1. Define Interface** (core/repositories/):
```typescript
export interface IWorkOrderRepository {
  findAll(): Promise<WorkOrder[]>;
  findById(id: string): Promise<WorkOrder | null>;
  create(data: CreateWorkOrderDTO): Promise<WorkOrder>;
  update(id: string, data: UpdateWorkOrderDTO): Promise<WorkOrder>;
}
```

**2. Implement in Infrastructure** (infrastructure/api/):
```typescript
export class WorkOrderApi implements IWorkOrderRepository {
  async findAll(): Promise<WorkOrder[]> {
    // Supabase/API implementation
  }
}
```

**3. Provide via Dependency Injection** (infrastructure/providers/):
```typescript
export const useRepositories = () => {
  return {
    workOrderRepository: new WorkOrderApi(supabase)
  };
};
```

**4. Use in Hooks/Components** (features/):
```typescript
const { workOrderRepository } = useRepositories();
const workOrders = await workOrderRepository.findAll();
```

---

### Service Pattern (Optional)

**Purpose**: Complex business logic, orchestration across repositories

**When to Use**:
- Complex validations
- Multi-entity operations
- Business rule enforcement
- Calculations

**Example**:
```typescript
// core/services/work-order-service.ts
export class WorkOrderService {
  constructor(
    private workOrderRepository: IWorkOrderRepository,
    private userRepository: IUserRepository
  ) {}

  async assignWorker(orderId: string, workerId: string): Promise<WorkOrder> {
    // Business logic: Validate worker exists
    const worker = await this.userRepository.findById(workerId);
    if (!worker || worker.role !== 'worker') {
      throw new Error('Invalid worker');
    }

    // Business logic: Validate order is available
    const order = await this.workOrderRepository.findById(orderId);
    if (!order) throw new Error('Order not found');
    if (order.assignedTo) throw new Error('Already assigned');
    if (order.status === 'cancelled') throw new Error('Order cancelled');

    // Update
    return await this.workOrderRepository.update(orderId, {
      assignedTo: workerId,
      status: 'assigned'
    });
  }
}
```

---

## Instructions for Your Project

**To customize this document:**

1. **Choose your architecture** (Clean, Hexagonal, Feature-based, etc.)
2. **Document dependency rules** clearly with diagrams
3. **Define each layer** (location, responsibility, rules, examples)
4. **Show before/after examples** for common patterns
5. **Document state management** strategy for your stack
6. **Include core patterns** (Repository, Service, Presenter, etc.)
7. **Keep examples practical** based on your actual domain

**Agents will use Grep** to load specific sections, so organize with clear headings:
- `## Dependency Rule`
- `## Repository Pattern`
- `## State Management Strategy`
- etc.

---

**Token Budget**: 2000-4000 tokens. Agents load sections as needed via Grep.