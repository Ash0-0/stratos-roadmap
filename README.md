# Stratos — Enterprise HR & Operations Platform

> **Goal**: Build on existing NestJS / Angular experience to systematically master enterprise full-stack development with React + Next.js and dual Java backends, ready for mainstream Full-Stack / Senior Frontend job interviews.

---

## Project Overview

**Stratos** is a multi-module enterprise HR and operations management system covering employee management, department structure, leave request approval, permission management, data reports, and more.

Systems like this are extremely common in real work environments. Every technical topic that comes up in interviews — complex forms, data tables, permission-based routing, state management, multi-role workflows — can be fully demonstrated in a single project.

### Feature Modules

| Module | Description |
|--------|-------------|
| Dashboard | Data overview, KPI cards, trend charts |
| Employee Management | Employee CRUD, avatar upload, complex info forms |
| Department Management | Department tree structure, org chart |
| Leave Management | Leave requests, multi-level approval workflow, leave calendar |
| Role & Permissions | Dynamic roles, fine-grained permission configuration |
| Audit Logs | Operation records, change tracking |
| Notifications | In-app messages + email notifications (handled by Quarkus service) |
| Reports | Export CSV / PDF, async generation (handled by Quarkus service) |

---

## GitHub Repos Plan

```
stratos-web      ← Next.js frontend (primary focus of this roadmap)
stratos-api      ← Spring Boot main API (core business logic)
stratos-ops      ← Quarkus microservice (notifications + reports, async / reactive)
```

> **Why two backends?**
> - Spring Boot: mature ecosystem, suited for main business CRUD + complex transactions
> - Quarkus: lightweight reactive, suited for event-driven scenarios (push notifications, async report generation), bridges prior experience
> - Two projects to demonstrate understanding of different tech stacks in interviews, with direct side-by-side comparison

---

## My Starting Point

| Technology | Proficiency | Notes |
|------------|-------------|-------|
| Angular | ★★★★☆ | Years of production experience |
| Node.js / Express | ★★★★☆ | Actual work experience |
| NestJS | ★★★★☆ | Actual work experience, understands DI / modules / decorators |
| Lit Element | ★★★☆☆ | ~1 year of actual experience |
| React / Next.js | ★★☆☆☆ | Theory only, **lacks hands-on** ← frontend key focus |
| Spring Boot | ★★☆☆☆ | Concept-level only, **lacks hands-on** ← backend key focus |
| Quarkus | ★★☆☆☆ | Some exposure, needs deeper dive |
| PostgreSQL | ★★☆☆☆ | Conceptual understanding, lacks hands-on |
| Docker / Containers | ★★☆☆☆ | Some knowledge, lacks hands-on |

### Knowledge Transfer (NestJS → Spring Boot)

| NestJS | Spring Boot | Notes |
|--------|-------------|-------|
| `@Module()` | `@Configuration` | Modular configuration |
| `@Injectable()` | `@Service` / `@Component` | Dependency injection |
| `@Controller()` | `@RestController` | Route controller |
| `@Get()` / `@Post()` | `@GetMapping` / `@PostMapping` | HTTP method mapping |
| `Middleware` | `Filter` / `Interceptor` | Request interception |
| `Guards` | `Spring Security` | Auth & authorization |
| `Pipes` | `@Valid` + Bean Validation | Parameter validation |
| `TypeORM Entity` | `@Entity` (JPA) | ORM entity |

---

## Full Tech Stack

### Frontend (stratos-web)

```
Next.js 14              App Router, Server Components, Middleware
TypeScript              Full type safety
Tailwind CSS            Utility-first styling, industry standard
shadcn/ui               Enterprise component library based on Radix UI, highly customizable
React Hook Form         Complex form management, performance optimized
Zod                     Schema validation, deeply integrated with RHF
TanStack Query v5       Server state management (REST data fetching / caching / sync)
Apollo Client v3        GraphQL client (connects to Quarkus GraphQL layer)
GraphQL Code Generator  Auto-generate TypeScript types + Hooks from Schema
Zustand                 Client-side global state (user info, UI state)
TanStack Table v8       Enterprise data table (sorting / filtering / pagination / column customization)
NextAuth.js v5          Auth integration, JWT handling
Axios                   HTTP requests, interceptor wrapper (for REST)
Recharts                Charts (line, bar, pie)
React Hot Toast         Lightweight toast notifications
date-fns                Date utilities
Lucide React            Icon library
```

> **Dual Client Strategy**: Axios + TanStack Query handles REST (Spring Boot), Apollo Client handles GraphQL (Quarkus). Both coexist in the project with clear separation of responsibilities.

### Backend Main API (stratos-api)

```
Spring Boot 3      Main framework
Java 17            LTS version, Records, Sealed Classes
Spring Data JPA    ORM, PostgreSQL driver
Spring Security    Auth & authorization framework
JWT (JJWT)         Token generation & validation
Flyway             Database migration management
MapStruct          Entity ↔ DTO mapping
Lombok             Reduces boilerplate code
Swagger / OpenAPI  API documentation
JUnit 5 + Mockito  Unit testing + mocking
Testcontainers     Integration testing (spins up real PostgreSQL container)
```

### Backend Microservice (stratos-ops)

```
Quarkus 3             Lightweight reactive framework
SmallRye GraphQL      MicroProfile GraphQL implementation, native Quarkus support
Mutiny                Quarkus-native reactive programming library
RESTEasy Reactive     Reactive REST endpoints
Hibernate Reactive    Reactive ORM
Panache               Simplifies repository code
SmallRye JWT          JWT validation
Mailer                Email sending (SMTP)
WebSocket             Real-time notification push (underlying GraphQL Subscription)
```

### Infrastructure

```
PostgreSQL 15      Primary database
Redis              Caching (sessions, rate limiting)
Docker             Containerization
Docker Compose     Local multi-service orchestration
GitHub Actions     CI/CD Pipeline
```

---

## System Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                         Browser                               │
│               Next.js 14 (App Router)                        │
│                                                               │
│   Server Components (RSC)      Client Components             │
│   ├─ Layout / Page render      ├─ React Hook Form            │
│   ├─ SEO / Initial data        ├─ TanStack Query  (REST)     │
│   └─ Server Actions            ├─ Apollo Client   (GraphQL)  │
│                                └─ Zustand store              │
└──────┬────────────────────────────────┬──────────────────────┘
       │                                │
       │  REST + JWT                    │  GraphQL + JWT
       │  (CRUD / Auth)                 │  (Query / Mutation / Subscription)
       ▼                                ▼
┌─────────────────────┐      ┌──────────────────────────────┐
│    stratos-api      │      │       stratos-ops            │
│    Spring Boot 3    │      │       Quarkus 3              │
│                     │      │                              │
│  Auth / RBAC        │─────▶│  GraphQL Schema              │
│  Employee CRUD      │ HTTP │  Dashboard aggregated queries │
│  Department / Leave │      │  Cross-entity complex data   │
│  Audit Logging      │      │  Notification (Subscription) │
│  REST API           │      │  Report async generation     │
└────────┬────────────┘      └──────────────────────────────┘
         │
         ▼
┌─────────────────────┐
│     PostgreSQL      │
└─────────────────────┘

Responsibility breakdown:
  Axios + TanStack Query → REST → Spring Boot  (Login, CRUD, form submissions)
  Apollo Client          → GraphQL → Quarkus   (Dashboard, aggregated queries, real-time push)
```

---

## Learning Roadmap

### Phase 1: Spring Boot Main API (3 weeks)

**Goal**: Independently build a REST API with full CRUD, authentication, and pagination

#### 1.1 Project Setup
- [ ] [Spring Initializr](https://start.spring.io): select Web / JPA / PostgreSQL / Security / Validation / Lombok
- [ ] `application.yml` multi-environment configuration (dev / prod)
- [ ] Understand Maven dependency tree (`mvn dependency:tree`)

#### 1.2 Layered Architecture in Practice
```
Controller   → Accept requests, validate params, return ResponseEntity
Service      → Business logic, transaction boundary (@Transactional)
Repository   → Extends JpaRepository, complex queries use @Query
Entity       → Database mapping, associations
DTO          → Request DTO (input) / Response DTO (output), never expose Entity directly
Mapper       → MapStruct handles Entity ↔ DTO conversion
```

#### 1.3 Database & JPA
- [ ] Table relationships: `@ManyToOne` / `@OneToMany` / `@ManyToMany`
- [ ] Lazy loading vs eager loading (N+1 is a must-know interview topic)
- [ ] Flyway migration files (`V1__init_schema.sql`)
- [ ] Paginated queries: `Pageable` + `Page<T>`
- [ ] Custom JPQL / Native Query

**Practice**: Complete CRUD API for Employee + Department with pagination, search, and sorting

#### 1.4 Authentication: Spring Security + JWT
- [ ] `SecurityFilterChain` config, permit public endpoints
- [ ] `JwtAuthFilter` extends `OncePerRequestFilter`
- [ ] `UserDetailsService` loads user from database
- [ ] Refresh Token: Access Token (15min) + Refresh Token (7 days)
- [ ] `@PreAuthorize("hasRole('ADMIN')")` method-level authorization

#### 1.5 Exception Handling
- [ ] `@RestControllerAdvice` global exception handling
- [ ] Unified response body: `ApiResponse<T>` wrapper
- [ ] Custom business exceptions: `ResourceNotFoundException`, `ForbiddenException`

---

### Phase 2: Quarkus + GraphQL Microservice (3 weeks)

**Goal**: Understand the reactive programming model, implement a complete GraphQL layer with SmallRye GraphQL, connecting to the frontend Apollo Client

#### 2.1 Getting Started with Quarkus
- [ ] Create project with Quarkus CLI (or [code.quarkus.io](https://code.quarkus.io))
  - Select extensions: `SmallRye GraphQL`, `Hibernate ORM with Panache`, `JDBC PostgreSQL`, `SmallRye JWT`, `Mailer`
- [ ] Dev Mode: `quarkus dev` (hot reload, built-in GraphQL UI at `/q/graphql-ui`)
- [ ] Native Build concept (conceptual understanding only, GraalVM)

#### 2.2 SmallRye GraphQL — Schema Definition

Quarkus uses a **code-first** approach where Java classes automatically generate the GraphQL Schema — no need to write `.graphql` files by hand.

```java
// entity/Employee.java — JPA entity, also serves as a GraphQL Type
@Entity
@Table(name = "employees")
public class Employee {
    @Id
    @GeneratedValue
    public UUID id;

    public String name;
    public String email;

    @ManyToOne
    public Department department;     // → automatically becomes a nestable query field

    @OneToMany(mappedBy = "employee")
    public List<Leave> leaves;        // → supports employee { leaves { status } }
}
```

Generated GraphQL Schema (auto-generated):
```graphql
type Employee {
  id: String!
  name: String!
  email: String!
  department: Department
  leaves: [Leave]
}

type Query {
  employees(page: Int, size: Int, search: String): EmployeePage
  employee(id: String!): Employee
  dashboardStats: DashboardStats
}

type Mutation {
  updateLeaveStatus(id: String!, status: LeaveStatus!): Leave
}

type Subscription {
  notificationAdded(userId: String!): Notification
}
```

#### 2.3 Query — Data Fetching

```java
@GraphQLApi
public class EmployeeResource {

    @Inject
    EmployeeService employeeService;

    // Basic query
    @Query("employee")
    @Description("Find a single employee by ID (includes department and leave records)")
    public Employee getEmployee(@Name("id") String id) {
        return employeeService.findById(UUID.fromString(id));
    }

    // Paginated + search
    @Query("employees")
    public EmployeePage listEmployees(
        @Name("page") @DefaultValue("0") int page,
        @Name("size") @DefaultValue("20") int size,
        @Name("search") String search
    ) {
        return employeeService.findAll(page, size, search);
    }
}

@GraphQLApi
public class DashboardResource {

    @Inject EmployeeService employeeService;
    @Inject LeaveService leaveService;
    @Inject ProjectService projectService;

    // Aggregated query: returns all Dashboard data in a single request
    // This is the core advantage of GraphQL over REST
    @Query("dashboardStats")
    public DashboardStats getDashboardStats() {
        return new DashboardStats(
            employeeService.count(),
            leaveService.countPending(),
            projectService.countActive(),
            employeeService.findRecentJoins(5)
        );
    }
}
```

#### 2.4 Mutation — Data Modification

```java
@GraphQLApi
public class LeaveResource {

    @Inject LeaveService leaveService;
    @Inject NotificationService notificationService;

    @Mutation("updateLeaveStatus")
    @Description("Approve or reject leave request (MANAGER / ADMIN role required)")
    @RolesAllowed({"MANAGER", "ADMIN"})   // SmallRye JWT permission control
    public Leave updateLeaveStatus(
        @Name("id") String id,
        @Name("status") LeaveStatus status,
        @Name("comment") String comment
    ) {
        Leave updated = leaveService.updateStatus(id, status, comment);
        // Trigger notification after approval (async)
        notificationService.notifyLeaveResult(updated);
        return updated;
    }
}
```

#### 2.5 Subscription — Real-Time Push (WebSocket underlying)

```java
@GraphQLApi
public class NotificationResource {

    @Inject NotificationService notificationService;

    // Auto-pushes to subscribed clients whenever a new notification arrives
    // WebSocket underlying, Apollo Client manages the connection automatically
    @Subscription("notificationAdded")
    public Multi<Notification> notificationAdded(
        @Name("userId") String userId
    ) {
        return notificationService.streamForUser(userId);
    }
}

// NotificationService.java
@ApplicationScoped
public class NotificationService {

    // Mutiny's Multi is analogous to RxJS Observable
    private final BroadcastProcessor<Notification> processor =
        BroadcastProcessor.create();

    public Multi<Notification> streamForUser(String userId) {
        return processor.filter(n -> n.userId.equals(userId));
    }

    // Call this method to broadcast a new notification
    public void publish(Notification notification) {
        processor.onNext(notification);
    }
}
```

#### 2.6 Reactive Programming Basics (Mutiny)

```java
// Uni<T> — single async result, analogous to JS Promise
public Uni<Employee> findByIdAsync(UUID id) {
    return Uni.createFrom()
        .item(() -> employeeRepository.findById(id))
        .runSubscriptionOn(Infrastructure.getDefaultWorkerPool());
}

// Multi<T> — data stream, analogous to RxJS Observable
public Multi<Notification> streamAll() {
    return Multi.createFrom().items(notificationRepository.findAll().stream());
}
```

- [ ] `Uni<T>`: single async value (analogous to Promise)
- [ ] `Multi<T>`: data stream (analogous to RxJS Observable)
- [ ] Panache: simplified Repository (`Employee.findById(id)` direct call)

#### 2.7 GraphQL Introspection & Debugging
- [ ] Quarkus Dev Mode comes with built-in GraphQL Playground (`http://localhost:8081/q/graphql-ui`)
- [ ] Test Query / Mutation / Subscription in the Playground
- [ ] Understand GraphQL Introspection: the frontend Code Generator depends on it

#### 2.8 Comparison Summary (interview bonus)

| Dimension | Spring Boot | Quarkus |
|-----------|-------------|---------|
| Startup time | 2–5s | < 0.5s |
| Memory usage | Higher | Very low (Native mode) |
| Programming model | Imperative (default) | Reactive (native) |
| GraphQL support | spring-graphql (newer) | SmallRye GraphQL (MicroProfile standard) |
| Ecosystem maturity | Very mature | Newer, fast-growing |
| Best suited for | Complex business logic, enterprise apps | Microservices, GraphQL layer, Serverless |

---

### Phase 3: Next.js Frontend (3–4 weeks)

**Goal**: Master the complete frontend architecture of an enterprise admin app, able to independently set up a production-grade Next.js project

#### 3.1 Next.js 14 App Router Core Concepts

```
app/
├── layout.tsx          ← Root layout (HTML shell, fonts, Providers)
├── page.tsx            ← Home page (Server Component)
├── (auth)/             ← Route Group, doesn't affect URL path
│   ├── login/page.tsx
│   └── register/page.tsx
├── (dashboard)/        ← Protected route group (requires login)
│   ├── layout.tsx      ← Dashboard layout (sidebar, top bar)
│   ├── page.tsx        ← / Dashboard home
│   ├── employees/
│   │   ├── page.tsx    ← Employee list
│   │   ├── [id]/
│   │   │   └── page.tsx  ← Employee details
│   │   └── new/page.tsx  ← Create employee
│   ├── departments/
│   ├── leaves/
│   └── settings/
└── api/
    └── auth/[...nextauth]/route.ts  ← NextAuth route handler
```

**Server Components vs Client Components (common interview question)**

```tsx
// Server Component (default): renders on server, can fetch data directly
// Cannot use useState, useEffect, or browser APIs
async function EmployeeList() {
  const employees = await fetchEmployees(); // direct await, no useEffect needed
  return <Table data={employees} />;
}

// Client Component: add 'use client', can use Hooks and event handlers
'use client';
function SearchBar() {
  const [query, setQuery] = useState('');
  return <input onChange={(e) => setQuery(e.target.value)} />;
}
```

**When to use which?**
- Server Component: static content, initial data fetching, SEO-critical content
- Client Component: interactions (clicks, inputs), using Hooks, browser APIs

#### 3.2 React Hook Form + Zod (key skill)

One of the most critical skills for enterprise admin apps. The employee creation form has 15+ fields requiring a comprehensive validation strategy.

**Basic Pattern**
```tsx
'use client';
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

const employeeSchema = z.object({
  firstName:   z.string().min(2, 'At least 2 characters'),
  lastName:    z.string().min(1, 'Required'),
  email:       z.string().email('Invalid email format'),
  phone:       z.string().regex(/^\+?[\d\s-]{10,}$/, 'Invalid phone number format'),
  departmentId: z.string().uuid('Please select a department'),
  role:        z.enum(['ADMIN', 'MANAGER', 'EMPLOYEE']),
  startDate:   z.date({ required_error: 'Please select a start date' }),
  salary:      z.number().min(0).optional(),
});

type EmployeeFormData = z.infer<typeof employeeSchema>;

export function EmployeeForm({ defaultValues }: { defaultValues?: Partial<EmployeeFormData> }) {
  const {
    register,
    handleSubmit,
    control,          // Used for Controller-wrapped controlled components (DatePicker, Select)
    formState: { errors, isSubmitting, isDirty },
    reset,
    watch,            // Watch field values to conditionally show other fields
    setValue,
  } = useForm<EmployeeFormData>({
    resolver: zodResolver(employeeSchema),
    defaultValues,
  });

  const onSubmit = async (data: EmployeeFormData) => {
    await createEmployee(data);  // TanStack Query mutation
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('email')} />
      {errors.email && <p>{errors.email.message}</p>}
      {/* ... */}
    </form>
  );
}
```

**Advanced Scenarios to Master**
- [ ] `Controller` component: wraps shadcn/ui components like Select and DatePicker that don't natively support `register`
- [ ] `useFieldArray`: dynamically add/remove fields (e.g., multiple contacts)
- [ ] Conditional fields: use `watch()` to observe a field value and conditionally show other fields
- [ ] Multi-step form: multiple `useForm` instances + state machine to control steps
- [ ] Server validation error display: `setError('email', { message: 'Email already exists' })`
- [ ] File upload: combine `Controller` + `FormData`
- [ ] Form dirty state: prompt "unsaved changes" before leaving the page

#### 3.3 TanStack Query v5 (Server State Management)

Replaces the traditional `useEffect + useState` data-fetching pattern with built-in caching, retries, and synchronization.

**Data Fetching**
```tsx
// hooks/useEmployees.ts
export function useEmployees(params: EmployeeQueryParams) {
  return useQuery({
    queryKey: ['employees', params],   // Cache key, auto re-fetches when params change
    queryFn: () => api.employees.list(params),
    staleTime: 1000 * 60 * 5,         // Don't re-fetch within 5 minutes
    placeholderData: keepPreviousData, // Keep previous data during page transitions to avoid flickering
  });
}

// hooks/useEmployee.ts
export function useEmployee(id: string) {
  return useQuery({
    queryKey: ['employees', id],
    queryFn: () => api.employees.getById(id),
    enabled: !!id,  // Don't fetch when id is empty
  });
}
```

**Data Mutation**
```tsx
export function useCreateEmployee() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: api.employees.create,
    onSuccess: () => {
      // Invalidate list cache after successful creation, triggers re-fetch
      queryClient.invalidateQueries({ queryKey: ['employees'] });
      toast.success('Employee created successfully');
    },
    onError: (error: ApiError) => {
      // Handle field validation errors returned from server
      if (error.status === 422) {
        error.fieldErrors.forEach(({ field, message }) =>
          form.setError(field, { message })
        );
      }
    },
  });
}
```

**Optimistic Update**
```tsx
// Immediately remove from UI on delete click, roll back on error
useMutation({
  mutationFn: api.employees.delete,
  onMutate: async (id) => {
    await queryClient.cancelQueries({ queryKey: ['employees'] });
    const previous = queryClient.getQueryData(['employees']);
    queryClient.setQueryData(['employees'], (old) =>
      old.filter((e) => e.id !== id)
    );
    return { previous };
  },
  onError: (err, id, context) => {
    queryClient.setQueryData(['employees'], context.previous); // Roll back
  },
});
```

**Scenarios to Master**
- [ ] `useInfiniteQuery`: infinite scroll loading
- [ ] `prefetchQuery`: pre-fetch data in Server Components, pass to Client Components
- [ ] `queryClient.setQueryData`: manually update cache (e.g., update detail page data directly after form save)
- [ ] `suspense: true`: use with React Suspense

#### 3.4 Zustand (Client-Side Global State)

Use only for true "client-side global state" — don't use it to manage server data (that's TanStack Query's responsibility).

```tsx
// store/auth.store.ts
interface AuthState {
  user: User | null;
  permissions: string[];
  setUser: (user: User) => void;
  clearUser: () => void;
  hasPermission: (permission: string) => boolean;
}

export const useAuthStore = create<AuthState>()(
  persist(  // Persist to localStorage
    (set, get) => ({
      user: null,
      permissions: [],
      setUser: (user) => set({ user, permissions: user.permissions }),
      clearUser: () => set({ user: null, permissions: [] }),
      hasPermission: (perm) => get().permissions.includes(perm),
    }),
    { name: 'stratos-auth' }
  )
);

// store/ui.store.ts — UI state (sidebar collapse, theme)
export const useUIStore = create<UIState>((set) => ({
  sidebarCollapsed: false,
  toggleSidebar: () => set((s) => ({ sidebarCollapsed: !s.sidebarCollapsed })),
}));
```

**State Management Selection Principles (common interview question)**

```
Server data (API responses)  →  TanStack Query (caching, sync)
Global client state          →  Zustand (user info, UI preferences)
Form state                   →  React Hook Form (local, don't put in global store)
Component-local state        →  useState (prefer this first)
```

#### 3.5 TanStack Table v8 (Enterprise Data Table)

80% of enterprise admin pages are tables — TanStack Table is the industry standard.

```tsx
'use client';
import {
  useReactTable, getCoreRowModel, getSortedRowModel,
  getFilteredRowModel, getPaginationRowModel,
  type ColumnDef, type SortingState,
} from '@tanstack/react-table';

const columns: ColumnDef<Employee>[] = [
  { id: 'select', header: ({ table }) => <Checkbox />, cell: ({ row }) => <Checkbox /> },
  { accessorKey: 'name', header: 'Name', enableSorting: true },
  { accessorKey: 'department.name', header: 'Department' },
  { accessorKey: 'role', header: 'Role', cell: ({ getValue }) => <Badge>{getValue()}</Badge> },
  { accessorKey: 'createdAt', header: 'Start Date', cell: ({ getValue }) => formatDate(getValue()) },
  {
    id: 'actions',
    cell: ({ row }) => (
      <DropdownMenu>
        <DropdownMenuItem onClick={() => router.push(`/employees/${row.original.id}`)}>
          Edit
        </DropdownMenuItem>
        <DropdownMenuItem onClick={() => deleteEmployee(row.original.id)}>
          Delete
        </DropdownMenuItem>
      </DropdownMenu>
    ),
  },
];

export function EmployeeTable({ data, pageCount }: Props) {
  const [sorting, setSorting] = useState<SortingState>([]);
  const [columnVisibility, setColumnVisibility] = useState({});

  const table = useReactTable({
    data,
    columns,
    pageCount,
    manualPagination: true,   // Server-side pagination
    manualSorting: true,      // Server-side sorting
    onSortingChange: setSorting,
    onColumnVisibilityChange: setColumnVisibility,
    getCoreRowModel: getCoreRowModel(),
    getSortedRowModel: getSortedRowModel(),
    state: { sorting, columnVisibility },
  });
  // ...
}
```

**Capabilities to Master**
- [ ] Server-side pagination + sorting (`manualPagination: true`)
- [ ] Global search + column-level filtering
- [ ] Multi-row selection + bulk operations
- [ ] Column visibility control
- [ ] Column pinning

#### 3.6 NextAuth.js v5 (Authentication)

```tsx
// auth.config.ts
export const authConfig: NextAuthConfig = {
  providers: [
    Credentials({
      async authorize(credentials) {
        // Call Spring Boot /api/auth/login
        const res = await fetch(`${process.env.API_URL}/auth/login`, {
          method: 'POST',
          body: JSON.stringify(credentials),
        });
        const user = await res.json();
        return res.ok ? user : null;
      },
    }),
  ],
  callbacks: {
    jwt({ token, user }) {
      if (user) {
        token.accessToken = user.accessToken;
        token.refreshToken = user.refreshToken;
        token.permissions = user.permissions;
      }
      return token;
    },
    session({ session, token }) {
      session.accessToken = token.accessToken;
      session.user.permissions = token.permissions;
      return session;
    },
  },
};
```

**Middleware Route Protection (Next.js level, no component dependency)**
```tsx
// middleware.ts (project root)
export function middleware(request: NextRequest) {
  const { pathname } = request.nextUrl;
  const token = request.cookies.get('next-auth.session-token');

  const isAuthPage = pathname.startsWith('/login');
  const isProtected = pathname.startsWith('/dashboard');  // or use matcher

  if (isProtected && !token) {
    return NextResponse.redirect(new URL('/login', request.url));
  }
  if (isAuthPage && token) {
    return NextResponse.redirect(new URL('/', request.url));
  }
}

export const config = {
  matcher: ['/((?!api|_next/static|_next/image|favicon.ico).*)'],
};
```

#### 3.7 Axios Setup

```tsx
// lib/api/client.ts
const apiClient = axios.create({
  baseURL: process.env.NEXT_PUBLIC_API_URL,
  headers: { 'Content-Type': 'application/json' },
});

// Request interceptor: attach JWT
apiClient.interceptors.request.use(async (config) => {
  const session = await getSession();
  if (session?.accessToken) {
    config.headers.Authorization = `Bearer ${session.accessToken}`;
  }
  return config;
});

// Response interceptor: unified error handling
apiClient.interceptors.response.use(
  (res) => res.data,
  async (error) => {
    if (error.response?.status === 401) {
      await signOut({ callbackUrl: '/login' });
    }
    return Promise.reject(error.response?.data);
  }
);
```

#### 3.8 Permission Control (Component Level)

```tsx
// components/PermissionGuard.tsx
export function PermissionGuard({
  permission,
  children,
  fallback = null,
}: {
  permission: string;
  children: ReactNode;
  fallback?: ReactNode;
}) {
  const hasPermission = useAuthStore((s) => s.hasPermission);
  return hasPermission(permission) ? children : fallback;
}

// Usage
<PermissionGuard permission="employee:delete" fallback={<span>No permission</span>}>
  <Button onClick={handleDelete}>Delete Employee</Button>
</PermissionGuard>
```

#### 3.9 Apollo Client + GraphQL (Connecting to Quarkus)

> REST (Axios) handles CRUD and authentication, Apollo Client handles Dashboard aggregated queries and real-time subscriptions. Both coexist in the project — which to use depends on the API type.

**Installation & Setup**
```bash
npm install @apollo/client graphql
npm install -D @graphql-codegen/cli @graphql-codegen/client-preset
```

**Apollo Client Initialization**
```tsx
// lib/apollo/client.ts
import { ApolloClient, InMemoryCache, createHttpLink, split } from '@apollo/client';
import { setContext } from '@apollo/client/link/context';
import { GraphQLWsLink } from '@apollo/client/link/subscriptions';
import { createClient } from 'graphql-ws';
import { getMainDefinition } from '@apollo/client/utilities';

// HTTP Link (Query / Mutation)
const httpLink = createHttpLink({
  uri: process.env.NEXT_PUBLIC_GRAPHQL_URL, // http://localhost:8081/graphql
});

// Auth Link: attach JWT (analogous to Axios interceptors)
const authLink = setContext(async (_, { headers }) => {
  const session = await getSession();
  return {
    headers: {
      ...headers,
      authorization: session?.accessToken ? `Bearer ${session.accessToken}` : '',
    },
  };
});

// WebSocket Link (Subscriptions only)
const wsLink = new GraphQLWsLink(
  createClient({
    url: process.env.NEXT_PUBLIC_GRAPHQL_WS_URL!, // ws://localhost:8081/graphql
    connectionParams: async () => {
      const session = await getSession();
      return { Authorization: `Bearer ${session?.accessToken}` };
    },
  })
);

// Auto-split: Subscription → WS, Query/Mutation → HTTP
const splitLink = split(
  ({ query }) => {
    const def = getMainDefinition(query);
    return def.kind === 'OperationDefinition' && def.operation === 'subscription';
  },
  wsLink,
  authLink.concat(httpLink)
);

export const apolloClient = new ApolloClient({
  link: splitLink,
  cache: new InMemoryCache(),
});
```

**Provider Injection (requires Client Component in App Router)**
```tsx
// components/providers/ApolloProvider.tsx
'use client';
import { ApolloProvider } from '@apollo/client';
import { apolloClient } from '@/lib/apollo/client';

export function ApolloClientProvider({ children }: { children: React.ReactNode }) {
  return <ApolloProvider client={apolloClient}>{children}</ApolloProvider>;
}

// app/layout.tsx
export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        <SessionProvider>
          <ApolloClientProvider>   {/* ← GraphQL */}
            <QueryClientProvider>  {/* ← TanStack Query (REST) */}
              {children}
            </QueryClientProvider>
          </ApolloClientProvider>
        </SessionProvider>
      </body>
    </html>
  );
}
```

**GraphQL Code Generator — Auto-generate TypeScript Types and Hooks**

This is a key engineering practice: don't write types by hand — auto-generate them from the Schema to keep frontend and backend types in sync.

```yaml
# codegen.ts (project root)
import type { CodegenConfig } from '@graphql-codegen/cli';

const config: CodegenConfig = {
  schema: 'http://localhost:8081/graphql',   // Introspect Schema from the running Quarkus instance
  documents: ['src/**/*.graphql'],            // Scan your .graphql query files
  generates: {
    'src/gql/': {
      preset: 'client',   // Generate type-safe useQuery / useMutation Hooks
    },
  },
};

export default config;
```

```bash
npx graphql-codegen  # generates type files under src/gql/
```

**Writing Query Files (.graphql)**
```graphql
# src/graphql/dashboard.graphql
query GetDashboardStats {
  dashboardStats {
    totalEmployees
    pendingLeaves
    activeProjects
    recentJoins {
      id
      name
      department {
        name
      }
    }
  }
}

# src/graphql/leaves.graphql
mutation UpdateLeaveStatus($id: String!, $status: LeaveStatus!, $comment: String) {
  updateLeaveStatus(id: $id, status: $status, comment: $comment) {
    id
    status
    updatedAt
  }
}

# src/graphql/notifications.graphql
subscription OnNotification($userId: String!) {
  notificationAdded(userId: $userId) {
    id
    message
    type
    createdAt
  }
}
```

**Usage in Components (all types auto-generated, no manual typing)**
```tsx
'use client';
// These Hooks and types are auto-generated by Code Generator
import {
  useGetDashboardStatsQuery,
  useUpdateLeaveStatusMutation,
  useOnNotificationSubscription,
} from '@/gql/graphql';

// Query
function DashboardPage() {
  const { data, loading, error } = useGetDashboardStatsQuery();

  if (loading) return <Skeleton />;
  return (
    <div>
      <StatCard label="Total Employees" value={data?.dashboardStats.totalEmployees} />
      <StatCard label="Pending Leave Requests" value={data?.dashboardStats.pendingLeaves} />
    </div>
  );
}

// Mutation
function LeaveApprovalButton({ leaveId }: { leaveId: string }) {
  const [updateStatus, { loading }] = useUpdateLeaveStatusMutation({
    onCompleted: () => toast.success('Approval complete'),
    // Update cache (analogous to TanStack Query's invalidateQueries)
    refetchQueries: ['GetDashboardStats'],
  });

  return (
    <Button
      loading={loading}
      onClick={() => updateStatus({ variables: { id: leaveId, status: 'APPROVED' } })}
    >
      Approve
    </Button>
  );
}

// Subscription — receive notifications in real time
function NotificationBell({ userId }: { userId: string }) {
  useOnNotificationSubscription({
    variables: { userId },
    onData: ({ data }) => {
      const msg = data.data?.notificationAdded.message;
      if (msg) toast(msg);
    },
  });

  return <BellIcon />;
}
```

**Capabilities to Master**
- [ ] Apollo Client cache mechanism: `InMemoryCache`, `cache.modify()`, `refetchQueries`
- [ ] Optimistic UI: `optimisticResponse` (analogous to TanStack Query's optimistic update)
- [ ] Error handling: `onError` Link for global GraphQL error capture
- [ ] Apollo Client vs TanStack Query: when to use which (common interview question)
- [ ] How GraphQL Code Generator works: Schema introspection → TypeScript types

**Dual Client Selection Reference (interview prep)**

| Scenario | Use | Reason |
|----------|-----|--------|
| Login / Register | Axios + TanStack Query | REST endpoint, straightforward |
| Employee CRUD forms | Axios + TanStack Query | REST, pairs well with React Hook Form |
| Dashboard data aggregation | Apollo Client | One GraphQL query replaces multiple REST requests |
| Leave Approval Mutation | Apollo Client | Backend is a GraphQL Mutation |
| Real-time notifications | Apollo Client useSubscription | WebSocket, GraphQL Subscription |
| Data table (pagination/sorting) | Axios + TanStack Query | REST pagination params are more intuitive |

---

### Phase 4: Containerization (1 week)

#### Docker Compose (Full Configuration)

```yaml
services:
  postgres:
    image: postgres:15
    environment:
      POSTGRES_DB: stratos
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U admin"]
      interval: 10s
      retries: 5

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

  stratos-api:
    build: ./stratos-api
    ports:
      - "8080:8080"
    environment:
      SPRING_DATASOURCE_URL: jdbc:postgresql://postgres:5432/stratos
      SPRING_DATASOURCE_USERNAME: admin
      SPRING_DATASOURCE_PASSWORD: password
      JWT_SECRET: ${JWT_SECRET}
    depends_on:
      postgres:
        condition: service_healthy

  stratos-ops:
    build: ./stratos-ops
    ports:
      - "8081:8081"
    environment:
      QUARKUS_DATASOURCE_JDBC_URL: jdbc:postgresql://postgres:5432/stratos
      MAIL_HOST: ${MAIL_HOST}
    depends_on:
      - stratos-api

  stratos-web:
    build: ./stratos-web
    ports:
      - "3000:3000"
    environment:
      NEXTAUTH_URL: http://localhost:3000
      NEXTAUTH_SECRET: ${NEXTAUTH_SECRET}
      NEXT_PUBLIC_API_URL: http://stratos-api:8080
    depends_on:
      - stratos-api

volumes:
  postgres_data:
```

---

## Project Directory Structure

```
stratos/
│
├── stratos-web/                        ← Next.js 14
│   ├── app/
│   │   ├── (auth)/login/page.tsx
│   │   ├── (dashboard)/
│   │   │   ├── layout.tsx              ← Sidebar layout
│   │   │   ├── page.tsx                ← Dashboard (Apollo Client)
│   │   │   ├── employees/
│   │   │   │   ├── page.tsx            ← Employee list (TanStack Query)
│   │   │   │   ├── [id]/page.tsx       ← Employee detail/edit
│   │   │   │   └── new/page.tsx        ← Create employee (React Hook Form)
│   │   │   ├── departments/
│   │   │   ├── leaves/                 ← Leave approval (Apollo Mutation)
│   │   │   └── settings/
│   │   └── api/auth/[...nextauth]/route.ts
│   ├── components/
│   │   ├── ui/                         ← shadcn/ui components
│   │   ├── layout/                     ← Sidebar, Header, NotificationBell
│   │   ├── employees/                  ← Employee business components
│   │   └── shared/                     ← DataTable, PageHeader, PermissionGuard
│   ├── graphql/                        ← .graphql query files (read by Code Generator)
│   │   ├── dashboard.graphql           ← GetDashboardStats query
│   │   ├── leaves.graphql              ← UpdateLeaveStatus mutation
│   │   └── notifications.graphql      ← OnNotification subscription
│   ├── gql/                            ← Auto-generated (do not edit manually)
│   │   └── graphql.ts                  ← Types + Hooks (generated by Code Generator)
│   ├── hooks/                          ← TanStack Query hooks (REST)
│   ├── lib/
│   │   ├── api/                        ← Axios client + REST API methods
│   │   ├── apollo/
│   │   │   └── client.ts               ← Apollo Client instance (includes WS split link)
│   │   └── utils.ts
│   ├── store/                          ← Zustand stores
│   ├── types/                          ← TypeScript types (REST side)
│   ├── auth.config.ts                  ← NextAuth config
│   ├── codegen.ts                      ← GraphQL Code Generator config
│   ├── middleware.ts                   ← Route protection
│   └── Dockerfile
│
├── stratos-api/                        ← Spring Boot 3 (REST + Auth)
│   └── src/main/java/com/stratos/
│       ├── controller/
│       ├── service/
│       ├── repository/
│       ├── entity/
│       ├── dto/
│       ├── security/
│       ├── mapper/                     ← MapStruct
│       └── exception/
│
├── stratos-ops/                        ← Quarkus 3 (GraphQL layer)
│   └── src/main/java/com/stratos/ops/
│       ├── graphql/                    ← @GraphQLApi resource classes
│       │   ├── DashboardResource.java  ← Aggregated queries
│       │   ├── EmployeeResource.java
│       │   └── LeaveResource.java      ← Mutation (approval)
│       ├── notification/               ← Subscription + email
│       └── report/                     ← Async report generation
│
└── docker-compose.yml
```

---

## Common Interview Topics

### React / Next.js (Key Focus)
- [ ] Virtual DOM and Reconciliation (Fiber architecture)
- [ ] Server Components vs Client Components — rendering timing, capability boundaries, data flow
- [ ] Hooks rules: why you can't call Hooks inside conditionals
- [ ] `useEffect` dependency array pitfalls and closure issues
- [ ] `useMemo` / `useCallback` / `React.memo` — when to use them, when not to over-optimize
- [ ] React 18 new features: Concurrent Mode, `useTransition`, `Suspense`
- [ ] TanStack Query cache mechanism — difference between `staleTime` and `gcTime`
- [ ] State management selection: when to use useState, Zustand, or TanStack Query
- [ ] Next.js App Router — Layout nesting, Route Groups, Middleware execution timing

### Spring Boot / Java
- [ ] Spring Bean lifecycle, scopes (Singleton / Prototype)
- [ ] `@Transactional` internals, transaction propagation levels (REQUIRED / REQUIRES_NEW / NESTED)
- [ ] JPA lazy loading pitfall (LazyInitializationException) and solutions
- [ ] N+1 query problem: how to detect it, how to solve with `@EntityGraph` or JOIN FETCH
- [ ] Spring Security Filter Chain execution order

### Database
- [ ] B+Tree index internals, leftmost prefix rule for composite indexes
- [ ] `EXPLAIN ANALYZE` for slow query analysis
- [ ] Transaction ACID, four isolation levels and the problems each solves

### GraphQL (Strong Bonus Points)
- [ ] GraphQL vs REST — core differences: over-fetching / under-fetching, single endpoint vs multiple endpoints
- [ ] Schema-first vs code-first — differences between the two approaches (Quarkus uses code-first)
- [ ] Query / Mutation / Subscription — three operation types and their use cases
- [ ] How N+1 problem manifests in GraphQL, how DataLoader solves it
- [ ] Apollo Client cache internals — how `InMemoryCache` normalizes cache using `__typename + id`
- [ ] GraphQL Code Generator — why use it, what problem it solves
- [ ] Subscription internals — WebSocket vs SSE, `graphql-ws` protocol
- [ ] GraphQL security — depth limiting, query complexity analysis, disabling introspection (production)
- [ ] When to choose GraphQL vs REST (most commonly asked interview question)

### Spring Boot vs Quarkus (Interview Bonus)
- [ ] Startup time, memory usage, programming model differences
- [ ] Quarkus reactive model (Mutiny) vs Spring WebFlux comparison
- [ ] When to choose Spring Boot vs Quarkus
- [ ] SmallRye GraphQL vs Spring GraphQL — design philosophy differences

---

## Milestones

### M1 — Spring Boot CRUD API + PostgreSQL

> Goal: A runnable REST API with full CRUD, pagination, and Swagger docs — verified end-to-end with Postman

- [ ] Create `stratos-api` repo, generate project via [Spring Initializr](https://start.spring.io) (Web, JPA, PostgreSQL, Validation, Lombok, Swagger)
- [ ] Write `docker-compose.yml` — PostgreSQL 15 + Redis containers
- [ ] Configure `application.yml` with `dev` / `prod` profiles
- [ ] Flyway `V1__init_schema.sql` — create `departments` and `employees` tables
- [ ] `Department` entity + `Employee` entity with `@ManyToOne` association
- [ ] `DepartmentRepository` + `EmployeeRepository` extending `JpaRepository`
- [ ] `EmployeeService` with `@Transactional` — CRUD + paginated list
- [ ] `EmployeeController` — full REST endpoints (GET / POST / PUT / DELETE)
- [ ] Pagination + search + sorting via `Pageable` + `Page<T>`
- [ ] Request / Response DTOs + MapStruct mappers (never expose Entity directly)
- [ ] `ApiResponse<T>` unified response wrapper
- [ ] `@RestControllerAdvice` global exception handler (`ResourceNotFoundException`, `ValidationException`)
- [ ] Swagger / OpenAPI config — all endpoints documented with examples
- [ ] Postman collection — smoke-test all endpoints, verify 200 / 400 / 404 responses

---

### M2 — Spring Security + JWT + RBAC

> Goal: Secured API with login, token refresh, and role-based access control

- [ ] Flyway `V2__add_auth_tables.sql` — `users`, `roles`, `user_roles` tables + seed admin user
- [ ] `User` / `Role` entities + associations
- [ ] `UserDetailsService` — load user + roles from database
- [ ] JWT utility class — generate, validate, parse Access Token (15 min) + Refresh Token (7 days)
- [ ] `JwtAuthFilter` extends `OncePerRequestFilter` — attach `SecurityContext` on every request
- [ ] `SecurityFilterChain` — define public vs protected route rules
- [ ] `POST /api/auth/login` endpoint — returns Access Token + Refresh Token
- [ ] `POST /api/auth/refresh` endpoint — exchanges Refresh Token for new Access Token
- [ ] `@PreAuthorize("hasRole('ADMIN')")` on admin-only endpoints
- [ ] Test full auth flow with Postman (login → use token → refresh → revoke)

---

### M3 — Quarkus Running + GraphQL Playground

> Goal: `stratos-ops` boots, Schema is auto-generated, first query works in Playground

- [ ] Create `stratos-ops` repo via [code.quarkus.io](https://code.quarkus.io) — select SmallRye GraphQL, Hibernate ORM with Panache, JDBC PostgreSQL, SmallRye JWT, Mailer
- [ ] Configure `application.properties` — database URL, JWT settings, Quarkus dev port (8081)
- [ ] Add `stratos-ops` service to `docker-compose.yml`
- [ ] First `@GraphQLApi` resource class with a stub `Query`
- [ ] Define `Employee` GraphQL type (code-first, using `@Type`)
- [ ] Verify GraphQL Playground accessible at `http://localhost:8081/q/graphql-ui`
- [ ] Verify Schema tab shows auto-generated SDL
- [ ] Run a basic `{ employees { id name } }` query successfully in Playground

---

### M4 — GraphQL Queries + Code Generator

> Goal: Dashboard aggregated query works; TypeScript types auto-generated in `stratos-web`

- [ ] Implement `employees(filter, page)` Query with search + pagination
- [ ] Implement `department(id)` Query
- [ ] Implement `dashboard` aggregated Query — returns total employees, pending leaves, recent activity in **one request**
- [ ] Create `stratos-web` repo, scaffold Next.js 14 project (App Router, TypeScript, Tailwind CSS)
- [ ] Install GraphQL Code Generator + config (`codegen.ts`) — introspect Quarkus schema at runtime
- [ ] Run `graphql-codegen` — verify `src/gql/graphql.ts` generated with correct types + hooks
- [ ] Write a sample Apollo Client `useQuery` calling `dashboard` — verify data returns correctly

---

### M5 — GraphQL Mutations + Subscriptions

> Goal: Leave approval via Mutation; real-time notification push via Subscription

- [ ] Implement `submitLeaveRequest(input)` Mutation
- [ ] Implement `approveLeaveRequest(id)` + `rejectLeaveRequest(id)` Mutations
- [ ] Create `Notification` entity + GraphQL type
- [ ] Implement `notificationAdded` Subscription (`@Subscription` with Mutiny `Multi<Notification>`)
- [ ] Configure WebSocket transport in Quarkus (`quarkus-websockets`)
- [ ] Trigger notification event after leave approval (async, non-blocking)
- [ ] Test Subscription in Playground — verify real-time push when leave is approved
- [ ] Regenerate Code Generator types — verify Mutation + Subscription hooks in `graphql.ts`

---

### M6 — Next.js Frontend Scaffold

> Goal: Login works, employee list loads from Spring Boot via Axios + TanStack Query

- [ ] Install and configure dependencies: shadcn/ui, TanStack Query v5, Axios, Zustand, NextAuth.js v5
- [ ] Set up `QueryClientProvider` + `SessionProvider` in root layout
- [ ] Create Axios instance — base URL, attach JWT from session, 401 interceptor (auto-refresh)
- [ ] NextAuth.js config — Credentials provider calling `POST /api/auth/login`
- [ ] Login page — form with email + password, error handling, redirect on success
- [ ] Route middleware — protect all `/dashboard/*` routes, redirect unauthenticated to `/login`
- [ ] Employee list page — `useQuery` fetching paginated employee list
- [ ] DataTable with pagination, search input, sorting columns (TanStack Table v8)
- [ ] Zustand store — persist user session info (role, name) for UI rendering

---

### M7 — Dashboard + Apollo Client

> Goal: Dashboard page fetches all KPI data in a single GraphQL request via Apollo Client

- [ ] Install Apollo Client + `graphql-ws`
- [ ] Configure Apollo split link — HTTP for Query/Mutation, WebSocket for Subscription
- [ ] Dashboard page — `useQuery` calling `dashboard` aggregated query
- [ ] KPI cards: Total Employees, Pending Leave Requests, Departments, Recent Hires
- [ ] Trend chart component (Recharts or similar) — headcount over time
- [ ] Confirm exactly **1 network request** loads all dashboard data (verify in DevTools)

---

### M8 — Complex Employee Form

> Goal: Employee create/edit form with 15+ fields, full validation, avatar upload

- [ ] Install React Hook Form + Zod
- [ ] Define Zod schema — 15+ fields (name, email, phone, department, position, start date, salary, status, emergency contact, etc.)
- [ ] Employee create form — all fields, inline validation error messages
- [ ] Edit mode — pre-populate form with existing employee data
- [ ] Department field as async select (fetch options from API)
- [ ] Avatar upload — file input + preview + upload to backend
- [ ] Form submission — POST / PUT via TanStack Query mutation, optimistic update
- [ ] Toast notification on success / error (shadcn/ui `<Toast>`)
- [ ] Confirm form reset and navigation after successful submission

---

### M9 — Leave Request End-to-End Workflow

> Goal: Full leave lifecycle — submit → approve → real-time notify — works across all three services

- [ ] Leave request form — date picker, leave type, reason field, Zod validation
- [ ] Submit via `POST /api/leaves` (REST → Spring Boot), TanStack Query mutation
- [ ] Manager view — pending leave list with employee info and request details
- [ ] Approve / Reject buttons → Apollo Client Mutation → Quarkus `approveLeaveRequest`
- [ ] Subscription listener active in layout — `useSubscription` on `notificationAdded`
- [ ] Notification bell icon — unread count badge, dropdown with latest notifications
- [ ] Mark notification as read on click
- [ ] Leave calendar view — visualize approved leaves by month

---

### M10 — Docker Compose Full Stack

> Goal: `docker-compose up --build` starts all services from scratch with no manual steps

- [ ] `Dockerfile` for `stratos-api` (multi-stage Maven build)
- [ ] `Dockerfile` for `stratos-ops` (multi-stage Maven build, Quarkus fast-jar)
- [ ] `Dockerfile` for `stratos-web` (multi-stage Node build)
- [ ] Root `docker-compose.yml` — all 5 services: postgres, redis, stratos-api, stratos-ops, stratos-web
- [ ] Health checks for postgres + redis; api/ops wait for DB to be ready (`depends_on: condition: service_healthy`)
- [ ] `.env.example` file — document all required environment variables
- [ ] Cold-start test: `docker-compose down -v && docker-compose up --build` — verify full stack comes up clean
- [ ] Verify cross-service communication (web → api, web → ops, api ↔ db)

---

### M11 — GitHub Polish + Public Release

> Goal: All three repos are public, documented, and runnable by a stranger in under 10 minutes

- [ ] `stratos-api` README — prerequisites, how to run locally, how to run with Docker, Swagger URL
- [ ] `stratos-ops` README — prerequisites, how to run locally, GraphQL Playground URL
- [ ] `stratos-web` README — prerequisites, env vars, how to run, feature screenshots
- [ ] `stratos-roadmap` README — final status update, mark completed milestones ✅
- [ ] Push all three repos to GitHub (public visibility)
- [ ] Final smoke test — clone each repo fresh, follow README, verify it works
- [ ] Add repo links to `stratos-roadmap` README header

---

## References

**React / Next.js**
- [Next.js Official Docs](https://nextjs.org/docs) — focus on App Router section
- [React Official Docs](https://react.dev)
- [TanStack Query Docs](https://tanstack.com/query/latest)
- [TanStack Table Docs](https://tanstack.com/table/latest)
- [Zustand Docs](https://docs.pmnd.rs/zustand)
- [React Hook Form Docs](https://react-hook-form.com)
- [Zod Docs](https://zod.dev)
- [shadcn/ui Docs](https://ui.shadcn.com)

**Spring Boot**
- [Spring Boot Official Docs](https://docs.spring.io/spring-boot/docs/current/reference/html/)
- [Baeldung](https://www.baeldung.com) — best Spring learning resource

**Quarkus + GraphQL**
- [Quarkus Official Docs](https://quarkus.io/guides/)
- [SmallRye GraphQL Guide](https://quarkus.io/guides/smallrye-graphql)
- [Quarkus vs Spring Boot](https://quarkus.io/blog/quarkus-for-spring-developers/)

**GraphQL Clients**
- [Apollo Client Docs](https://www.apollographql.com/docs/react/)
- [GraphQL Code Generator Docs](https://the-guild.dev/graphql/codegen)
- [graphql-ws Docs](https://github.com/enisdenjo/graphql-ws)

**Database**
- [Use The Index, Luke](https://use-the-index-luke.com)
