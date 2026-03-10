# CLAUDE.md — ClawFriend Frontend

> Rules apply to the `claw-friend-frontend/` folder. Claude acts as a **Frontend Engineer** — prioritize code quality, UX, and performance.

---

## Tech Stack (quick ref)
- **Framework:** Next.js (App Router, Turbopack dev)
- **Styling:** Tailwind CSS JIT + CSS Variables (design tokens)
- **UI primitives:** Radix UI (Dialog/Modal, Dropdown, etc.)
- **Fonts:** `next/font/google` — Geist (default), Space Mono, JetBrains Mono
- **State:** React Query (server state), React hooks (local)
- **Package manager:** `pnpm`
- **Branch convention:** `feat/`, `fix/`, `sophie-uiux`, etc. — do not commit directly to `main`

---

## ⚙️ Dev Workflow — 6 REQUIRED Steps

### Step 1 — Brainstorm
- Analyze requirements, define scope
- List possible approaches (at least 2 options if task is non-trivial)
- Read context if needed: `_context/tech-stack.md`, `_context/current-sprint.md`, `_memory/ERRORS.md`

### Step 2 — Impact Analysis ⚠️
Before coding, check the impact scope:

| Change | Must check |
|--------|------------|
| Base UI component (`src/components/ui/`) | Grep all usages, report number of affected files |
| `tailwind.config.js` (fontSize, fontFamily, colors) | Affects entire UI — ask for confirmation |
| `src/app/layout.tsx` | Affects all pages |
| `src/styles/globals.scss` | Global — confirm first |
| Design token / CSS variable | Trace all usages of that token |
| Shared hook / util | Grep usages first |

**Rules:**
- If changing a base/shared component → grep all usages, report file count before proceeding
- If scope is unclear → **ASK USER first**, do not guess
- If only changing one specific component → can proceed directly

### Step 3 — Concrete Plan
List exactly:
- Files to change
- Each specific change per file
- Reason for choosing this approach

### Step 4 — Implement
- Only code **after user approves plan** (for complex tasks or shared code changes)
- For small, clear tasks → can proceed immediately
- Use `Edit` instead of `Write` when modifying existing files

### Step 5 — Review & Verify
After coding:
1. Check preview server is running (`preview_list`)
2. Reload page if needed (`preview_eval: window.location.reload()`)
3. Check console/server logs (`preview_console_logs`, `preview_logs`)
4. Verify DOM/CSS with `preview_inspect` or `preview_eval`
5. Screenshot with `preview_screenshot` to confirm visuals
6. Check related pages for regressions

### Step 6 — Session Log
At end of session or after large task:
- Use skill `/session-log` to automate
- Or manually: update `docs/WORKFLOW-LOG.md` and `docs/DECISION-LOG.md`
- Record lessons learned if new errors/quirks found

---

## ⚠️ Known Quirks — READ BEFORE CODING

### Tailwind JIT — Arbitrary values
- **Issue:** New classes using arbitrary values (`ml-[4px]`, `max-w-[576px]`, `underline-offset-2`) may NOT be generated in dev HMR
- **Fix:** Use standard Tailwind utilities (`ml-1`, `px-3`) or inline `style={}` prop
- **Known example:** `ml-[4px]` → `ml-1` | `maxWidth: '576px'` via `style` prop

### Turbopack Browser Cache
- **Issue:** After server restart, browser may still serve old JS chunks from memory cache
- **Not affected:** Production builds, user fresh load
- **Debug:** Use `fetch('/_next/static/chunks/...', { cache: 'no-store' })` to verify server content
- **Fix when stuck:** Delete `.next` (`rm -rf .next`) then restart server

### Modal Width
- **Correct pattern:** `<ModalContent className="w-full" style={{ maxWidth: '576px' }}>` — do NOT use `max-w-[576px]` (Tailwind JIT issue)

### Preview Server
- Server ID changes on every start — always use the latest ID from `preview_list`
- Server uses `.env.local` — copy content from `.env.dev` to `.env.local` if needed

### Font System
- Default font: **Geist** (variable: `--font-geist`)
- Monospace: **Space Mono** (`font-spaceMono`) and **JetBrains Mono** (`font-jetBrainsMono`)
- `font-outfit` class in Tailwind = alias for Geist (backward compat)

---

## Conventions

### Naming
- Component: `PascalCase`
- File: `kebab-case`
- Hook: `useXxx.ts`

### Component structure
```
src/
  app/           # Next.js App Router pages
  features/      # Feature-based modules (home, profile, feeds...)
  components/
    ui/           # Base UI primitives (button, modal, dropdown...)
    icons/        # SVG icons
  hooks/         # Shared hooks
  services/      # API calls
  utils/         # Utilities
```

### Typography scale (tailwind classes)
| Class | Size | Weight | Line height |
|-------|------|--------|-------------|
| `text-heading-sm` | 20px | 500 | 28px |
| `text-label-xs` | 12px | 500 | 16px |
| `text-body-sm` | 13px | 400 | 20px |
| `text-body-xs` | 12px | 400 | 16px |

### Modal title
- Always use `text-heading-sm` (20px/500) for `ModalTitle` — defined in `src/components/ui/modal.tsx`

---

## Output rules
| Output type | Save to |
|-------------|---------|
| Code review notes | `reviews/` |
| Technical documentation | `docs/` |
| New task brief | `tasks/` |
| Workflow/decision log | `docs/WORKFLOW-LOG.md`, `docs/DECISION-LOG.md` |
| Errors encountered | `_memory/ERRORS.md` |

## After completing a task
- Update `_context/current-sprint.md`
- Write to `_memory/TASK-HISTORY.md`
- If new errors encountered → write to `_memory/ERRORS.md`
