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

### Step 3: Produce the location map

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

### Step 4: Update `_meta.json`
Set `"locate": "complete"` in the pipeline state.

## After Completion

Tell the user:
- Which app and modules are affected
- Blast radius assessment
- Key precedent features to reference
- Suggested next step: "Run `/nash-clarify` to generate qualifying questions, or `/nash-pipeline` to continue."
