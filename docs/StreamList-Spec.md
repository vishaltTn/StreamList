
# StreamList — Project Specification
### AI-First Development Training Program

---

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [What You Are Being Assessed On](#2-what-you-are-being-assessed-on)
3. [Technical Stack](#3-technical-stack)
4. [Environment Setup](#4-environment-setup)
5. [Project Structure](#5-project-structure)
6. [Design System](#6-design-system)
7. [Screen Specifications](#7-screen-specifications)
8. [API Reference](#8-api-reference)
9. [Technical Requirements](#9-technical-requirements)
10. [Submission Requirements](#10-submission-requirements)
11. [Assessment Rubric](#11-assessment-rubric)

---

## 1. Project Overview

### What You Are Building

**StreamList** is a React Native OTT content discovery and watchlist app. It allows users to browse trending and top-rated movies, search for titles, view detailed content information, and manage a personal watchlist.

This is not a full streaming app. There is no video playback. The product is the **browsing, discovery, and curation layer** — the experience before someone presses play.

### Why This Project

This project is intentionally designed to exercise every core skill of AI-first development:

- **Scaffolding from a spec** — you will direct AI to create the project structure, not accept defaults
- **Architecture decisions** — the stack is prescribed; how you prompt AI to implement it is not
- **Multi-layered state** — local persistence, remote data, UI state, and navigation state all intersect
- **Real API integration** — TMDB returns real data with real edge cases AI will not anticipate for you
- **Judgment over completion** — a submission that handles edge cases thoughtfully scores higher than one that builds more features carelessly

### The Minimum Bar vs. What Good Looks Like

> The spec defines the minimum bar. Developers who only hit the spec hit the minimum score. What separates a good submission from an excellent one is evidence of **judgment** — catching edge cases that weren't mentioned, handling states that weren't specified, making an architectural decision and defending it in your ADR. You are not being assessed on whether you followed instructions. You are being assessed on whether you **thought**.

Examples of going beyond the spec (not a checklist — use these to calibrate your thinking):

- TMDB returns `null` for `poster_path` on some titles. Does your app crash or handle it?
- What does your Watchlist screen show when the user has zero items? Is it designed or just empty?
- What if a search returns zero results? What if a genre row returns no content?
- What if the user taps "Add to Watchlist" three times rapidly? Does your store prevent duplicates?
- What does your app look like during the first 500ms before any data loads?
- What happens on the Detail screen if the cast API call fails but the movie details succeed?

None of these are in the spec. A developer with genuine AI-first judgment will surface at least some of them through deliberate prompting and careful review of AI output.

---

## 2. What You Are Being Assessed On

There are **four dimensions** of assessment, weighted equally:

| Dimension | What It Measures |
|---|---|
| **Architecture & Structure** | Did you scaffold a clean, consistent project? Does the folder structure, naming, and code organisation reflect deliberate decisions? |
| **Feature Completeness** | Does the app meet the screen specifications? Do interactions work correctly? Is the UI faithful to the designs? |
| **AI-First Process** | Does your prompt log demonstrate intelligent decomposition, iteration, and recovery from bad output? |
| **Judgment & Edge Cases** | Did you go beyond the spec? Did you handle states and conditions that weren't explicitly described? |

The prompt log and ADR are **half the assessment**. A beautifully built app with a thin prompt log will score lower than a slightly rougher app with a prompt log that shows real thinking.

---

## 3. Technical Stack

The following decisions are **prescribed**. Do not deviate. Part of the assessment is directing AI to implement your architecture, not whatever architecture AI prefers.

| Concern | Decision | Why Prescribed |
|---|---|---|
| Language | TypeScript — strict mode | Tests AI's handling of typed interfaces |
| Navigation | React Navigation v6 — Bottom Tab + Stack | Standard mobile pattern; AI handles it inconsistently without precise prompting |
| State Management | Zustand | Lightweight, slice-based; AI tends to over-engineer with Redux unless constrained |
| API Client | Axios with a central client file | Tests whether developers can get AI to respect a shared abstraction |
| Watchlist Persistence | AsyncStorage | Local only; no backend required |
| Styling | `StyleSheet.create()` only | No third-party UI libraries; tests whether AI respects styling constraints |
| Fonts | Manrope + Inter (via react-native-google-fonts) | Matches the design system |
| Data Layer | Custom hooks per screen | AI defaults to inline fetching; this tests prompt precision |

**Strictly prohibited:**
- NativeWind, Styled Components, or any CSS-in-JS library
- Redux, MobX, or any state library other than Zustand
- Pre-built UI component libraries (no React Native Paper, Elements, etc.)
- Any navigation library other than React Navigation v6
- Hardcoded API keys anywhere in source files

---

## 4. Environment Setup

### 4.1 TMDB API Key

Each developer must create their own free TMDB account and generate their own API key.

1. Go to [https://www.themoviedb.org/signup](https://www.themoviedb.org/signup) and create a free account
2. Navigate to **Settings → API** and request a Developer API key
3. Copy your **API Read Access Token** (the long Bearer token, not the short API key)

### 4.2 Environment Variables

Create a `.env` file in the project root. **This file must be in `.gitignore` and must never be committed.**

```
TMDB_BASE_URL=https://api.themoviedb.org/3
TMDB_IMAGE_BASE_URL=https://image.tmdb.org/t/p
TMDB_ACCESS_TOKEN=your_bearer_token_here
```

Use `react-native-dotenv` (Babel plugin) to access these. Your central API client must read the token from environment variables — not from a hardcoded string.

> **Prompt challenge:** Direct AI to set up environment variable handling correctly, including the `.gitignore` entry and TypeScript type declarations for the env module. This is a common setup step AI gets partially wrong.

### 4.3 React Native CLI

Use **React Native CLI** (not Expo). Initialise with `npx @react-native-community/cli init StreamList --template react-native-template-typescript`. The project must run on both iOS and Android simulators.

---

## 5. Project Structure

You must direct AI to scaffold this exact folder structure. It will not generate this by default — you need to prompt it precisely.

```
streamlist/
├── src/
│   ├── api/
│   │   ├── client.ts          # Central Axios instance with interceptors
│   │   ├── movies.ts          # All movie/show endpoint functions
│   │   └── types.ts           # TypeScript interfaces for all API responses
│   ├── components/
│   │   ├── common/            # Shared across screens (ContentCard, Badge, SkeletonCard, etc.)
│   │   └── [screen-name]/     # Screen-specific components
│   ├── hooks/
│   │   ├── useHome.ts
│   │   ├── useSearch.ts
│   │   └── useMovieDetail.ts
│   ├── navigation/
│   │   ├── RootNavigator.tsx  # Bottom tab navigator
│   │   └── types.ts           # Navigation param types
│   ├── screens/
│   │   ├── HomeScreen.tsx
│   │   ├── SearchScreen.tsx
│   │   ├── DetailScreen.tsx
│   │   ├── WatchlistScreen.tsx
│   │   └── ProfileScreen.tsx  # Placeholder only
│   ├── store/
│   │   └── watchlistStore.ts  # Zustand store with AsyncStorage persistence
│   ├── theme/
│   │   ├── colors.ts          # All color tokens
│   │   ├── typography.ts      # Font sizes, weights, line heights
│   │   └── spacing.ts         # Spacing scale
│   └── utils/
│       └── image.ts           # TMDB image URL helpers
├── .env                       # Never committed
├── .env.example               # Committed — placeholder values only
├── .gitignore
├── .cursor/rules              # Your cursor rules — committed
├── prompt-logs/               # SpecStory exports — committed
│   └── .gitkeep
├── docs/
│   └── ADR.md                 # Architectural Decisions Record
├── app.json
├── tsconfig.json              # Strict mode enabled
└── package.json
```

### Structure Rules

Enforce these in your `.cursor/rules` — AI will violate them without explicit constraints:

- Every screen fetches data exclusively through a custom hook. No inline `useEffect` + `fetch` in screen components.
- All API calls go through `src/api/client.ts`. No direct Axios imports in hooks or components.
- All colour values come from `src/theme/colors.ts`. No hardcoded hex strings in component files.
- All spacing values come from `src/theme/spacing.ts`. No hardcoded pixel values in StyleSheet.
- Navigation types must be defined in `src/navigation/types.ts` and used everywhere.

---

## 6. Design System

The app follows **"The Cinematic Curator"** design language. Study this before prompting any UI work — these rules govern every component.

### 6.1 Colour Tokens

Define all of these in `src/theme/colors.ts`:

```typescript
export const colors = {
  // Surfaces — layered from deepest to brightest
  surface:                   '#131313',  // Main app background
  surface_container_lowest:  '#0E0E0E',  // Deepest recessed elements
  surface_container_low:     '#1C1B1B',  // Section groupings
  surface_container:         '#232323',  // Mid-level containers
  surface_container_high:    '#2A2A2A',  // Elevated sections
  surface_container_highest: '#353534',  // Cards, interactive elements
  surface_bright:            '#3A3939',  // Hover/pressed states

  // Primary accent (Coral-Red)
  primary:                   '#FFB3AE',  // Gradient start / light tint
  primary_container:         '#FF5351',  // Gradient end / strong accent
  secondary_container:       '#822625',  // Active chip state (deep red)

  // Text
  on_surface:                '#E5E2E1',  // Primary text — never use pure white
  on_surface_variant:        '#E4BDBA',  // Secondary metadata text

  // Utility
  outline_variant:           'rgba(255,255,255,0.15)', // Ghost borders (accessibility only)
};
```

**The No-Line Rule:** Never use solid 1px borders to define sections or containers. All boundaries are created through background colour shifts or spacing alone. This is a hard rule — put it in your `.cursor/rules` explicitly.

### 6.2 Typography

Define in `src/theme/typography.ts`. Use **Manrope** for display and headlines, **Inter** for body and labels.

| Token | Font | Size | Weight | Letter Spacing | Usage |
|---|---|---|---|---|---|
| `display-lg` | Manrope | 56px | 800 | -0.02em | Hero titles |
| `display-md` | Manrope | 40px | 800 | -0.02em | Screen titles (e.g. "My Watchlist") |
| `headline-md` | Manrope | 28px | 700 | -0.01em | Section headers ("Trending Now") |
| `title-lg` | Manrope | 20px | 600 | 0 | Card titles |
| `title-sm` | Inter | 14px | 600 | 0 | Buttons, labels |
| `body-md` | Inter | 14px | 400 | 0 | Synopsis, body copy |
| `label-sm` | Inter | 12px | 400 | 0 | Metadata (year, genre, rating) |

### 6.3 Primary Gradient

Used on all primary CTA buttons:

```typescript
// Use react-native-linear-gradient
colors: ['#FFB3AE', '#FF5351']
start: { x: 0, y: 0 }
end: { x: 1, y: 1 }
```

### 6.4 Glassmorphism — Tab Bar

```typescript
backgroundColor: 'rgba(35, 35, 35, 0.70)'
// BlurView from @react-native-community/blur, blurAmount: 20
```

### 6.5 Content Card — Standard Portrait

The core repeating unit used in carousels, grids, and watchlist:

- Aspect ratio: 2:3 (portrait)
- Border radius: 16px on outer container, 12px on internal elements
- No explicit border — use background colour to define edges
- Title: `title-lg`, `on_surface`, below the image
- Subtitle (year • genre): `label-sm`, `on_surface_variant`
- Rating badge (when shown): top-right corner overlay, `surface_container_highest` background, `primary_container` star icon, `label-sm` value

### 6.6 Skeleton Loading

Every async section must have a skeleton state — not a spinner:

- Skeleton blocks: `surface_container_high` (#2A2A2A) background
- Shimmer animation: opacity or gradient sweep from `surface_container_high` to `surface_bright` (#3A3939), looping at ~1.5s
- Skeleton shapes must match the content they represent — circular for cast avatars, portrait rectangle for cards, wide rectangle for title text

### 6.7 Design Rules Summary

**DO:**
- Use `surface_bright` (#3A3939) for pressed/active states on dark cards
- Allow asymmetry — the hero card on Home occupies significantly more width than regular cards
- Use spacing tokens to signal section boundaries, not borders or dividers
- Use `on_surface_variant` for all secondary metadata

**DO NOT:**
- Use pure white (#FFFFFF) for any text
- Use a 1px solid border at full opacity anywhere
- Use dividers between list or carousel items
- Hardcode any hex colour outside `src/theme/colors.ts`

---

## 7. Screen Specifications

---

### 7.1 Home Screen

**Reference Design:** [docs/design/screens/home.png](./docs/design/screens/home.png)

#### Layout

**Header**
- Left: StreamList flame icon + wordmark in `primary_container`
- Right: Notification bell icon (inactive placeholder)
- Background: transparent over scroll, `surface` when at rest

**Genre Filter Strip**
- Horizontally scrollable, no scroll indicator
- Chips: All, Action, Drama, Comedy, Sci-Fi, Horror, Documentary
- Active chip: `secondary_container` (#822625) background, `on_surface` text, `title-sm`
- Inactive chip: `surface_container_high` background, `on_surface_variant` text
- No borders on chips

**Hero Featured Card**
- Large card (~90% screen width), rounded 16px
- Full backdrop image with gradient overlay (bottom 40% fades to `surface`)
- "NEW RELEASE" badge: coral-red pill, `label-sm` uppercase
- Title: `display-md` Manrope, overlapping the card bottom edge
- Synopsis: `body-md`, `on_surface_variant`, truncated to 2 lines
- Two buttons side by side: "Watch Now" (primary gradient, play icon) + "Details" (`surface_container_highest` background)

**Content Rows**
- Each row: section title (`headline-md`) left + "See All" right (`primary_container` colour, `title-sm`)
- Horizontally scrollable, no scroll indicator
- Row 1: "Trending Now"
- Row 2: "Top Rated"
- Row 3: Name of selected genre (e.g. "Action") — updates when chip changes

**Infinite Scroll Indicator**
- Shown at the end of the last visible row when more pages are available
- "LOADING MORE CONTENT" in `on_surface_variant` `label-sm` uppercase + spinner

**Bottom Tab Bar**
- Tabs: Home (active), Search, Watchlist, Profile
- Active icon: `primary_container`
- Inactive: `on_surface_variant`
- Glassmorphism background

#### Data

- Hero: first item from `/trending/movie/week`
- Trending row: `/trending/movie/week` paginated
- Top Rated row: `/movie/top_rated` paginated
- Genre row: `/discover/movie?with_genres={id}` — genre ID from selected chip
- Genre chip list: `/genre/movie/list`

#### Interactions

- Tapping a genre chip updates the third row. Component state only — not Zustand.
- Tapping any card navigates to DetailScreen with movie ID
- Tapping "See All" navigates to a simple full list screen (functional, not heavily designed)
- Tapping "Details" on hero navigates to DetailScreen
- Scrolling to within 3 items of the end of a row loads the next page for that row
- Each row manages its own pagination independently

#### Minimum Bar
All three rows populated, genre filter functional, navigation to Detail working.

#### What Good Looks Like
Infinite scroll per row. Each row has independent loading skeleton on first render. Genre row updates correctly on chip change. Empty state if genre returns no results. Hero card skeleton before data loads.

---

### 7.2 Search Screen

**Reference Designs:** [search_default.png](./docs/design/screens/search_default.png) · [search_results.png](./docs/design/screens/search_results.png)

#### State 1 — Default (No Query)

**Header:** StreamList wordmark + circular user avatar (top right, placeholder icon)

**Search Bar**
- `surface_container_low` background, 12px radius
- Magnifier icon left, placeholder: "Search movies, actors, directors..."
- Focused state: subtle `outline_variant` ghost border at 15% opacity

**Genre Quick Filter Chips**
- Horizontally scrollable below the search bar
- Same chip style as Home. Tapping pre-populates the search bar and immediately triggers a search.

**Recent Searches**
- Section title "Recent Searches" (`headline-md`) + "CLEAR ALL" right-aligned (`primary_container`, `title-sm` uppercase)
- Up to 5 entries. Each: clock icon + search term (`body-md`, `on_surface`)
- Stored and retrieved from AsyncStorage
- Shown only when search bar is empty / not focused

**Trending Now Section**
- One large featured landscape card with "FEATURED" badge (`secondary_container`), title (`title-lg`), genre • year • runtime in `on_surface_variant`
- 2-column portrait grid below for additional trending titles, each showing rating badge

#### State 2 — Results

- Search bar with active query text
- Result count: `{n} results for '{query}'` in `on_surface_variant` `label-sm`
- 2-column portrait grid: poster, title (`title-lg`), year (`label-sm`, `on_surface_variant`), rating badge top-right
- Zero results state: centred icon, "No results for '{query}'" (`headline-md`), suggestion copy (`body-md`, `on_surface_variant`)

#### Interactions

**Debounced search:** API call fires 400ms after the user stops typing. Implement with `useRef` + `clearTimeout`. Do not fire on every keystroke.

**Request cancellation (required, not stretch):** If a new keystroke arrives before the previous call resolves, cancel the outstanding request using Axios `CancelToken` or `AbortController`. This prevents stale results from overwriting newer ones.

- Tapping a result navigates to DetailScreen
- Recent searches saved on each completed search (not on each keystroke)
- "Clear All" removes all recent searches from AsyncStorage and clears the list immediately

#### Minimum Bar
Search functional with results in 2-column grid. Navigation to Detail working.

#### What Good Looks Like
Debounce correctly implemented. Request cancellation demonstrably working (verify by rapid typing). Recent searches persisted across app restarts. Genre chips pre-populate and trigger correctly. Zero-results state handled.

---

### 7.3 Detail Screen

**Reference Design:** [docs/design/screens/detail.png](./docs/design/screens/detail.png)

#### Layout

**Navigation**
- No bottom tab bar (stack screen pushed over tabs)
- Back arrow top-left, share icon top-right (placeholder — no action required)

**Hero Backdrop**
- Full-width image, ~220px tall
- Bottom 40% gradient fade to `surface` (#131313) so title flows naturally below

**Title**
- `display-md` Manrope, `on_surface`, below the hero image

**Metadata Chips Row**
- Non-scrollable horizontal row
- Chips: Year | ★ Rating | Genre | Runtime
- `surface_container_highest` background, `on_surface_variant` text, `label-sm`
- Omit any chip whose value is null or zero (see edge cases below)

**Watchlist Button**
- Default: full-width, primary gradient (`#FFB3AE` → `#FF5351`), bookmark-plus icon, "Add to Watchlist", `title-sm` Inter Semi-Bold
- Added: full-width, `surface_container_highest` background, `outline_variant` border at 15% opacity, `primary_container` bookmark icon, "In Watchlist"
- Toggle is **immediate and optimistic** — update UI before AsyncStorage confirms

**Synopsis**
- "Synopsis" label (`headline-md`)
- Body text `body-md`, `on_surface`, truncated to 3 lines
- "Read more" / "Show less" toggle in `primary_container` colour

**Cast Section**
- "Cast" label (`headline-md`)
- Horizontal scroll of circular avatar cards: 60px diameter image, actor name `label-sm` `on_surface`, character name `label-sm` `on_surface_variant`

**More Like This Section**
- "More Like This" (`headline-md`) + "See All" right-aligned
- Horizontal scroll of standard portrait content cards

#### Data — Three Parallel Calls (Required)

All three must be initiated simultaneously using `Promise.allSettled`. Sequential chaining is incorrect and will be flagged in assessment.

| Call | Endpoint | Drives |
|---|---|---|
| Movie details | `/movie/{id}` | Title, backdrop, metadata chips, synopsis |
| Credits | `/movie/{id}/credits` | Cast section |
| Similar titles | `/movie/{id}/similar` | More Like This section |

Each section must have its own independent loading skeleton and error state. A failed cast call must not affect the details section.

#### Edge Cases — All Required

| Condition | Required Handling |
|---|---|
| `poster_path` or `backdrop_path` is `null` | Show `surface_container_high` placeholder with centred StreamList icon |
| `runtime` is `null` or `0` | Omit the runtime chip entirely |
| `vote_average` is `0` | Omit the rating chip entirely |
| Credits array is empty | Show "Cast information unavailable" in `on_surface_variant` — do not show an empty section header |
| Similar movies array is empty | Hide the "More Like This" section including its header |
| Individual API call fails | Show a section-level error state with a retry button for that section only |

#### Minimum Bar
All three data sections displaying. Watchlist toggle functional and persisted. Back navigation working.

#### What Good Looks Like
`Promise.allSettled` correctly used. Each section has independent skeleton and error state. All edge cases above handled. Watchlist button state correct on re-entry (add to watchlist, go back, return — button shows "In Watchlist"). Read more/less toggle working.

---

### 7.4 Watchlist Screen

**Reference Designs:** [watchlist.png](./docs/design/screens/watchlist.png) · [watchlist_empty.png](./docs/design/screens/watchlist_empty.png)

#### State 1 — Populated

**Header**
- "YOUR COLLECTION" label: `label-sm`, `on_surface_variant`, uppercase, tracked
- "My Watchlist" title: `display-md` Manrope
- Search icon + circular user avatar top-right

**Filter Chips**
- All | Movies | Series
- Active: `secondary_container` background
- Filters the displayed list client-side — no re-fetch
- "All" shows everything; "Movies" shows only movies; "Series" shows only TV shows

**Content Grid**
- 2-column grid of content cards
- Each card:
  - Portrait poster image (2:3 ratio), 16px radius
  - Rating badge: top-right corner, `surface_container_highest` bg, `primary_container` star, `label-sm` value
  - "×" remove icon: top-right of card area (below badge or overlaid)
  - Title: `title-lg`, `on_surface`, truncated with ellipsis
  - Year • Genre(s): `label-sm`, `on_surface_variant`
  - "Details" button: full-width, `surface_container_highest` bg, `on_surface` text, `title-sm`, 8px radius

**"Because you saved [Title]" Row**
- Appears only when watchlist has at least one item
- Based on the most recently added item
- "See All" right-aligned in `primary_container`
- Horizontal scroll of portrait content cards, sourced from `/movie/{id}/similar` for that title
- If Similar returns empty, hide this section

**Empty State Within a Filter**
- If the user selects "Series" but has only saved movies, show a contextual empty message within the filtered view: "No [Series] in your watchlist yet" with a "Browse All" chip that resets to "All" filter.

#### State 2 — Empty (Zero Items in Watchlist)

- "YOUR COLLECTION" label + "My Watchlist" title + "0 titles" in `on_surface_variant`
- Large bookmark icon centred, `secondary_container` colour
- "Your watchlist is empty" heading: `headline-md`
- "Save movies and shows you want to watch later and they'll appear here": `body-md`, `on_surface_variant`, centred
- "Browse Trending Now" CTA: primary gradient button, navigates to Home tab
- "POPULAR RECOMMENDATIONS" section: `label-sm` uppercase heading in `on_surface_variant`, followed by trending content (optional to load — skeleton cards are acceptable)

#### Interactions

- Tapping "×" removes item with **optimistic UI**: item disappears immediately from grid. If AsyncStorage write fails, snap back and show a brief error toast.
- Tapping "Details" navigates to DetailScreen
- Tab bar Watchlist icon shows a numeric badge when count > 0, using `primary_container` colour
- Badge updates reactively as items are added/removed from any screen

#### Minimum Bar
Both populated and empty states displaying correctly. Remove action working and persisted. Navigation to Detail working.

#### What Good Looks Like
Optimistic removal with snap-back on failure. All three filter chips working. "Because you saved" row loading correctly. Badge on tab updating reactively. Correct contextual empty state when a filter returns no results in a non-empty watchlist.

---

### 7.5 Navigation Shell

#### Bottom Tab Navigator

Four tabs: Home, Search, Watchlist, Profile

- Active tab icon: `primary_container` colour
- Inactive: `on_surface_variant`
- Background: Glassmorphism (BlurView + `rgba(35,35,35,0.70)`)
- Watchlist tab: numeric badge, `primary_container`, shown when count > 0
- Profile: placeholder — tapping shows a "Coming Soon" toast or does nothing

#### Stack Navigation

The Detail screen is pushed onto a stack navigator nested inside each tab. Navigating to Detail from Search returns to Search on back — not Home.

#### Type Safety

All navigation params must be typed in `src/navigation/types.ts`:

```typescript
export type RootTabParamList = {
  Home: undefined;
  Search: undefined;
  Watchlist: undefined;
  Profile: undefined;
};

export type HomeStackParamList = {
  HomeMain: undefined;
  Detail: { movieId: number };
};

// Repeat pattern for Search, Watchlist stacks
```

No untyped `navigation.navigate('Detail', { id })` calls anywhere.

---

## 8. API Reference

**Base URL:** `https://api.themoviedb.org/3`

All requests must include `Authorization: Bearer {TMDB_ACCESS_TOKEN}` set by the central Axios interceptor — not manually on individual calls.

### Endpoints

| Endpoint | Used In | Key Response Fields |
|---|---|---|
| `GET /trending/movie/week` | Home hero + Trending row | `results[].id`, `title`, `poster_path`, `backdrop_path`, `vote_average`, `release_date`, `genre_ids` |
| `GET /movie/top_rated` | Home Top Rated row | Same as above |
| `GET /genre/movie/list` | Genre filter chips | `genres[].id`, `genres[].name` |
| `GET /discover/movie?with_genres={id}` | Home genre row | Same as trending |
| `GET /search/movie?query={q}` | Search results | `results[].id`, `title`, `poster_path`, `vote_average`, `release_date`, `genre_ids` |
| `GET /movie/{id}` | Detail main info | `title`, `backdrop_path`, `vote_average`, `release_date`, `genres[]`, `runtime`, `overview` |
| `GET /movie/{id}/credits` | Detail cast | `cast[].name`, `cast[].character`, `cast[].profile_path` |
| `GET /movie/{id}/similar` | Detail similar + Watchlist row | `results[].id`, `title`, `poster_path` |

### Image URLs

TMDB returns image paths, not full URLs. Construct them using:

```
https://image.tmdb.org/t/p/{size}{path}
```

| Size | Use Case |
|---|---|
| `w185` | Cast avatars, small thumbnails |
| `w342` | Standard portrait content cards |
| `w780` | Detail screen backdrop |

Create `src/utils/image.ts` with a helper that returns `null` (not a broken URL) when `path` is `null`.

### Pagination

Paginated endpoints accept a `page` query param (default: 1, max varies). Each page returns up to 20 results. `total_pages` in the response indicates how many pages exist. Request the next page when the user scrolls within 3 items of the row end.

### Left for You to Discover

The following are intentionally not documented. Explore the TMDB API docs and handle them correctly:

- How to filter `/credits` to show only top-billed cast (not the full cast + crew list)
- How `release_date` is formatted and how to extract just the year from it
- The difference between `genre_ids` (on list endpoints) and `genres` (on detail endpoint) and how to handle both

---

## 9. Technical Requirements

### 9.1 Central API Client

`src/api/client.ts` must implement a single Axios instance with:

- Request interceptor: attaches `Authorization: Bearer {token}` from env to every request
- Response interceptor: normalises error shapes into a consistent `{ message, status }` format
- Retry logic: one automatic retry on network error (not on 4xx/5xx responses)

No component or hook imports Axios directly. All imports come from `src/api/client.ts` or `src/api/movies.ts`.

### 9.2 Custom Hook Interface

Every data-fetching hook must return this consistent shape:

```typescript
interface UseQueryResult<T> {
  data: T | null;
  loading: boolean;
  error: string | null;
  refetch: () => void;
}
```

Enforce this in `.cursor/rules`. AI will deviate from this frequently — detecting and correcting that deviation is part of the assessment.

### 9.3 Zustand Store

`src/store/watchlistStore.ts` must implement:

```typescript
interface WatchlistItem {
  id: number;
  title: string;
  posterPath: string | null;
  voteAverage: number;
  releaseDate: string;
  genreIds: number[];
  mediaType: 'movie' | 'tv';
}

interface WatchlistStore {
  items: WatchlistItem[];
  addItem: (item: WatchlistItem) => void;
  removeItem: (id: number) => void;
  isInWatchlist: (id: number) => boolean;
  count: number;
  hydrated: boolean;
}
```

Use Zustand's `persist` middleware with `AsyncStorage` as the storage adapter.

The `hydrated` flag is **required**. No watchlist state (button states, badge counts) should be rendered until the store confirms it has loaded from AsyncStorage. Showing a stale "Add to Watchlist" button before hydration is a bug.

### 9.4 TypeScript

`"strict": true` in `tsconfig.json`. No `any` types permitted. All TMDB API response shapes must have interfaces in `src/api/types.ts`.

### 9.5 Error Boundaries

Wrap each main screen in a React Error Boundary with:
- A "Something went wrong" message in `on_surface`
- A "Try Again" button that triggers `refetch()` on the screen's hook
- The StreamList logo centred above the message

### 9.6 Skeleton Loading

All async sections must use skeleton states (not spinners):
- Shapes match the content they will become
- Shimmer animation cycling between `surface_container_high` and `surface_bright`
- Duration: ~1.5s loop

---

## 10. Submission Requirements

### 10.1 Repository

Submit a link to a public GitHub repository.

- `prompt-logs/` (or `.specstory/history/`) must be committed and non-empty
- `.env` must NOT be committed. `.env.example` with placeholder values must be committed.
- `README.md` must include clear setup instructions

### 10.2 Prompt Logs

Install the **SpecStory** extension in Cursor before you start. All chat and composer sessions will be saved automatically to `.specstory/history/`. Commit this folder.

At the beginning of each Cursor session, include this in your first message:

```
## Session — [Date]
Goal: [What I am trying to accomplish today]
```

A log with 3 entries will score significantly lower than one with 25. The log is evidence of process — not just output.

### 10.3 Architectural Decisions Record

`docs/ADR.md` must document **at least 3 architectural decisions**. For each:

```markdown
## Decision: [Title]

**What:** [The decision you made]

**Context:** [Why it was a choice — what alternatives existed]

**Prompt strategy:** [How you directed AI to implement it — 
  including what went wrong on the first attempt and how you recovered]

**Trade-offs:** [What you gave up with this choice]
```

The ADR is not a formality. It is the clearest signal of AI-first thinking in the entire submission.

### 10.4 Pre-Submission Checklist

- [ ] App runs on both iOS and Android simulators without errors
- [ ] All four main screens implemented and functional
- [ ] `.env` not committed; `.env.example` committed with placeholder values
- [ ] `prompt-logs/` committed and contains meaningful session history
- [ ] `docs/ADR.md` contains at least 3 documented decisions
- [ ] `tsconfig.json` has `"strict": true` and zero `any` types in source
- [ ] No hardcoded hex colours outside `src/theme/colors.ts`
- [ ] No hardcoded spacing values outside `src/theme/spacing.ts`
- [ ] No hardcoded API token anywhere in source files
- [ ] `.cursor/rules` committed, substantive, minimum 10 rules
- [ ] `README.md` with setup instructions present

---

## 11. Assessment Rubric

Each dimension scored 1–5. **Total: 20 points. 16+ clears Phase 2.**

---

### Dimension 1 — Architecture & Structure (5 points)

| Score | Criteria |
|---|---|
| **5** | Folder structure exactly matches spec. Central API client with interceptors correctly implemented. Custom hook interface consistent across all hooks. Zustand store correctly structured with persistence and `hydrated` flag. Strict TypeScript with zero `any`. All colours and spacing from theme tokens — no hardcoded values. `.cursor/rules` is substantive and reflects real project constraints. |
| **4** | Minor deviations from structure. One or two `any` types or hardcoded values. Hooks mostly consistent. |
| **3** | Structure broadly correct but with notable gaps — API calls not going through central client, inconsistent hook interfaces, or missing theme token usage. |
| **2** | Significant structural issues. Multiple hardcoded values, inconsistent patterns, missing abstractions. |
| **1** | No discernible architecture. Inline fetch logic in components, hardcoded strings throughout, no shared utilities. |

---

### Dimension 2 — Feature Completeness & UI Fidelity (5 points)

| Score | Criteria |
|---|---|
| **5** | All four screens implemented and fully functional. UI is faithful to provided designs — correct colours, typography, spacing, and component shapes. All interactions work. At least one stretch goal implemented (infinite scroll, optimistic UI, etc.). |
| **4** | All screens functional. Minor UI deviations from designs. Most interactions working. |
| **3** | All screens present but one has significant functional issues or major visual deviation from design. |
| **2** | One or more screens missing or non-functional. |
| **1** | Fewer than three screens functional. |

---

### Dimension 3 — AI-First Process / Prompt Log Quality (5 points)

| Score | Criteria |
|---|---|
| **5** | Log shows clear task decomposition before prompting. CDIR framework visible in prompt construction. Multiple recovery sequences present (bad output → diagnosis → corrected prompt). Sessions annotated with goals. Log density reflects actual complexity of the build. |
| **4** | Good decomposition visible. Some recovery sequences. Log reasonably dense but missing session annotations. |
| **3** | Log exists and shows some thought, but prompts are mostly single-shot with limited iteration or recovery. |
| **2** | Thin log. Few entries. Vague prompts with no visible decomposition. |
| **1** | Log nearly empty, missing, or shows no meaningful prompting process. |

---

### Dimension 4 — Judgment & Edge Cases (5 points)

| Score | Criteria |
|---|---|
| **5** | All required edge cases handled (null poster/backdrop, null runtime, empty cast, empty similar, independent section error states). Multiple unrequired edge cases identified and handled. ADR documents genuine decisions with prompt strategy and honest trade-offs. Strong evidence of reviewing AI output critically before accepting it. |
| **4** | All required edge cases handled. One or two unrequired edge cases identified. ADR present and substantive. |
| **3** | Most required edge cases handled. ADR present but surface-level. |
| **2** | Several required edge cases missing. ADR sparse or formulaic. |
| **1** | Edge cases largely ignored. ADR absent or empty. |

---

## Timelines

| Day | Focus |
|---|---|
| Day 1 | React Native CLI setup, environment variables, folder scaffold, theme tokens, central API client, navigation shell |
| Day 2 | Home screen — hero card, genre chips, content rows |
| Day 3 | Search screen — both states, debounce, request cancellation, recent searches |
| Day 4 | Detail screen — parallel calls, independent section states, watchlist toggle, all edge cases |
| Day 5 | Watchlist screen — both states, filter chips, optimistic removal, badge, "Because you saved" row |
| Day 6 | Edge case sweep, ADR documentation, prompt log review, testing on iOS + Android |

---

*StreamList Project Specification — AI-First Development Training Program*
*Version 1.0*
