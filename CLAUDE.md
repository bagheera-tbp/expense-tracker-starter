# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project context

Starter project for the Code with Mosh Claude Code course: a React 19 + Vite 7 expense tracker that **intentionally** ships with a bug, poor UI, and messy code, to be fixed step by step during the course. Don't silently fix unrelated problems while working on a specific task — change only what was asked.

## Commands

```bash
npm install     in  # install deps
npm run dev       # Vite dev server at http://localhost:5173
npm run build     # production build to dist/
npm run preview   # serve the built dist/
npm run lint      # ESLint (flat config, eslint.config.js)
```

There is no test runner configured.

Environment: this repo lives in WSL. Node is installed via nvm (`~/.nvm`); if `npm` resolves to `/mnt/c/Program Files/nodejs/...` (the Windows install), commands fail with "UNC paths are not supported" — run `source ~/.nvm/nvm.sh` first.

## Architecture

Plain JavaScript (JSX, no TypeScript), no router, no state library, no backend or persistence.

- `src/main.jsx` mounts `<App />` in `StrictMode`.
- `src/App.jsx` is the entire app in one component: seed transaction data, the add-transaction form, summary cards (income / expenses / balance), type/category filters, and the transactions table. All state is local `useState`; totals and the filtered list are derived on every render.
- Styling is plain CSS in `src/App.css` (component) and `src/index.css` (globals), using class names like `summary-card`, `income-amount`, `expense-amount`.

Transaction shape: `{ id, description, amount, type: "income" | "expense", category, date: "YYYY-MM-DD" }`. Note that `amount` is stored as a **string** (both in seed data and from the number input), which matters for any arithmetic on it. Categories are a hard-coded array inside `App`.

ESLint: `no-unused-vars` ignores names matching `^[A-Z_]`; react-hooks and react-refresh (Vite) rules are enabled.
