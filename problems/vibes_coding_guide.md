# Vibes Coding Guide (Laravel + Vue.js) — Separate Playbooks

## 1) Laravel Vibes Coding Playbook

### A. The “10-line spec” (paste this first)
- Goal:
- Users/roles:
- Endpoint/UI entry:
- Request fields + validation:
- Response/redirect:
- DB changes (tables/columns/indexes):
- Auth (policies/gates/guards):
- Side effects (events/jobs/notifications):
- Edge cases:
- Done = (tests + acceptance criteria):

### B. Preferred Laravel structure (keeps vibes from becoming spaghetti)
- **Validation/Auth**: `FormRequest` + `Policy` (or Gates)
- **Business logic**: `App\Actions\...` (or `Services`)
- **HTTP layer**: thin `Controller`
- **Data shaping**: `JsonResource` / `ResourceCollection`
- **Side effects**: `Events` + `Listeners`, `Jobs` (queues), `Notifications`
- **Consistency**: follow your existing naming conventions and folder structure

### C. Vibe loop (fast, safe iteration)
1. Ask model for a **vertical slice**: migration → model/relations → request → action/service → controller → resource → feature test.
2. Run: tests + lint/static analysis.
3. Paste failures/logs back, ask for a **minimal diff** fix.
4. Commit small, repeat.

### D. Prompts that work well (copy/paste)
**1) Vertical slice generator**
> Implement a minimal vertical slice for: [feature].  
> Output: (1) files to add/change, (2) unified diffs per file, (3) brief rationale.  
> Constraints: Laravel [version], DB=[mysql/pgsql], tests=[Pest/PHPUnit], follow existing conventions, avoid breaking changes.

**2) Bugfix from failing test**
> Here is the failing test output + relevant code.  
> Fix with the smallest change. Output unified diff only. Also explain the root cause in 3 bullets.

**3) Maintainer-friendly PR**
> Write a PR description with: summary, motivation, changes, screenshots (if any), testing steps, risk/rollback plan, and checklist.

### E. Laravel quality gates (must-haves for “good vibes”)
- **Tests**: at least 1–2 feature tests per endpoint/flow
- **Authorization**: policy checks are explicit and testable
- **Validation**: all inputs validated in `FormRequest`
- **Transactions**: for multi-write operations
- **N+1 checks**: eager-load relations where needed
- **Idempotency**: for webhook-like endpoints
- **Errors**: consistent API error shape (if API) and user-friendly flash messages (if web)

### F. Vibes coding “Do / Don’t” in Laravel
**Do**
- Keep controllers thin; move logic to action/service.
- Add migrations with indexes/constraints intentionally.
- Use `Resource` classes to avoid leaking internal fields.
- Return diffs + file list so changes are reviewable.

**Don’t**
- Add “helper” functions everywhere.
- Bypass policies “just to make it work.”
- Mix validation, DB writes, and side effects in the controller.

---

## 2) Vue.js Vibes Coding Playbook

### A. The “UI spec card” (paste this first)
- Screen/component name:
- User goal:
- Data source (API endpoint + fields):
- States: loading / empty / error / success
- Interactions: create/edit/delete/filter/sort/paginate
- Validation rules:
- Accessibility notes:
- Done = (unit/e2e checks + acceptance criteria):

### B. Preferred Vue structure (for clean iteration)
- **Components**: presentational components + container/page component
- **Composables**: `useXyz()` for data fetching, caching, form state
- **State management**: Pinia (if used) for cross-page/shared state
- **API client**: centralized `api.ts` with interceptors + typed responses (TS recommended)
- **Forms**: schema-based validation (e.g., Zod/VeeValidate) if your stack supports it

### C. Vibe loop (UI-first, then correctness)
1. Build UI skeleton + states (loading/empty/error) first.
2. Wire API integration (mock first if needed).
3. Add form validation + optimistic UX only after happy path works.
4. Add tests (component tests and/or e2e) for key flows.

### D. Prompts that work well (copy/paste)
**1) Component + composable vertical slice**
> Create a Vue [2/3] component for [feature] with:  
> - a composable `useFeature()` for fetching/mutations  
> - loading/empty/error states  
> - minimal styling consistent with [Tailwind/Bootstrap/your system]  
> Output: file list + unified diffs.

**2) Debugging UI bug**
> Here is the component code and console/network error.  
> Identify likely causes, propose 2 fixes, then implement the safest minimal diff.

**3) UX polish pass**
> Review this component for accessibility, error handling, and edge cases.  
> Return a checklist + suggested diffs (small patches).

### E. Vue quality gates (keep vibes productive)
- **State handling**: explicit loading/error/empty states
- **API errors**: surfaced clearly to user; logged for debugging
- **Typing**: use TypeScript interfaces/types for DTOs if possible
- **Reactivity correctness**: avoid accidental deep mutation issues
- **Accessibility**: labels, focus management, keyboard nav for modals
- **Performance**: debounce search, avoid unnecessary watchers, paginate large lists

### F. Vibes coding “Do / Don’t” in Vue
**Do**
- Extract repeated logic into composables early.
- Keep components small; one responsibility per component.
- Normalize API DTOs in one place (avoid scattered mapping).

**Don’t**
- Mix API calls directly in many components.
- Leave silent failures (always handle errors).
- Overuse global state for local-only concerns.

---

## 3) “Glue” Workflow (Laravel + Vue together)

### A. Contract-first checklist (prevents back-and-forth)
- Define endpoint: method + route + auth
- Define DTO: request + response fields (include error shape)
- Define pagination/filter conventions
- Define validation messages that UI expects

### B. End-to-end vertical slice plan
1. Laravel: migration + endpoint + feature test.
2. Vue: composable + component + mock/happy-path integration.
3. Connect: run through the flow, then add edge cases.
4. Maintain: update docs + PR notes + release notes.

### C. Best practice for GitHub issue maintenance
- Convert meeting minutes into:
  - **Decisions**
  - **Action items** (owner + due date)
  - **Issue drafts** (title, context, acceptance criteria, checklist)
- Keep PRs small: one feature/fix per PR, with screenshots and test steps.

---

## 4) Your default “Vibes Coding” instruction block (paste into any prompt)
- Output **file list + unified diffs**.
- Keep changes minimal and consistent with existing conventions.
- Add/update tests for critical paths.
- Highlight any breaking change or migration risk.
- If unsure, ask up to 3 clarifying questions before coding.


# Turning a Long Prototype into Working Laravel + Vue Components (when you can’t paste all code)

## Goal
Convert an existing “prototype” (long, messy, partially working code) into production-ready Laravel + Vue code **without needing to feed the entire codebase to the model at once**.

---

## Part A — Strategy: Work in Slices, Not in One Prompt

### 1) Freeze scope + define acceptance criteria
Write 5–10 bullets:
- user flow (start → finish)
- roles/permissions
- screens involved
- API endpoints needed
- “done means” (tests passing, UI states, validations, logging)

### 2) Build an “inventory map” of the prototype (no AI required yet)
Create a checklist file (or GitHub issue) with:
- Pages/components touched (Vue)
- Routes/controllers involved (Laravel)
- DB tables/fields used
- External integrations (mail, payments, queue, etc.)
- Known bugs/edge cases

### 3) Break the prototype into vertical slices
Pick slices that can be completed end-to-end:
- Slice examples: “List”, “Create”, “Update”, “Delete”, “Export”, “Approval step”
Each slice should be small enough that you can paste only relevant files/snippets.

---

## Part B — Laravel: Prototype → Working Feature (Steps)

### 1) Create a clean API contract (per slice)
For each slice, write:
- route + method (e.g., `POST /api/widgets`)
- request fields + validation rules
- response shape (success + error)
This becomes your “source of truth” when refactoring.

### 2) Isolate the prototype logic into an Action/Service
Do not refactor everything at once.
- Create `App\Actions\Feature\CreateWidgetAction` (example)
- Copy prototype logic into it “as-is” first, just enough to run
- Then clean it in small commits

### 3) Keep controllers thin
Controller should:
- authorize
- validate via `FormRequest`
- call Action/Service
- return `Resource`

### 4) Introduce FormRequest + Policy early
- `StoreWidgetRequest` / `UpdateWidgetRequest`
- `WidgetPolicy` (or gates)
This removes messy inline validation/auth from prototype code.

### 5) Convert DB assumptions into real migrations
Prototype often assumes columns exist.
- Add migrations for missing columns/indexes/constraints
- Add foreign keys if appropriate
- If data exists, add safe defaults or backfill steps

### 6) Stabilize response formatting with API Resources
- `WidgetResource` ensures consistent output
- Prevents frontend breakage when internals change

### 7) Add 1 feature test per slice (minimum)
Even if the prototype has no tests:
- “Create works”
- “Validation fails”
- “Unauthorized forbidden”
This is your safety net while refactoring.

### 8) Refactor in small passes
Order that usually works:
1. make it pass tests (even ugly)
2. remove duplication
3. extract helpers (only after stable)
4. add transactions/events/jobs if needed

### 9) Handling “too much code to paste”
Use a **progressive context pack** per slice:
- Route definition
- Controller method
- Action/Service class
- Request + Resource
- Relevant model relationships
- Error/log output
Avoid pasting whole models or unrelated files.

---

## Part C — Vue.js: Prototype → Working Components (Steps)

### 1) Identify the screen and split responsibilities
Prototype often mixes everything in one file.
Split into:
- Page container (loads data, owns state)
- Presentational components (table/form/modal)
- Composables (API + state logic)

Example structure:
- `pages/WidgetsPage.vue`
- `components/widgets/WidgetForm.vue`
- `components/widgets/WidgetTable.vue`
- `composables/useWidgets.ts`

### 2) Normalize the API integration
Create/clean a single API module:
- `api/widgets.ts` with functions like:
  - `listWidgets(params)`
  - `createWidget(payload)`
  - `updateWidget(id, payload)`
This reduces “random axios calls everywhere”.

### 3) Add explicit UI states
For each slice, ensure:
- loading state
- empty state
- error state
- success notifications
Prototypes often miss these, causing “it works only in perfect conditions”.

### 4) Make forms real: validation + submission states
- disable submit while saving
- show field errors from backend validation (422)
- keep a single source of truth for form model mapping

### 5) Replace long inline code with composables
Move:
- fetch logic
- debounced search
- pagination state
- mutation handlers
into composables so components stay readable.

### 6) Add minimal tests (or at least deterministic checks)
If you do tests:
- component test for render + submit
- e2e for main flow
If you don’t:
- ensure reproducible manual test steps in PR description.

### 7) Handling “too much code to paste”
Send only:
- the single component you’re converting now
- the API file used by it
- example API responses (JSON)
- the error you’re seeing (console/network)
That’s enough to iterate.

---

## Part D — The “Chunking” Workflow (How to use the model effectively)

### Step 1: Ask for a refactor plan using only summaries
You provide:
- inventory map
- API contract
- slice list
Model returns:
- file-by-file plan + order of operations + risks

### Step 2: Iterate slice-by-slice with diffs
For each slice, you provide:
- current prototype snippet (only relevant functions/files)
- expected behavior
- errors/test failures (if any)
Ask model:
- “Return unified diffs; keep changes minimal.”

### Step 3: Use tests/logs as the “compression format”
Instead of pasting huge code:
- paste failing test output
- paste stack trace + the function involved
- paste request/response samples
This gives the model the highest signal with minimal context.

### Step 4: Keep a running “context header”
Maintain a small note you reuse in every prompt:
- Laravel version
- Vue version
- routing style
- auth method (Sanctum/Passport/session)
- coding conventions
- DTO shapes

---

## Part E — Practical Conversion Order (Recommended)
1. Lock API contract (even if temporary).
2. Laravel: migration + model relations.
3. Laravel: endpoint for Slice 1 + feature test.
4. Vue: Slice 1 UI with mock data → then real API.
5. Repeat for Slice 2..N.
6. Final pass: permissions, edge cases, UX polish, performance, docs.

---

## What I need from you to give an exact step-by-step for your feature
Paste (small, not everything):
1. Feature description + main user flow
2. The prototype entry points:
   - Laravel route + controller method name
   - Vue page/component name
3. One sample API request/response (even if imagined)
4. Your stack: Laravel version, Vue 2/3, Inertia or SPA, auth (Sanctum/session)

Then I’ll outline a concrete slice plan and the exact files/classes/components to create or refactor first.
