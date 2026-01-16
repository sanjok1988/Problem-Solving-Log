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
