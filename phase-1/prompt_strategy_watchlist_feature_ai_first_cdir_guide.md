# Prompt Strategy – Watchlist Feature (AI‑First CDIR Guide)

## Purpose

This document provides a concise, high‑impact prompt strategy for implementing the **Watchlist Feature** in an OTT React Native application using AI‑first development (Cursor / Agent workflow). The strategy follows the **CDIR framework** and is optimized for predictable multi‑file edits with minimal hallucination risk.

---

# CDIR Framework (Core Prompting Model)

## C — Context

Always define:

- tech stack (React Native + TypeScript + Redux Toolkit)
- file paths
- reusable components
- constraints

Example:

Use Redux Toolkit slice pattern from:
`src/store/slices/userSlice.ts`

Reuse component:
`src/components/ContentCard.tsx`

Do not install new libraries.

---

## D — Decompose

Never request the entire feature at once.
Break implementation into small agent‑safe tasks:

1. Create watchlist Redux slice
2. Create watchlist hook
3. Add bookmark indicator icon
4. Add toggle interaction handler
5. Create Watchlist screen
6. Register navigation route

Each task = one prompt.

---

## I — Instruct

Provide strict execution boundaries:

Include:

- file location
- naming conventions
- typing expectations
- reuse rules
- layout constraints

Example instruction block:

Create `watchlistSlice.ts` inside:
`src/store/slices/`

Requirements:

- store string[] contentIds only
- prevent duplicates
- export selector
- export reducer
- no `any` types
- no new dependencies

Return only the slice file.

---

## R — Review

Before accepting output, verify:

Checklist:

- correct folder placement
- no extra files created
- strict typing used
- existing components reused
- navigation unchanged
- no dependency installation

If incorrect → fix prompt (not code).

---

# Watchlist Feature – Implementation Prompt Sequence

## Prompt 1 — Create Redux Slice

Context:
React Native TypeScript project using Redux Toolkit.
Follow pattern from:
`src/store/slices/userSlice.ts`

Task:
Create `watchlistSlice.ts`

Requirements:

- state: string[] contentIds
- actions:
  addToWatchlist(id)
  removeFromWatchlist(id)
- selector: selectWatchlist
- prevent duplicates
- strict typing
- no new libraries

Return only the slice file.

---

## Prompt 2 — Create Watchlist Hook

Create:
`src/hooks/useWatchlist.ts`

Requirements:
Expose:

- isSaved(id)
- toggleWatchlist(id)

Use Redux selector + dispatch
No new dependencies
Strict typing only

Return hook file only.

---

## Prompt 3 — Add Bookmark Indicator

Update:
`src/components/ContentCard.tsx`

Requirements:

- show bookmark icon top‑right
- filled if saved
- outline if not saved
- accept prop: contentId
- do not modify layout
- reuse existing icon system

Return updated component only.

---

## Prompt 4 — Toggle Watchlist Interaction

Update bookmark button handler inside:
`ContentCard.tsx`

Requirements:

- call toggleWatchlist(id)
- no new components
- no UI restructure

Return updated component only.

---

## Prompt 5 — Create Watchlist Screen

Create:
`src/screens/WatchlistScreen.tsx`

Requirements:

- render FlatList
- reuse ContentCard
- read state from selector
- add empty state message
- mock navigation handler allowed

Return screen file only.

---

## Prompt 6 — Navigation Registration

Update:
`src/navigation/MainStack.tsx`

Requirements:

- register WatchlistScreen route
- do not modify existing routes
- follow existing stack pattern

Return updated file only.

---

# Rules to Add in `.cursor/rules`

Rule 1:
Store only `contentId: string[]` inside watchlist state unless backend persistence explicitly requested.

Rule 2:
Reuse `ContentCard.tsx` wherever watchlist items are rendered.

Rule 3:
Do not modify navigation hierarchy beyond adding WatchlistScreen route.

---

# Expected AI Mistakes + Recovery Prompts

## Mistake 1

Agent stores full objects instead of IDs

Recovery Prompt:
Store only `string[] contentIds` inside Redux state. Do not store objects.

---

## Mistake 2

Agent creates new bookmark component unnecessarily

Recovery Prompt:
Update existing `ContentCard.tsx` only. Do not create new components.

