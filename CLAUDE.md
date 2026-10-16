# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project context

Starter project for the Code with Mosh Claude Code course: a React 19 + Vite 7 expense tracker that **intentionally** ships with a bug, poor UI, and messy code, to be fixed step by step during the course. Don't silently fix unrelated problems while working on a specific task — change only what was asked.

## Commands

```bash
npm install       # install deps
npm run dev       # Vite dev server at http://localhost:5173
npm run build     # production build to dist/
npm run preview   # serve the built dist/
npm run lint      # ESLint (flat config, eslint.config.js)
```

There is no test runner configured.

Environment: this repo lives in WSL. Node is installed via nvm (`~/.nvm`); if `npm` resolves to `/mnt/c/Program Files/nodejs/...` (the Windows install), commands fail with "UNC paths are not supported" — run `source ~/.nvm/nvm.sh` first.

## Architecture

Plain JavaScript (JSX, no TypeScript), no router, no state library, no backend or persistence.

Component tree (`src/main.jsx` mounts `<App />` in `StrictMode`):

```
App                 owns transactions state + categories constant
├── Summary         props: transactions          → derives income / expenses / balance
├── TransactionForm props: categories, onAdd     → local form state, builds new transaction
└── TransactionList props: transactions, categories → local filter state, renders filtered table
```

Data flow:
- `App.jsx` is the single source of truth for `transactions` (seeded with sample data in `useState`). Children never mutate it; `TransactionForm` calls `onAdd(transaction)` and `App` appends with a functional update (`setTransactions(prev => [...prev, t])`).
- Totals (`Summary`) and the filtered list (`TransactionList`) are derived on every render from props — nothing derived is stored in state.
- UI-only state lives in the component that uses it: form inputs in `TransactionForm`, filter selections in `TransactionList`.
- `categories` is a module-level constant in `App.jsx`, passed down as a prop to the form and list.

Transaction shape: `{ id, description, amount, type: "income" | "expense", category, date: "YYYY-MM-DD" }`. `amount` is a **number**; the form input yields a string, so `TransactionForm` converts it with `Number()` before calling `onAdd`. `id` is `Date.now()`.

Conventions: one component per file in `src/`, default export, imported with the `.jsx` extension. Styling is plain CSS — `src/App.css` holds all component styles (class names like `summary-card`, `income-amount`, `expense-amount`), `src/index.css` the globals; extracted components reuse those existing class names rather than having their own stylesheets.

ESLint: `no-unused-vars` ignores names matching `^[A-Z_]`; react-hooks and react-refresh (Vite) rules are enabled.
