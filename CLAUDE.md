# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project context

Starter project for a Claude Code course. Per the README, it "intentionally has a bug, poor UI, and messy code — all of which we fix together throughout the course." Treat existing code as a teaching artifact: don't assume current shape is intentional, and expect to be asked to refactor or fix it.

## Commands

```bash
npm run dev      # Vite dev server at http://localhost:5173
npm run build    # Production build to dist/
npm run preview  # Serve the production build locally
npm run lint     # ESLint over all .js/.jsx files
```

No test runner is configured.

## Toolchain requirements

Vite 7 requires **Node 20.19+ or 22.12+**. Node 18 fails at startup with `TypeError: crypto.hash is not a function`. An nvm install is set up on this machine — run `nvm use 20` if a fresh shell drops back to system Node 18.

## Architecture

Single-page React 19 app, Vite-bundled. `src/main.jsx` mounts `<App />`; everything else lives under `src/`. No router, no state library, no backend.

Component layout (all flat in `src/`):

- **`App.jsx`** — owns the `transactions` array (seeded with hardcoded data, no persistence) and the `categories` constant. Exposes `handleAdd(formData)` that stamps `id`/`date` onto the form payload and appends. Composes the three children below.
- **`Summary.jsx`** — receives `transactions`, computes `totalIncome` / `totalExpenses` / `balance` locally, renders the three summary cards.
- **`TransactionForm.jsx`** — owns its own form state (description/amount/type/category). Takes `categories` and `onAdd` props. Coerces `amount` to `Number` before calling `onAdd`, then resets fields.
- **`TransactionList.jsx`** — owns its own filter state (filterType/filterCategory). Takes `transactions` and `categories` props. Does the type+category filtering internally and renders the table.

Data flow is one-way: `App` holds the canonical list, children either read from it (`Summary`, `TransactionList`) or contribute to it via `onAdd` (`TransactionForm`). UI-local concerns (form fields, filters) live inside the component that uses them, not in `App`.

## Invariant: `amount` is numeric

Transaction `amount` is a `Number` everywhere in the `transactions` array — both in the seeded data (`App.jsx`) and in entries added via the form (`TransactionForm.handleSubmit` coerces the string input with `Number(amount)`). The README originally flagged a string-vs-number bug in the totals; that bug is fixed by keeping the data shape numeric throughout. If you add new write paths, preserve the invariant — don't reintroduce string amounts.

## Lint config note

The flat ESLint config in `eslint.config.js` sets `no-unused-vars` with `varsIgnorePattern: '^[A-Z_]'` — unused identifiers starting with a capital letter or underscore won't be flagged. Useful when leaving imports for component constants or marker vars.
