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

Single-page React 19 app, Vite-bundled. The entire application lives in `src/App.jsx` — one component holding:

- All transaction state (seeded with hardcoded data, no persistence)
- Form state for adding transactions
- Filter state (by type and category)
- Derived totals (income, expenses, balance)
- The full render tree (summary cards, add form, filter controls, table)

`src/main.jsx` just mounts `<App />`. There is no router, no component decomposition, no state library, and no backend — adding any of these is a deliberate architectural change, not a given.

## Known issue to be aware of

Transaction `amount` is stored as a string (both in the seeded data and from the `<input type="number">` whose `value` is a string). The totals use `reduce((sum, t) => sum + t.amount, 0)`, which produces string concatenation (e.g. `"0" + "5000" + "1200"…`) rather than numeric addition. This is the "intentional bug" the README alludes to — flag it before silently changing it, since fixing it may be the user's actual task.

## Lint config note

The flat ESLint config in `eslint.config.js` sets `no-unused-vars` with `varsIgnorePattern: '^[A-Z_]'` — unused identifiers starting with a capital letter or underscore won't be flagged. Useful when leaving imports for component constants or marker vars.
