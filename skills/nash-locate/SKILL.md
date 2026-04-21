---
name: nash-locate
description: Map a feature request to the Nash architecture — which app, module, screens, components, and GraphQL operations are involved. Use after /nash-ingest to understand where to build.
argument-hint: Optional path to spec directory (defaults to latest in specs/)
allowed-tools: Read, Grep, Glob, Agent, Write
---

# Nash Locate — Map Feature to Nash Architecture

You are a Nash frontend architecture expert. Your job is to take the intake document and map the feature to exact locations in the Nash codebase.

## Nash Ecosystem Map

### Apps
| App | Purpose | Tech | Port |
|-----|---------|------|------|
| nash-portal | Merchant dashboard | React 18 + Vite | 3000 |
| nash-provider | Fleet/provider portal | React 18 + Vite | 3001 |
| nash-tracking | Public order tracking | Next.js | - |
| nash-store | Store/commerce portal | React + Vite | - |

### Portal Modules (apps/nash-portal/src/features/)
AI, AgenticCheckout, AsyncActivities, AuditLog, CSVUpload, CreateJob, DeliveryEvents, DeliveryWindows, DriverApp, Fleet, Job, Locations, Login, NotificationCenter, Orders, OrgSettings (20+ sub-modules), OrganizationPicker, Overview, ProductCatalog, Providers, ProvidersManagement, PublicJobTracking, PublicPodUpload, PublicPreChecklist, QuickActions, Returns, SavedViews, Shifts, Shipping, ShopifyOnboarding, Signup, SupportAICalls, Template, ToastOnboarding, ToastSettings, WixSettings, Zones

### Provider Screens (apps/nash-provider/src/screens/)
AI, Analytics, ContractDetails, Contracts, JobDetails, Jobs, Layout, Login, Merchants, Notifications, Opportunities, OpportunityDetails, Payments, Profile, Refunds, RolesManagement, UserManagement

### Shared Packages
- `@usenash/ui` — 46 foundational components (shadcn/ui-based)
- `@usenash/components` — 90+ business components (DataTable, NashMap, TrackingComponents, AddressForm, etc.)
- `@usenash/hooks` — Shared React hooks
- `@usenash/utils` — Utility functions
- `@usenash/graphql-schema` — Generated GraphQL types
- `@usenash/i18n` — Internationalization

### Architecture Patterns
- **Feature-based organization**: Each feature = self-contained directory with components/, hooks/, graphql/, schemas/, utils/
- **GraphQL + Apollo Client**: Server state management
- **Zustand**: Client state (dialog stores via `createDialogStore()`)
- **react-hook-form + Zod**: Form validation
- **Tailwind CSS v4**: Styling with semantic design tokens
- **CVA**: Component variants

## Execution Steps

### Step 1: Read the intake
Read `00-intake.md` from the spec directory. If no path is given, find the latest spec directory.

### Step 2: Explore the codebase
Based on the intake's auto-detected target app:

1. **Identify the target app** and confirm the intake's guess
2. **Search for related modules**: Grep for keywords from the feature request in the features/ or screens/ directory
3. **Find existing screens/routes**: Check the app's router configuration for relevant routes
4. **Find relevant components**: Search `@usenash/ui` and `@usenash/components` for components that match the feature's needs
5. **Find GraphQL operations**: Search for queries/mutations related to the feature's data domain
6. **Find Zustand stores**: Search for stores that manage related state
7. **Check for existing patterns**: Find similar features already implemented that could serve as precedents

### Step 3: Map users, use cases, and flows
Based on the intake and codebase exploration:

1. **Identify all user types** that interact with this feature — categorize as Primary (🔴), Secondary (🟡), or Tertiary (⚪)
2. **Generate up to 10 use cases** in "As a... I want to... So that..." format, prioritized by user type
3. **Map up to 5 user flows** — step-by-step journeys showing what the user clicks/sees/does. Start with the most common flow. Use simple numbered steps: `User clicks X >> System shows Y >> User does Z`
4. **List edge cases** — what can go wrong? Empty states, large datasets, permissions, concurrent edits, network failures, validation errors
5. **Define fallbacks** — what happens when things fail? Map each failure condition to a Nash component/pattern

### Step 4: Produce the location map

## Output Format: `01-location.md`

```markdown
# Feature Location: [Feature Name]

## Target App
**Primary**: [nash-portal / nash-provider / nash-tracking / nash-store]
**Secondary**: [if cross-app, list others]

## Affected Modules

### Modified (existing)
| Module | Path | What changes |
|--------|------|-------------|
| [Name] | `apps/nash-portal/src/features/[Name]/` | [Brief description] |

### New (to create)
| Module | Proposed path | Purpose |
|--------|--------------|---------|
| [Name] | `apps/nash-portal/src/features/[Name]/` | [Brief description] |

## Screens & Routes

### Existing screens affected
| Screen | Route | File | Change type |
|--------|-------|------|-------------|
| [Name] | `/path` | `pages/[file].tsx` | Modify / Extend |

### New screens needed
| Screen | Proposed route | Purpose |
|--------|---------------|---------|
| [Name] | `/proposed-path` | [Description] |

## Relevant Components

### From @usenash/ui (use as-is)
- `<Component>` — [why it's relevant]

### From @usenash/components (use as-is)
- `<Component>` — [why it's relevant]

### New components needed
- `<ProposedComponent>` — [what it does, similar to existing X]

## Data Layer

### Existing GraphQL operations
| Operation | Type | File | Relevant fields |
|-----------|------|------|----------------|
| [Name] | Query/Mutation | `graphql/[file].graphql` | [fields] |

### New GraphQL operations needed
- [Description of what data is needed]

### State Management
| Store | Type | File | What it manages |
|-------|------|------|----------------|
| [Name] | Zustand / Apollo | `store/[file].ts` | [Description] |

## Users & Interactions

### User Types Involved
Identify every user type that interacts with this feature and their role:

| # | User Type | Role | Priority | App |
|---|-----------|------|----------|-----|
| 1 | [e.g., Merchant Admin] | [What they do with this feature] | 🔴 Primary | nash-portal |
| 2 | [e.g., Provider Manager] | [What they do with this feature] | 🟡 Secondary | nash-provider |
| 3 | [e.g., End Customer] | [What they see/experience] | ⚪ Tertiary | nash-tracking |

**Priority key**: 🔴 Primary = main user, designs optimized for them | 🟡 Secondary = important but not primary focus | ⚪ Tertiary = affected indirectly

### Use Cases (up to 10)

| # | Priority | As a... | I want to... | So that... |
|---|----------|---------|-------------|-----------|
| UC1 | 🔴 Primary | [User type] | [Action they want to perform] | [Outcome/value they get] |
| UC2 | 🔴 Primary | [User type] | [Action] | [Outcome] |
| UC3 | 🟡 Secondary | [User type] | [Action] | [Outcome] |
| UC4 | 🟡 Secondary | [User type] | [Action] | [Outcome] |
| UC5 | ⚪ Tertiary | [User type] | [Action] | [Outcome] |
| ... | ... | ... | ... | ... |

### User Flows (max 5)

Describe the primary workflows step-by-step. Focus on the user's perspective, not technical implementation.

#### Flow 1: [Primary flow name] 🔴
The most common/important user journey.
```
1. User navigates to [screen/page]
2. User clicks [element/action]
3. System shows [response/screen]
4. User fills in [data/form]
5. User confirms [action]
6. System [processes/saves/updates]
7. User sees [confirmation/result]
```

#### Flow 2: [Secondary flow name] 🔴
```
1. User [action] >> 2. System [response] >> 3. User [action] >> ...
```

#### Flow 3: [Flow name] 🟡
```
1. >> 2. >> 3. >> ...
```

#### Flow 4: [Flow name] 🟡
```
1. >> 2. >> 3. >> ...
```

#### Flow 5: [Flow name] ⚪
```
1. >> 2. >> 3. >> ...
```

### Edge Cases & Fallbacks

Identify what can go wrong and what happens when it does.

#### Edge Cases
| # | Scenario | Expected behavior | Affected flow |
|---|----------|------------------|---------------|
| E1 | [Empty state — no data exists yet] | [What to show, CTA to create first item] | Flow 1 |
| E2 | [Large dataset — 1000+ items] | [Pagination, virtualization, loading strategy] | Flow 1 |
| E3 | [Concurrent edit — two users editing same item] | [Last-write-wins / optimistic lock / merge] | Flow 2 |
| E4 | [Permission denied — user lacks role] | [Hide feature / show disabled / show error] | All |
| E5 | [Partial data — required fields missing from API] | [Skeleton / fallback values / error boundary] | Flow 1 |
| E6 | [Network failure mid-action] | [Retry / rollback / draft save] | Flow 2 |
| E7 | [Invalid input — form validation fails] | [Inline errors, field highlighting, focus first error] | Flow 2, 4 |
| E8 | [Timeout — slow API response] | [Loading indicator, timeout message, retry button] | All |

#### Fallbacks
| Condition | Fallback behavior | Component/Pattern |
|-----------|-------------------|-------------------|
| Data loading | Skeleton loader matching final layout | `<Skeleton>` from @usenash/ui |
| Empty list | Empty state illustration + CTA | Custom empty state component |
| API error | Error boundary with retry action | Toast + error message |
| Missing permissions | Feature hidden or disabled state | Role-based conditional render |
| Offline / network issue | Cached data if available, error if not | Apollo cache / error boundary |

---

## Impact Assessment

### Blast radius: [Small / Medium / Large]
- **Files touched**: ~[N] files
- **Modules affected**: [N] modules
- **Cross-app**: [Yes/No]
- **Design system impact**: [None / New variant / New component / New token]
- **Schema changes**: [None / Extend existing / New types]

## Precedent Features
Features already in Nash that follow a similar pattern:
- **[Feature Name]** (`features/[path]`) — [Why it's similar, what pattern it uses]

## Architecture Recommendation
[1-2 sentences on the recommended approach: extend existing module, create new module, or cross-module integration]
```

### Step 5: Update `_meta.json`
Set `"locate": "complete"` in the pipeline state.

## After Completion

Tell the user:
- Which app and modules are affected
- Blast radius assessment
- Number of user types, use cases, and flows identified
- Key edge cases that need design decisions
- Key precedent features to reference
- Suggested next step: "Run `/nash-clarify` to generate qualifying questions, or `/nash-pipeline` to continue."
