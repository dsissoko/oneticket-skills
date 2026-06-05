---
name: oneticket-scaffold-appshell
description: "Scaffold a new React+Vite app from the AppShell template — bootstrap strategy, file ownership rules, parallel-safe task decomposition, and wiring patterns for FAN-OUT implementation."
version: "1.0.0"
---

# oneticket-scaffold-appshell

## Purpose

AppShell is the canonical React + Vite skeleton and design system reference for all OneTicket application projects. This skill covers the full lifecycle: bootstrapping a new project from AppShell, implementing features in parallel without merge conflicts, and wiring everything together.

Its primary purposes:
1. **Eliminate merge conflicts in FAN-OUT parallel development** — exclusive file ownership: one component = one file, one screen = one file
2. **Enforce quality design by constraint** — design tokens frozen in `tailwind.config.ts` and `globals.css` + Radix UI/shadcn primitives only
3. **Accelerate onboarding** — new projects copy `apps/appshell/app/` and are production-ready in minutes
4. **Define the wiring strategy** — explicit rules for which files are shared, append-only, or integration-task-only

---

## AppShell is the reference source — never the working directory

`apps/appshell/` is the canonical reference. It is **never modified, never touched**, regardless of the current project. It is only read and copied. All implementation work happens exclusively in `apps/<current_project>/`. This separation is absolute.

---

## Bootstrapping — When the scaffold is fresh

When `apps/<current_project>/app/` has just been copied from AppShell, the following files must be **replaced** to adapt the skeleton to the project. This is a standalone step — once done, implementation decomposition happens in the next invocation.

### Files to REPLACE (project-specific content)

| File | What to replace |
|---|---|
| `src/screens/HomeScreen.tsx` | Replace with project home — main entry point for the app |
| `src/screens/AboutScreen.tsx` | Replace with project about content, or remove if not needed |
| `src/screens/DemoScreen.tsx` | Remove or replace — AppShell demo, not a project screen |
| `index.html` | Update `<title>` to project name |
| `package.json` | Update `name` field to project name |

### Files to KEEP (locked foundation — do not modify)

| File | Why locked |
|---|---|
| `src/components/layout/AppLayout.tsx` | Grid layout shell — touches every screen |
| `src/components/layout/Header.tsx` | Navigation — update nav links only in Integration Task |
| `src/components/layout/Footer.tsx` | Footer — stable, no project-specific content |
| `src/components/ErrorBoundary.tsx` | Safety net — never modify |
| `src/components/ThemeToggle.tsx` | Theme system — never modify |
| `src/components/ui/` | shadcn/ui primitives — never modify |
| `src/styles/globals.css` | Design tokens — frozen |
| `tailwind.config.ts` | Design tokens — frozen |

---

## When AppShell is already in place

The foundation is set. AppShell's conventions apply to everything that follows — locked files, component patterns, design tokens. Every task `content` field in the manifest should reference the AppShell foundation so `@dev` knows what they are building on top of.

---

## Screens Convention: One Screen = One File = Exclusive Ownership

- Each route owns one `.tsx` file in `src/screens/`
- No two tasks modify the same screen file
- Screen naming: `<FeatureName>Screen.tsx`
- Route registration in `main.tsx` is centralized — updated only in the Integration Task

---

## Components Convention: shadcn/ui + Single Setup Task

- All UI components derive from **shadcn/ui** (Radix UI primitives)
- Location: `src/components/ui/` — installed once during skeleton setup
- Feature-specific components live in `src/components/`
- **Locked after setup**: `Header.tsx`, `Footer.tsx`, `AppLayout.tsx`, `ThemeToggle.tsx`

---

## Styles Convention: Frozen Design Tokens

- All colors via CSS variables: `className="bg-background text-foreground"` — never `bg-red-500`
- No magic numbers in className — use named tokens from `tailwind.config.ts`
- Mobile-first breakpoints: `sm:`, `md:`, `lg:`, `xl:`

---

## Task Decomposition Strategy

### Task 0: Skeleton Foundation (Sequential — must complete before any parallel task)

```
Task 0: Skeleton
├─ AppShell scaffold already copied to apps/<project>/app/ (done by init-template)
├─ Replace HomeScreen.tsx, AboutScreen.tsx, DemoScreen.tsx with project stubs
├─ Update index.html title + package.json name
├─ Verify npm run dev renders without errors
└─ Output: foundation ready for parallel tasks
```

### Tasks 1–N: Parallel Screen & Feature Tasks

Each task owns exclusive files — zero shared file writes:

**Screen Task Pattern:**
```
Task X: Create FeatureScreen
├─ Depends: Task 0
├─ Creates: src/screens/FeatureScreen.tsx (exclusive file)
├─ Creates: src/hooks/useFeatureData.ts if needed (exclusive file)
├─ Appends: src/mocks/handlers.ts — add a // <FeatureName> handlers block, never modify existing
└─ Creates: tests alongside each file
```

**Hook/Data Task Pattern:**
```
Task X: Data Layer
├─ Depends: Task 0
├─ Creates: src/hooks/useDataLayer.ts (exclusive file)
├─ Appends: src/mocks/handlers.ts (append only)
├─ Appends: src/mocks/data/entities.ts (append only)
└─ Creates: unit tests
```

### Integration Task (Final — Sequential, depends on ALL parallel tasks)

```
Task N+1: Integration & Wiring
├─ Depends: ALL tasks 1–N
├─ Updates: src/main.tsx — lazy imports + route array (ALL screens wired here, nowhere else)
├─ Updates: src/components/index.ts — export ALL new components (barrel file, touch only here)
├─ Updates: src/components/layout/Header.tsx — add nav links for new screens
├─ Updates: src/screens/routing.test.tsx — add route assertions
├─ Runs: npm run build + npm run test
└─ Output: fully integrated, tested, deployable app
```

---

## File Ownership Matrix

| File | Owner | Parallel? | Rule |
|---|---|---|---|
| `src/screens/FeatureScreen.tsx` | Task X | ✅ YES | New file per screen |
| `src/hooks/useFeature.ts` | Task X | ✅ YES | New file per hook |
| `src/components/FeatureComponent.tsx` | Task X | ✅ YES | New file per component |
| `src/main.tsx` | Integration Task | ❌ NO | Touch only in task N+1 |
| `src/components/index.ts` | Integration Task | ❌ NO | Barrel file — touch only in task N+1 |
| `src/components/layout/Header.tsx` | Integration Task | ❌ NO | Nav links — touch only in task N+1 |
| `tsconfig.json` | Integration Task | ❌ NO | Shared config — touch only in task N+1 |
| `vite.config.ts` | Integration Task | ❌ NO | Shared config — touch only in task N+1 |
| `package.json` | Integration Task | ❌ NO | Shared config — touch only in task N+1 |
| `src/mocks/handlers.ts` | Multiple tasks | ⚠️ APPEND ONLY | Each task adds a `// <Feature> handlers` block |
| `src/mocks/data/*.ts` | Multiple tasks | ⚠️ APPEND ONLY | Append new entities only |
| `src/screens/routing.test.tsx` | Integration Task | ❌ NO | Route assertions — task N+1 only |
| `src/components/layout/AppLayout.tsx` | Task 0 | ❌ NO | Locked after setup |
| `src/components/layout/Footer.tsx` | Task 0 | ❌ NO | Locked after setup |
| `tailwind.config.ts` | Task 0 | ❌ NO | Frozen design tokens |
| `src/styles/globals.css` | Task 0 | ❌ NO | Frozen theme variables |

**Critical rule**: `src/main.tsx`, `src/components/index.ts`, `src/components/layout/Header.tsx`, `tsconfig.json`, `vite.config.ts`, and `package.json` are **shared config/wiring files** — written **once**, by the Integration Task, **after all parallel tasks complete**. No parallel task may touch these files.

---

## Example: MonJournal Decomposition (localStorage, no backend)

```
Task 0: Skeleton (sequential)
  ├─ Replace HomeScreen with MonJournal stub
  ├─ Remove DemoScreen
  └─ Verify dev renders

Task A: Data Foundation (depends: Task 0)
  ├─ src/models/thoughtModel.ts
  ├─ src/models/tagModel.ts
  ├─ src/hooks/useThoughts.ts (localStorage)
  └─ src/utils/colorPalette.ts

Task B: Thought List Screen (depends: Task 0, A)
  └─ src/screens/ThoughtListScreen.tsx

Task C: Add Thought Screen (depends: Task 0, A)
  └─ src/screens/AddThoughtScreen.tsx

Task D: Filter Components (depends: Task A)
  ├─ src/components/FilterPanel.tsx
  ├─ src/components/TagMultiSelect.tsx
  └─ src/components/DateRangePicker.tsx

Task E: Display Components (depends: Task A)
  ├─ src/components/ThoughtCard.tsx
  ├─ src/components/ThoughtList.tsx
  └─ src/components/TimelineView.tsx

Task F: Integration (depends: ALL)
  ├─ src/main.tsx — add /thoughts and /add routes
  ├─ src/components/index.ts — export all new components
  ├─ src/components/layout/Header.tsx — add nav link
  └─ npm run build + npm run test
```

Zero merge conflicts — each task owns exclusive files. Integration Task is the **only task** that touches shared wiring files.

---

## Available Tech Stack

| Layer | Library | Version |
|---|---|---|
| Bundler | Vite | ^5 |
| UI Framework | React | ^19 |
| Language | TypeScript | ^5 (strict) |
| Router | React Router DOM | ^6 |
| Styling | Tailwind CSS | ^3 |
| UI Components | Radix UI via shadcn/ui | latest |
| Data Fetching | @tanstack/react-query | ^5 |
| API Mocking | MSW | ^2 |
| Forms | React Hook Form + Zod | ^7 / ^3 |
| Icons | lucide-react | latest |
| Theme | next-themes | ^0.3 |
| Testing | Vitest + Testing Library | ^1 / ^14 |

**Constraints**: React 19 only, Vite only, TypeScript strict, shadcn/ui only, no Redux/MobX.
