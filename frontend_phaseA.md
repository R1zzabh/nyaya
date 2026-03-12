# NyayaAI Frontend — Phase A: Implementation Plan

## Table of Contents

1. [Overview & Goals](#1-overview--goals)
2. [Tech Stack & Tooling](#2-tech-stack--tooling)
3. [Project Structure](#3-project-structure)
4. [Design System & Theme](#4-design-system--theme)
5. [Page-by-Page Breakdown](#5-page-by-page-breakdown)
6. [Component Architecture](#6-component-architecture)
7. [Backend Integration Layer](#7-backend-integration-layer)
8. [Animation & Motion Design](#8-animation--motion-design)
9. [State Management](#9-state-management)
10. [File Upload & Document Processing](#10-file-upload--document-processing)
11. [Real-Time Search UX](#11-real-time-search-ux)
12. [Responsive Design & Accessibility](#12-responsive-design--accessibility)
13. [Performance Optimization](#13-performance-optimization)
14. [Testing Strategy](#14-testing-strategy)
15. [Deployment & DevOps](#15-deployment--devops)
16. [Implementation Phases & Task Breakdown](#16-implementation-phases--task-breakdown)
17. [Future Expansion Hooks](#17-future-expansion-hooks)

---

## 1. Overview & Goals

### What We're Building (Phase A)

A **premium, professional legal research interface** for Similar Case Retrieval — the first user-facing feature of NyayaAI. The frontend allows lawyers, advocates, and judges to:

- Upload a case document (PDF/DOCX) or paste case text
- Search for similar historical cases from 9,718+ indexed Indian High Court judgments
- View ranked results with multi-dimensional similarity scores
- Compare cases side-by-side
- Filter by court, case type, date range, verdict
- Export results to PDF/JSON

### Design Philosophy

- **Westlaw meets modern SaaS** — clean, authoritative, trustworthy
- Dark navy/charcoal base with gold/amber accents (legal gravitas)
- Subtle animations that feel polished, not playful
- Information-dense layouts that don't overwhelm
- Every interaction should feel deliberate and precise

### Backend API (Already Built)

The FastAPI backend is fully operational at `http://localhost:8000` with these endpoints:

| Method | Endpoint | Purpose |
|--------|----------|---------|
| `POST` | `/api/v1/cases/similar` | Search by text query (min 100 chars) |
| `POST` | `/api/v1/cases/similar/upload` | Upload PDF/DOCX and search |
| `GET` | `/api/v1/cases/{case_id}` | Get full case details |
| `GET` | `/api/v1/cases/{case_id}/similar` | Find similar to existing case |
| `POST` | `/api/v1/cases/explain` | On-demand LLM explanation |
| `GET` | `/api/v1/cases/search/filters` | Available filter options |
| `GET` | `/health` | Backend health check |

---

## 2. Tech Stack & Tooling

### Core Framework

| Tool | Version | Purpose |
|------|---------|---------|
| **Next.js** | 15.x (App Router) | Full-stack React framework |
| **React** | 19.x | UI library |
| **TypeScript** | 5.x | Type safety throughout |

### UI & Styling

| Tool | Purpose |
|------|---------|
| **Tailwind CSS v4** | Utility-first styling |
| **shadcn/ui** | Base component library (Button, Dialog, Card, Sheet, Tabs, Tooltip, etc.) |
| **Radix UI** | Accessible primitives underlying shadcn |
| **Magic UI** | Premium animated components (shimmer borders, animated gradients, text reveals, orbiting elements) |
| **Framer Motion** | Page transitions, layout animations, gesture handling |
| **Lucide React** | Icon library (consistent with shadcn) |
| **Recharts** | Charts for similarity scores, outcome distributions |

### Data & State

| Tool | Purpose |
|------|---------|
| **TanStack Query (React Query) v5** | Server state, caching, background refetches |
| **Zustand** | Lightweight client state (UI state, filters, preferences) |
| **Zod** | Runtime schema validation for API responses |

### File Handling & Export

| Tool | Purpose |
|------|---------|
| **react-dropzone** | Drag-and-drop file upload |
| **@react-pdf/renderer** | PDF export of search results |
| **file-saver** | Client-side file downloads |

### Developer Experience

| Tool | Purpose |
|------|---------|
| **ESLint** | Linting (next/core-web-vitals config) |
| **Prettier** | Code formatting |
| **Vitest** | Unit testing |
| **Playwright** | E2E testing |
| **Storybook** | Component documentation & isolation |

### Package Manager

- **pnpm** — fast, disk-efficient, strict dependency resolution

---

## 3. Project Structure

```
nyaya-ai/frontend/
├── public/
│   ├── fonts/
│   │   ├── inter-var.woff2              # Primary font
│   │   └── playfair-display-var.woff2   # Display/heading font
│   ├── images/
│   │   ├── logo.svg                     # NyayaAI logo
│   │   ├── logo-icon.svg               # Icon-only variant
│   │   ├── og-image.png                # Social share image
│   │   └── scales-pattern.svg          # Decorative legal pattern
│   └── favicon.ico
│
├── src/
│   ├── app/                             # Next.js App Router
│   │   ├── layout.tsx                   # Root layout (fonts, providers, nav)
│   │   ├── page.tsx                     # Landing / home page
│   │   ├── loading.tsx                  # Global loading state
│   │   ├── error.tsx                    # Global error boundary
│   │   ├── not-found.tsx               # 404 page
│   │   │
│   │   ├── search/
│   │   │   ├── page.tsx                # Main search page (upload + text input)
│   │   │   └── loading.tsx
│   │   │
│   │   ├── results/
│   │   │   ├── page.tsx                # Search results list
│   │   │   └── loading.tsx
│   │   │
│   │   ├── case/
│   │   │   └── [id]/
│   │   │       ├── page.tsx            # Individual case detail view
│   │   │       └── loading.tsx
│   │   │
│   │   ├── compare/
│   │   │   └── page.tsx                # Side-by-side case comparison
│   │   │
│   │   └── (future)/                   # Placeholder route groups
│   │       ├── courtroom/              # Feature 1: Simulated Courtroom
│   │       ├── assistant/              # Feature 2: Legal Research Assistant
│   │       └── analytics/              # Feature 4: Outcome Tendency
│   │
│   ├── components/
│   │   ├── ui/                          # shadcn/ui components (auto-generated)
│   │   │   ├── button.tsx
│   │   │   ├── card.tsx
│   │   │   ├── dialog.tsx
│   │   │   ├── dropdown-menu.tsx
│   │   │   ├── input.tsx
│   │   │   ├── select.tsx
│   │   │   ├── sheet.tsx
│   │   │   ├── skeleton.tsx
│   │   │   ├── slider.tsx
│   │   │   ├── tabs.tsx
│   │   │   ├── textarea.tsx
│   │   │   ├── toast.tsx
│   │   │   ├── tooltip.tsx
│   │   │   ├── badge.tsx
│   │   │   ├── progress.tsx
│   │   │   ├── separator.tsx
│   │   │   ├── scroll-area.tsx
│   │   │   ├── collapsible.tsx
│   │   │   ├── command.tsx
│   │   │   └── popover.tsx
│   │   │
│   │   ├── layout/                      # Structural components
│   │   │   ├── navbar.tsx               # Top navigation bar
│   │   │   ├── sidebar.tsx              # Collapsible sidebar (future features)
│   │   │   ├── footer.tsx               # Footer with disclaimers
│   │   │   ├── page-header.tsx          # Reusable page title + breadcrumbs
│   │   │   └── container.tsx            # Max-width wrapper
│   │   │
│   │   ├── search/                      # Search-specific components
│   │   │   ├── search-hero.tsx          # Hero section with search input
│   │   │   ├── text-search-input.tsx    # Textarea for pasting case text
│   │   │   ├── file-upload-zone.tsx     # Drag-and-drop PDF/DOCX upload
│   │   │   ├── search-mode-toggle.tsx   # Toggle: text vs upload
│   │   │   ├── search-filters.tsx       # Filter panel (court, type, date, verdict)
│   │   │   ├── similarity-mode-select.tsx # Combined / Factual / Legal toggle
│   │   │   └── search-history.tsx       # Recent searches list
│   │   │
│   │   ├── results/                     # Results display components
│   │   │   ├── results-header.tsx       # Query summary + result count
│   │   │   ├── result-card.tsx          # Individual case result card
│   │   │   ├── similarity-score-ring.tsx # Circular score visualization
│   │   │   ├── dimension-scores.tsx     # Bar chart of 5 similarity dimensions
│   │   │   ├── result-skeleton.tsx      # Loading skeleton for results
│   │   │   ├── no-results.tsx           # Empty state
│   │   │   ├── legal-disclaimer.tsx     # Mandatory legal disclaimer banner
│   │   │   └── export-menu.tsx          # Export to PDF/JSON dropdown
│   │   │
│   │   ├── case/                        # Case detail components
│   │   │   ├── case-header.tsx          # Title, court, date, verdict badge
│   │   │   ├── case-metadata-grid.tsx   # Structured metadata display
│   │   │   ├── case-parties.tsx         # Petitioner vs Respondent
│   │   │   ├── case-arguments.tsx       # Key arguments (tabbed: petitioner/respondent)
│   │   │   ├── case-reasoning.tsx       # Court reasoning section
│   │   │   ├── case-statutes.tsx        # Cited statutes list with links
│   │   │   ├── case-precedents.tsx      # Cited precedents list
│   │   │   ├── case-full-text.tsx       # Expandable full judgment text
│   │   │   ├── case-confidence.tsx      # Extraction confidence indicators
│   │   │   └── find-similar-button.tsx  # "Find Similar Cases" CTA
│   │   │
│   │   ├── compare/                     # Comparison components
│   │   │   ├── compare-panel.tsx        # Single case column in comparison
│   │   │   ├── compare-diff.tsx         # Highlighted differences
│   │   │   └── compare-toolbar.tsx      # Field selector for comparison
│   │   │
│   │   └── shared/                      # Shared/generic components
│   │       ├── animated-counter.tsx     # Number counting animation
│   │       ├── shimmer-card.tsx         # Magic UI shimmer border card
│   │       ├── gradient-text.tsx        # Animated gradient headings
│   │       ├── spotlight-card.tsx       # Card with cursor spotlight effect
│   │       ├── typing-animation.tsx     # Typewriter text effect
│   │       ├── stat-card.tsx            # Statistics display card
│   │       ├── verdict-badge.tsx        # Color-coded verdict chip
│   │       ├── court-badge.tsx          # Court identifier chip
│   │       ├── confidence-meter.tsx     # Visual confidence indicator
│   │       ├── date-range-picker.tsx    # Date range selection
│   │       ├── multi-select.tsx         # Multi-option select (courts, statutes)
│   │       └── copy-button.tsx          # Copy to clipboard with feedback
│   │
│   ├── lib/                             # Utilities & configuration
│   │   ├── api/
│   │   │   ├── client.ts               # Axios/fetch wrapper with base URL, error handling
│   │   │   ├── cases.ts                # Case-related API functions
│   │   │   ├── search.ts               # Search-related API functions
│   │   │   └── types.ts                # API response TypeScript types
│   │   │
│   │   ├── hooks/
│   │   │   ├── use-search.ts           # Search mutation + result caching
│   │   │   ├── use-case.ts             # Single case query
│   │   │   ├── use-filters.ts          # Filter options query
│   │   │   ├── use-compare.ts          # Case comparison state
│   │   │   ├── use-export.ts           # Export functionality
│   │   │   ├── use-file-upload.ts      # File upload with progress
│   │   │   └── use-debounce.ts         # Input debouncing
│   │   │
│   │   ├── stores/
│   │   │   ├── search-store.ts         # Search state (query, filters, mode)
│   │   │   ├── compare-store.ts        # Cases selected for comparison
│   │   │   └── ui-store.ts             # UI preferences (sidebar, theme)
│   │   │
│   │   ├── utils/
│   │   │   ├── cn.ts                   # clsx + tailwind-merge utility
│   │   │   ├── format-date.ts          # Indian date formatting
│   │   │   ├── format-score.ts         # Score display formatting
│   │   │   ├── truncate.ts             # Text truncation
│   │   │   ├── verdict-color.ts        # Verdict → color mapping
│   │   │   └── court-utils.ts          # Court code → full name mapping
│   │   │
│   │   └── validators/
│   │       ├── search-schema.ts        # Zod schema for search params
│   │       └── api-response-schema.ts  # Zod schemas for API responses
│   │
│   ├── styles/
│   │   └── globals.css                  # Tailwind directives + CSS custom properties
│   │
│   └── providers/
│       ├── query-provider.tsx           # TanStack Query provider
│       └── theme-provider.tsx           # Theme context (dark/light)
│
├── .env.local                           # Local environment variables
├── .env.example                         # Template for env vars
├── next.config.ts                       # Next.js configuration
├── tailwind.config.ts                   # Tailwind theme customization
├── tsconfig.json                        # TypeScript configuration
├── components.json                      # shadcn/ui configuration
├── package.json
├── pnpm-lock.yaml
├── vitest.config.ts                     # Unit test config
├── playwright.config.ts                 # E2E test config
└── .storybook/                          # Storybook config (optional)
```

---

## 4. Design System & Theme

### Color Palette

```css
/* globals.css — CSS custom properties */

:root {
  /* Primary — Deep Navy (authority, trust) */
  --primary-50:  #f0f4fa;
  --primary-100: #dbe3f2;
  --primary-200: #b8c8e5;
  --primary-300: #8da5d4;
  --primary-400: #6282c0;
  --primary-500: #3d5a99;
  --primary-600: #2d4373;
  --primary-700: #1e2d4d;  /* Main brand color */
  --primary-800: #141f36;
  --primary-900: #0b1120;
  --primary-950: #060911;

  /* Accent — Gold/Amber (legal prestige, highlights) */
  --accent-50:  #fefbf0;
  --accent-100: #fdf4d4;
  --accent-200: #fbe7a3;
  --accent-300: #f8d462;
  --accent-400: #f5c030;  /* Primary accent */
  --accent-500: #e6a817;
  --accent-600: #c78510;
  --accent-700: #a66310;
  --accent-800: #884e14;
  --accent-900: #714016;

  /* Semantic Colors */
  --success:    #22c55e;  /* Green — Acquitted, Valid */
  --warning:    #f59e0b;  /* Amber — Needs Review */
  --danger:     #ef4444;  /* Red — Convicted, Invalid */
  --info:       #3b82f6;  /* Blue — Informational */

  /* Verdict-specific Colors */
  --verdict-convicted:  #ef4444;
  --verdict-acquitted:  #22c55e;
  --verdict-dismissed:  #6b7280;
  --verdict-allowed:    #3b82f6;
  --verdict-remanded:   #f59e0b;
  --verdict-settlement: #8b5cf6;

  /* Neutral — Warm Grays (softer than pure gray) */
  --neutral-50:  #fafaf9;
  --neutral-100: #f5f5f4;
  --neutral-200: #e7e5e4;
  --neutral-300: #d6d3d1;
  --neutral-400: #a8a29e;
  --neutral-500: #78716c;
  --neutral-600: #57534e;
  --neutral-700: #44403c;
  --neutral-800: #292524;
  --neutral-900: #1c1917;

  /* Surface Colors */
  --background:  #fafaf9;
  --surface:     #ffffff;
  --surface-alt: #f5f5f4;
  --border:      #e7e5e4;
}

/* Dark mode overrides */
[data-theme="dark"] {
  --background:  #0b1120;
  --surface:     #141f36;
  --surface-alt: #1e2d4d;
  --border:      #2d4373;
}
```

### Typography

```
Headings:      Playfair Display (serif) — legal gravitas, editorial feel
Body:          Inter (sans-serif) — clean readability at all sizes
Monospace:     JetBrains Mono — case IDs, citations, code
```

**Type Scale:**
| Level | Size | Weight | Font | Usage |
|-------|------|--------|------|-------|
| Display | 48px / 3rem | 700 | Playfair | Landing hero |
| H1 | 36px / 2.25rem | 700 | Playfair | Page titles |
| H2 | 28px / 1.75rem | 600 | Playfair | Section headers |
| H3 | 22px / 1.375rem | 600 | Inter | Card titles |
| H4 | 18px / 1.125rem | 600 | Inter | Sub-sections |
| Body | 16px / 1rem | 400 | Inter | Default text |
| Body SM | 14px / 0.875rem | 400 | Inter | Secondary text |
| Caption | 12px / 0.75rem | 500 | Inter | Labels, badges |
| Mono | 14px / 0.875rem | 400 | JetBrains Mono | Citations, IDs |

### Spacing & Layout

- **Container max-width**: 1400px (legal UIs are information-dense)
- **Grid**: 12-column grid with 24px gutters
- **Spacing scale**: 4px base (4, 8, 12, 16, 20, 24, 32, 40, 48, 64, 80)
- **Border radius**: 8px (cards), 6px (inputs), 4px (badges), 9999px (pills)
- **Shadows**: Subtle, layered (no harsh drop shadows)
  ```
  sm:  0 1px 2px rgba(0,0,0,0.05)
  md:  0 4px 6px rgba(0,0,0,0.05), 0 2px 4px rgba(0,0,0,0.03)
  lg:  0 10px 15px rgba(0,0,0,0.05), 0 4px 6px rgba(0,0,0,0.03)
  xl:  0 20px 25px rgba(0,0,0,0.08), 0 8px 10px rgba(0,0,0,0.03)
  ```

### Component Styling Patterns

- **Cards**: White surface + 1px border + subtle shadow. On hover: slight lift + border color change to accent.
- **Buttons (Primary)**: Deep navy bg → gold bg on hover. Subtle scale on press.
- **Buttons (Secondary)**: Ghost/outline style. Border animates on hover.
- **Inputs**: Neutral border, accent focus ring, subtle inner shadow.
- **Badges**: Pill-shaped, muted backgrounds, small text. Verdict badges use semantic colors.
- **Tables**: Alternating subtle row backgrounds, sticky header, hover highlight.

---

## 5. Page-by-Page Breakdown

### 5.1 Landing Page (`/`)

**Purpose**: First impression. Establish credibility. Direct users to search.

**Layout**:
```
┌─────────────────────────────────────────────────────┐
│  Navbar: Logo | Search | [Future: Courtroom | Assistant | Analytics] | Theme Toggle  │
├─────────────────────────────────────────────────────┤
│                                                     │
│  HERO SECTION                                       │
│  ┌───────────────────────────────────────────────┐  │
│  │  Animated gradient background (subtle)        │  │
│  │                                               │  │
│  │  "Find Precedents That Matter"                │  │
│  │   (Playfair Display, gradient text reveal)    │  │
│  │                                               │  │
│  │  AI-powered case law research for Indian      │  │
│  │  High Court judgments                         │  │
│  │                                               │  │
│  │  [Upload Case Document]  [Search by Text]     │  │
│  │   (two prominent CTAs)                        │  │
│  └───────────────────────────────────────────────┘  │
│                                                     │
│  STATS BAR (animated counters on scroll)            │
│  ┌──────────┬──────────┬──────────┬──────────┐      │
│  │ 9,718+   │ 43       │ 13 Years │ 5-Layer  │      │
│  │ Cases    │ Courts   │ Coverage │ Matching │      │
│  └──────────┴──────────┴──────────┴──────────┘      │
│                                                     │
│  HOW IT WORKS (3-step process, staggered reveal)    │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐          │
│  │ 1. Upload│  │ 2. AI    │  │ 3. Review │          │
│  │ your case│  │ analyzes │  │ similar   │          │
│  │ document │  │ & matches│  │ precedents│          │
│  └──────────┘  └──────────┘  └──────────┘          │
│                                                     │
│  FEATURES GRID (spotlight cards with hover)         │
│  ┌────────────────┐  ┌────────────────┐             │
│  │ Multi-Level    │  │ Smart Filters  │             │
│  │ Similarity     │  │ Court, Type,   │             │
│  │ Factual/Legal/ │  │ Date, Verdict  │             │
│  │ Outcome scoring│  │                │             │
│  ├────────────────┤  ├────────────────┤             │
│  │ Cross-Encoder  │  │ AI-Powered     │             │
│  │ Reranking      │  │ Explanations   │             │
│  │ Precision over │  │ Why each case  │             │
│  │ recall         │  │ is similar     │             │
│  └────────────────┘  └────────────────┘             │
│                                                     │
│  FOOTER                                             │
│  Disclaimer | About | Privacy | © 2026 NyayaAI      │
└─────────────────────────────────────────────────────┘
```

**Animations**:
- Hero text: `gradient-text` reveal with Framer Motion `variants` (stagger children)
- Stats bar: `animated-counter` (counts from 0 to target on scroll into view)
- How It Works: staggered fade-up on scroll (IntersectionObserver + Framer Motion)
- Feature cards: Magic UI `spotlight-card` with cursor-following gradient
- Decorative: subtle floating scales-of-justice SVG pattern (parallax)

---

### 5.2 Search Page (`/search`)

**Purpose**: Primary interaction point. Upload or paste case text, configure search.

**Layout**:
```
┌─────────────────────────────────────────────────────┐
│  Navbar                                             │
├─────────────────────────────────────────────────────┤
│                                                     │
│  Page Header: "Search Similar Cases"                │
│  Breadcrumb: Home > Search                          │
│                                                     │
│  ┌─ SEARCH MODE TOGGLE ─────────────────────────┐   │
│  │  [Upload Document]        [Paste Text]        │   │
│  │   (tab-style toggle with animated underline)  │   │
│  └───────────────────────────────────────────────┘   │
│                                                     │
│  ── IF UPLOAD MODE ──                               │
│  ┌───────────────────────────────────────────────┐  │
│  │                                               │  │
│  │        ┌─────────────────────────┐            │  │
│  │        │     📄                  │            │  │
│  │        │  Drop PDF or DOCX here  │            │  │
│  │        │  or click to browse     │            │  │
│  │        │                         │            │  │
│  │        │  Max 50MB • PDF, DOCX   │            │  │
│  │        └─────────────────────────┘            │  │
│  │                                               │  │
│  │  [file-name.pdf  ✕]  (shown after upload)     │  │
│  │  12 pages • 45,320 characters extracted       │  │
│  └───────────────────────────────────────────────┘  │
│                                                     │
│  ── IF TEXT MODE ──                                 │
│  ┌───────────────────────────────────────────────┐  │
│  │  Paste your case text here...                 │  │
│  │                                               │  │
│  │  (large textarea, min 100 chars indicator)    │  │
│  │                                               │  │
│  │  Character count: 0/100 minimum               │  │
│  └───────────────────────────────────────────────┘  │
│                                                     │
│  ┌─ SEARCH OPTIONS (collapsible) ────────────────┐  │
│  │                                               │  │
│  │  Similarity Mode:                             │  │
│  │  ● Combined  ○ Factual  ○ Legal               │  │
│  │                                               │  │
│  │  Max Results: [10 ▾]                          │  │
│  │                                               │  │
│  │  ┌─ Filters (collapsible) ─────────────────┐  │  │
│  │  │ Court:      [Multi-select dropdown]     │  │  │
│  │  │ Case Type:  [Multi-select dropdown]     │  │  │
│  │  │ Date Range: [2012] ──── [2025]          │  │  │
│  │  │ Verdict:    [Multi-select dropdown]     │  │  │
│  │  └─────────────────────────────────────────┘  │  │
│  └───────────────────────────────────────────────┘  │
│                                                     │
│  [ 🔍 Search Similar Cases ]  (primary CTA)        │
│                                                     │
│  ── RECENT SEARCHES (if any) ──                     │
│  ┌───────────────────────────────────────────────┐  │
│  │ "State of Maharashtra v. ..." • 3 min ago     │  │
│  │ "Criminal appeal under Section..." • 1hr ago  │  │
│  └───────────────────────────────────────────────┘  │
│                                                     │
└─────────────────────────────────────────────────────┘
```

**Key UX Details**:
- File upload zone uses `react-dropzone` with visual feedback (border animation on drag-over via Framer Motion)
- Text input shows a live character counter that turns green at 100+ chars
- Filters are fetched from `GET /api/v1/cases/search/filters` on page load and cached
- Search history stored in `localStorage`, displayed as clickable chips
- The Search button is disabled until minimum requirements are met (file uploaded OR 100+ chars)
- On submit, navigate to `/results` with search params in URL (shareable links)

---

### 5.3 Results Page (`/results`)

**Purpose**: Display ranked search results with similarity scores and quick actions.

**Layout**:
```
┌─────────────────────────────────────────────────────┐
│  Navbar                                             │
├─────────────────────────────────────────────────────┤
│                                                     │
│  ┌─ RESULTS HEADER ─────────────────────────────┐   │
│  │  Showing 8 similar cases                      │   │
│  │  Query: "Criminal appeal under IPC 302..."    │   │
│  │  Mode: Combined | Filters: Patna HC, 2020-25  │   │
│  │                                               │   │
│  │  [Modify Search]  [Export ▾]  [Compare (2)]   │   │
│  └───────────────────────────────────────────────┘   │
│                                                     │
│  ┌─ LEGAL DISCLAIMER ───────────────────────────┐   │
│  │ ⚠ This is an AI-powered research tool, not   │   │
│  │   legal advice. Results are based on          │   │
│  │   statistical similarity. Always verify with  │   │
│  │   primary sources.                [Dismiss]   │   │
│  └───────────────────────────────────────────────┘   │
│                                                     │
│  ┌─ RESULT CARD #1 ─────────────────────────────┐   │
│  │                                               │   │
│  │  ┌─────┐  State of Bihar v. Ram Kumar         │   │
│  │  │ 87% │  High Court of Patna • 2023-06-14   │   │
│  │  │score│  Criminal • Convicted                │   │
│  │  └─────┘                                      │   │
│  │                                               │   │
│  │  Similarity Dimensions:                       │   │
│  │  Statutory ████████░░ 82%                     │   │
│  │  Case Type ██████████ 100%                    │   │
│  │  Court     ██████████ 100%                    │   │
│  │  Outcome   ██████░░░░ 60%                     │   │
│  │  Temporal  ████████░░ 85%                     │   │
│  │                                               │   │
│  │  "Both cases involve IPC Section 302 murder   │   │
│  │   charges with circumstantial evidence..."    │   │
│  │                                               │   │
│  │  Statutes: IPC §302, §304, CrPC §164         │   │
│  │                                               │   │
│  │  [View Details]  [☐ Compare]  [Get AI Expl.]  │   │
│  └───────────────────────────────────────────────┘   │
│                                                     │
│  ┌─ RESULT CARD #2 ─────────────────────────────┐   │
│  │  ... (same structure)                         │   │
│  └───────────────────────────────────────────────┘   │
│                                                     │
│  ... more cards ...                                 │
│                                                     │
└─────────────────────────────────────────────────────┘
```

**Key UX Details**:
- Results appear with staggered fade-in animation (Framer Motion `variants` with `staggerChildren`)
- Each result card has a prominent circular similarity score (animated ring that fills to the percentage)
- Dimension scores shown as horizontal mini bar charts
- "Get AI Explanation" triggers `POST /api/v1/cases/explain` — shows a loading shimmer, then reveals LLM explanation inline
- Compare checkbox adds/removes cases from comparison store (Zustand)
- Floating "Compare" button appears at bottom when 2+ cases selected (Framer Motion `AnimatePresence`)
- Export dropdown: PDF (formatted report) or JSON (raw data)
- "Modify Search" returns to `/search` with previous params pre-filled

---

### 5.4 Case Detail Page (`/case/[id]`)

**Purpose**: Full case view with all metadata, arguments, reasoning, and similar cases.

**Layout**:
```
┌─────────────────────────────────────────────────────┐
│  Navbar                                             │
├─────────────────────────────────────────────────────┤
│                                                     │
│  Breadcrumb: Home > Search > Results > Case Detail  │
│                                                     │
│  ┌─ CASE HEADER ────────────────────────────────┐   │
│  │                                               │   │
│  │  State of Bihar v. Ram Kumar Yadav            │   │
│  │  ─────────────────────────────────────────    │   │
│  │  High Court of Patna  •  14 June 2023         │   │
│  │  Case ID: CR.A. 456/2023                      │   │
│  │                                               │   │
│  │  [Convicted]  [Criminal]  [IPC §302]          │   │
│  │   (colored badges)                            │   │
│  │                                               │   │
│  │  Confidence: ████████░░ 82%                   │   │
│  │                                               │   │
│  │  [Find Similar Cases]  [Add to Compare]       │   │
│  └───────────────────────────────────────────────┘   │
│                                                     │
│  ┌─ TABS ───────────────────────────────────────┐   │
│  │ [Overview] [Arguments] [Reasoning] [Full Text]│   │
│  ├───────────────────────────────────────────────┤   │
│  │                                               │   │
│  │  ── OVERVIEW TAB ──                           │   │
│  │                                               │   │
│  │  ┌─ Parties ───────────────────────────────┐  │   │
│  │  │  Petitioner(s):  State of Bihar          │  │   │
│  │  │  Respondent(s):  Ram Kumar Yadav         │  │   │
│  │  │  Bench:          Justice A.K. Singh      │  │   │
│  │  └─────────────────────────────────────────┘  │   │
│  │                                               │   │
│  │  ┌─ Charges & Sections ────────────────────┐  │   │
│  │  │  Charges: Murder, Criminal Conspiracy    │  │   │
│  │  │  Sections: IPC §302, §120B, §34          │  │   │
│  │  │  Sentence: Life Imprisonment             │  │   │
│  │  └─────────────────────────────────────────┘  │   │
│  │                                               │   │
│  │  ┌─ Statutes Cited ────────────────────────┐  │   │
│  │  │  • Indian Penal Code, 1860               │  │   │
│  │  │  • Code of Criminal Procedure, 1973      │  │   │
│  │  │  • Indian Evidence Act, 1872             │  │   │
│  │  └─────────────────────────────────────────┘  │   │
│  │                                               │   │
│  │  ┌─ Precedents Cited ─────────────────────┐   │   │
│  │  │  • Bachan Singh v. State of Punjab       │  │   │
│  │  │  • Machhi Singh v. State of Punjab       │  │   │
│  │  └─────────────────────────────────────────┘  │   │
│  │                                               │   │
│  │  ── ARGUMENTS TAB ──                          │   │
│  │  (Side-by-side or tabbed petitioner/resp.)    │   │
│  │                                               │   │
│  │  ── REASONING TAB ──                          │   │
│  │  (Court reasoning with headnotes highlighted) │   │
│  │                                               │   │
│  │  ── FULL TEXT TAB ──                          │   │
│  │  (Scrollable full judgment, collapsible)      │   │
│  │                                               │   │
│  └───────────────────────────────────────────────┘   │
│                                                     │
└─────────────────────────────────────────────────────┘
```

**Key UX Details**:
- Data fetched via `GET /api/v1/cases/{case_id}` with TanStack Query caching
- "Find Similar Cases" triggers `GET /api/v1/cases/{case_id}/similar` and navigates to `/results`
- Full text tab uses a scroll area with virtual scrolling for very long documents
- Tab transitions use Framer Motion `layout` animations
- Confidence meter per field shown on hover (from `confidence_scores` JSON)

---

### 5.5 Compare Page (`/compare`)

**Purpose**: Side-by-side comparison of 2-3 selected cases.

**Layout**:
```
┌──────────────────────────────────────────────────────────────┐
│  Navbar                                                      │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Compare Cases (2 selected)            [Clear All] [Export]  │
│                                                              │
│  ┌─ FIELD SELECTOR ────────────────────────────────────────┐ │
│  │  Show: ☑ Parties  ☑ Charges  ☑ Verdict  ☑ Statutes     │ │
│  │        ☑ Arguments  ☑ Reasoning  ☐ Full Text            │ │
│  └─────────────────────────────────────────────────────────┘ │
│                                                              │
│  ┌──────────────────────┬──────────────────────┐             │
│  │  Case A              │  Case B              │             │
│  │  State v. Kumar      │  State v. Singh      │             │
│  │  Patna HC • 2023     │  Bombay HC • 2022    │             │
│  │  ──────────────────  │  ──────────────────  │             │
│  │                      │                      │             │
│  │  Charges:            │  Charges:            │             │
│  │  IPC §302, §120B     │  IPC §302, §307     │             │
│  │         ^^^^ common         ^^^^            │             │
│  │                      │                      │             │
│  │  Verdict:            │  Verdict:            │             │
│  │  [Convicted]         │  [Acquitted]         │             │
│  │                      │                      │             │
│  │  ... more fields ... │  ... more fields ... │             │
│  └──────────────────────┴──────────────────────┘             │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

**Key UX Details**:
- Cases stored in Zustand `compare-store`, read from URL params as well (shareable)
- Common fields highlighted (e.g., shared statutes get accent background)
- Divergent fields shown with subtle diff coloring (green vs red)
- Responsive: stacks vertically on mobile
- Max 3 cases in comparison (UI constraint, not API constraint)

---

## 6. Component Architecture

### Component Hierarchy

```
app/layout.tsx
├── QueryProvider
│   ├── ThemeProvider
│   │   ├── Navbar
│   │   │   ├── Logo
│   │   │   ├── NavLinks (with future feature placeholders)
│   │   │   ├── ThemeToggle
│   │   │   └── MobileMenu (Sheet)
│   │   │
│   │   ├── <page content via children>
│   │   │
│   │   └── Footer
│   │       └── LegalDisclaimer
│   │
│   └── Toaster (notifications)

search/page.tsx
├── PageHeader
├── SearchModeToggle
├── FileUploadZone | TextSearchInput  (conditional)
├── Collapsible → SearchFilters
│   ├── SimilarityModeSelect
│   ├── MultiSelect (courts)
│   ├── MultiSelect (case types)
│   ├── DateRangePicker
│   └── MultiSelect (verdicts)
├── Button (Search CTA)
└── SearchHistory

results/page.tsx
├── ResultsHeader
│   ├── ExportMenu
│   └── CompareButton
├── LegalDisclaimer
└── ResultCard[] (mapped)
    ├── SimilarityScoreRing
    ├── DimensionScores
    ├── VerdictBadge
    ├── CourtBadge
    └── ActionButtons (View, Compare, AI Explain)

case/[id]/page.tsx
├── CaseHeader
│   ├── VerdictBadge
│   ├── CourtBadge
│   ├── ConfidenceMeter
│   └── FindSimilarButton
├── Tabs
│   ├── TabContent: CaseMetadataGrid
│   │   ├── CaseParties
│   │   ├── CaseStatutes
│   │   └── CasePrecedents
│   ├── TabContent: CaseArguments
│   ├── TabContent: CaseReasoning
│   └── TabContent: CaseFullText
```

### Shared Component Patterns

All custom components follow these conventions:

```typescript
// Pattern: Forward ref, composable, cn() for className merge
import { forwardRef, type HTMLAttributes } from "react"
import { cn } from "@/lib/utils/cn"

interface ResultCardProps extends HTMLAttributes<HTMLDivElement> {
  result: SearchResult
  onCompareToggle?: (id: string) => void
  isComparing?: boolean
}

const ResultCard = forwardRef<HTMLDivElement, ResultCardProps>(
  ({ result, onCompareToggle, isComparing, className, ...props }, ref) => {
    return (
      <div ref={ref} className={cn("...", className)} {...props}>
        {/* ... */}
      </div>
    )
  }
)
ResultCard.displayName = "ResultCard"
```

---

## 7. Backend Integration Layer

### API Client (`lib/api/client.ts`)

```typescript
// Centralized fetch wrapper

const API_BASE_URL = process.env.NEXT_PUBLIC_API_URL || "http://localhost:8000"

class ApiClient {
  private baseUrl: string

  constructor(baseUrl: string) {
    this.baseUrl = baseUrl
  }

  async get<T>(path: string, params?: Record<string, string>): Promise<T> {
    const url = new URL(`${this.baseUrl}${path}`)
    if (params) {
      Object.entries(params).forEach(([k, v]) => url.searchParams.set(k, v))
    }
    const res = await fetch(url.toString())
    if (!res.ok) throw new ApiError(res.status, await res.text())
    return res.json()
  }

  async post<T>(path: string, body: unknown): Promise<T> {
    const res = await fetch(`${this.baseUrl}${path}`, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(body),
    })
    if (!res.ok) throw new ApiError(res.status, await res.text())
    return res.json()
  }

  async postForm<T>(path: string, formData: FormData): Promise<T> {
    const res = await fetch(`${this.baseUrl}${path}`, {
      method: "POST",
      body: formData,
    })
    if (!res.ok) throw new ApiError(res.status, await res.text())
    return res.json()
  }
}

export const api = new ApiClient(API_BASE_URL)
```

### TypeScript Types (`lib/api/types.ts`)

```typescript
// Mirrors backend Pydantic models exactly

export interface SearchResult {
  case_id: string
  case_title: string
  court: string
  date_of_judgment: string       // ISO date string
  case_type: string
  verdict: string
  similarity_score: number       // 0.0 - 1.0
  vector_score: number
  rerank_score: number | null
  dimension_scores: {
    statutory: number
    case_type: number
    court: number
    outcome: number
    temporal: number
  }
  explanation: string            // Short explanation
  detailed_explanation: string   // Markdown
  statutes: string[]
  sections_invoked: string[]
  charges: string[]
  headnotes: string[]
}

export interface SearchResponse {
  query_summary: string
  total_results: number
  search_mode: "combined" | "factual" | "legal"
  results: SearchResult[]
  disclaimer: string
  processing_time_ms: number
}

export interface CaseDetail {
  id: number
  case_id: string
  case_title: string
  court: string
  bench: string[]
  date_of_judgment: string
  case_type: string
  petitioner: string[]
  respondent: string[]
  sections_invoked: string[]
  charges: string[]
  verdict: string
  sentence: string | null
  key_arguments_petitioner: string | null
  key_arguments_respondent: string | null
  court_reasoning: string | null
  headnotes: string[]
  full_text: string
  confidence_scores: Record<string, number>
  average_confidence: number
  validation_status: string
  statutes: { statute_name: string; statute_type: string }[]
  precedents: { case_name: string; citation: string; year: number }[]
}

export interface SearchFilters {
  courts: string[]
  case_types: string[]
  verdicts: string[]
  year_range: { min: number; max: number }
}

export interface SearchParams {
  query_text?: string
  search_mode?: "combined" | "factual" | "legal"
  top_k?: number
  filters?: {
    courts?: string[]
    case_types?: string[]
    verdicts?: string[]
    year_min?: number
    year_max?: number
  }
}
```

### API Functions (`lib/api/search.ts`, `lib/api/cases.ts`)

```typescript
// search.ts
import { api } from "./client"
import type { SearchResponse, SearchParams, SearchFilters } from "./types"

export async function searchByText(params: SearchParams): Promise<SearchResponse> {
  return api.post<SearchResponse>("/api/v1/cases/similar", params)
}

export async function searchByUpload(
  file: File,
  params: Omit<SearchParams, "query_text">
): Promise<SearchResponse> {
  const formData = new FormData()
  formData.append("file", file)
  if (params.search_mode) formData.append("search_mode", params.search_mode)
  if (params.top_k) formData.append("top_k", String(params.top_k))
  if (params.filters) formData.append("filters", JSON.stringify(params.filters))
  return api.postForm<SearchResponse>("/api/v1/cases/similar/upload", formData)
}

export async function getSearchFilters(): Promise<SearchFilters> {
  return api.get<SearchFilters>("/api/v1/cases/search/filters")
}

export async function getAiExplanation(
  queryText: string,
  caseId: string
): Promise<{ explanation: string }> {
  return api.post("/api/v1/cases/explain", {
    query_text: queryText,
    case_id: caseId,
  })
}

// cases.ts
import { api } from "./client"
import type { CaseDetail, SearchResponse } from "./types"

export async function getCaseById(caseId: string): Promise<CaseDetail> {
  return api.get<CaseDetail>(`/api/v1/cases/${encodeURIComponent(caseId)}`)
}

export async function getSimilarCases(caseId: string): Promise<SearchResponse> {
  return api.get<SearchResponse>(
    `/api/v1/cases/${encodeURIComponent(caseId)}/similar`
  )
}
```

### TanStack Query Hooks (`lib/hooks/`)

```typescript
// use-search.ts
import { useMutation, useQuery } from "@tanstack/react-query"
import { searchByText, searchByUpload, getSearchFilters } from "@/lib/api/search"

export function useSearchByText() {
  return useMutation({
    mutationFn: searchByText,
    // Results cached by query hash for back-navigation
  })
}

export function useSearchByUpload() {
  return useMutation({
    mutationFn: ({ file, params }: { file: File; params: any }) =>
      searchByUpload(file, params),
  })
}

export function useSearchFilters() {
  return useQuery({
    queryKey: ["search-filters"],
    queryFn: getSearchFilters,
    staleTime: 1000 * 60 * 30,  // Cache for 30 min (filters rarely change)
  })
}

// use-case.ts
import { useQuery } from "@tanstack/react-query"
import { getCaseById } from "@/lib/api/cases"

export function useCase(caseId: string) {
  return useQuery({
    queryKey: ["case", caseId],
    queryFn: () => getCaseById(caseId),
    enabled: !!caseId,
    staleTime: 1000 * 60 * 60, // Cases don't change, cache 1 hour
  })
}
```

### Environment Configuration

```bash
# .env.local
NEXT_PUBLIC_API_URL=http://localhost:8000
NEXT_PUBLIC_APP_NAME=NyayaAI
NEXT_PUBLIC_APP_DESCRIPTION=AI-Powered Legal Research for Indian Courts
```

```bash
# .env.example (committed to repo)
NEXT_PUBLIC_API_URL=http://localhost:8000
NEXT_PUBLIC_APP_NAME=NyayaAI
NEXT_PUBLIC_APP_DESCRIPTION=AI-Powered Legal Research for Indian Courts
```

### Backend CORS (Already Configured)

The FastAPI backend already has CORS configured for `http://localhost:3000`:
```python
# src/api/main.py — already in place
app.add_middleware(
    CORSMiddleware,
    allow_origins=settings.cors_origins,  # ["http://localhost:3000"]
    allow_methods=["*"],
    allow_headers=["*"],
)
```

No backend changes needed for local development. For production, update `cors_origins` in `.env`.

---

## 8. Animation & Motion Design

### Philosophy

Animations serve **three purposes** in NyayaAI:
1. **Feedback** — Confirm user actions (button clicks, uploads, searches)
2. **Continuity** — Smooth transitions between pages and states
3. **Polish** — Elevated feel befitting a legal professional tool

Animations should be **subtle and fast**. Nothing bouncy, no overshooting. Think "luxury brand website", not "startup landing page".

### Framer Motion Defaults

```typescript
// lib/utils/motion.ts

export const defaultTransition = {
  type: "spring",
  stiffness: 300,
  damping: 30,
}

export const fadeIn = {
  initial: { opacity: 0, y: 8 },
  animate: { opacity: 1, y: 0 },
  exit: { opacity: 0, y: -8 },
  transition: { duration: 0.3, ease: "easeOut" },
}

export const staggerContainer = {
  animate: {
    transition: {
      staggerChildren: 0.08,
    },
  },
}

export const staggerItem = {
  initial: { opacity: 0, y: 16 },
  animate: { opacity: 1, y: 0 },
  transition: { duration: 0.4, ease: [0.25, 0.4, 0.25, 1] },
}

export const scaleOnTap = {
  whileTap: { scale: 0.98 },
  transition: { duration: 0.1 },
}

export const slideInFromRight = {
  initial: { x: 20, opacity: 0 },
  animate: { x: 0, opacity: 1 },
  exit: { x: -20, opacity: 0 },
  transition: { duration: 0.3 },
}
```

### Specific Animation Assignments

| Element | Animation | Library |
|---------|-----------|---------|
| Page transitions | Fade + slight vertical shift | Framer Motion `AnimatePresence` |
| Result cards appearing | Staggered fade-up | Framer Motion `variants` |
| Similarity score ring | SVG stroke-dashoffset animation | Framer Motion `motion.circle` |
| Stat counters (landing) | Count from 0 to target | Custom `useCountAnimation` hook |
| File upload zone | Border dash animation on drag | Framer Motion |
| Search button | Subtle scale on press, shimmer on hover | Framer Motion + CSS |
| Feature cards (landing) | Cursor-following spotlight gradient | Magic UI `SpotlightCard` |
| Hero heading | Gradient text color shift | Magic UI `AnimatedGradientText` |
| Tab switching | Layout animation with shared element | Framer Motion `layout` |
| Toast notifications | Slide in from top-right | shadcn `Toaster` |
| Skeleton loaders | Pulse shimmer | Tailwind `animate-pulse` |
| Compare floating button | Slide up from bottom | Framer Motion `AnimatePresence` |
| Filter panel expand | Height animation with overflow | Framer Motion `motion.div` with `layout` |
| Navbar on scroll | Backdrop blur + border appears | CSS `backdrop-filter` transition |

### Loading States

Every async operation has a three-phase visual:

1. **Skeleton**: Placeholder shapes matching expected content layout (shadcn `Skeleton`)
2. **Progress Indicator**: For long operations (search), show a stepped progress:
   - "Generating embeddings..."
   - "Searching 9,718 cases..."
   - "Reranking top candidates..."
   - "Analyzing similarity..."
3. **Result Reveal**: Staggered animation as results appear

---

## 9. State Management

### Strategy: Server State + Minimal Client State

**Server state** (TanStack Query): All data from the API — search results, case details, filters.
**Client state** (Zustand): UI-only state — selected comparison cases, filter panel open/closed, theme preference.

### Zustand Stores

```typescript
// stores/search-store.ts
interface SearchState {
  searchMode: "text" | "upload"
  queryText: string
  uploadedFile: File | null
  similarityMode: "combined" | "factual" | "legal"
  topK: number
  filters: {
    courts: string[]
    caseTypes: string[]
    verdicts: string[]
    yearMin: number | null
    yearMax: number | null
  }
  recentSearches: Array<{ query: string; timestamp: number; resultCount: number }>

  // Actions
  setSearchMode: (mode: "text" | "upload") => void
  setQueryText: (text: string) => void
  setUploadedFile: (file: File | null) => void
  setSimilarityMode: (mode: "combined" | "factual" | "legal") => void
  setTopK: (k: number) => void
  updateFilters: (partial: Partial<SearchState["filters"]>) => void
  clearFilters: () => void
  addRecentSearch: (search: { query: string; resultCount: number }) => void
}

// stores/compare-store.ts
interface CompareState {
  selectedCaseIds: string[]              // Max 3
  addCase: (id: string) => void
  removeCase: (id: string) => void
  clearAll: () => void
  isSelected: (id: string) => boolean
}

// stores/ui-store.ts
interface UiState {
  theme: "light" | "dark"
  sidebarOpen: boolean
  filterPanelOpen: boolean
  toggleTheme: () => void
  setSidebarOpen: (open: boolean) => void
  setFilterPanelOpen: (open: boolean) => void
}
```

### Data Flow Diagram

```
User Action (search, upload, click)
    │
    ▼
Zustand Store (update local state: filters, mode, etc.)
    │
    ▼
TanStack Query Mutation/Query (API call with params from store)
    │
    ▼
API Client (fetch to FastAPI backend at :8000)
    │
    ▼
TanStack Query Cache (responses cached, deduplicated)
    │
    ▼
React Components (re-render with new data)
```

---

## 10. File Upload & Document Processing

### Upload Flow

```
1. User drags/drops or clicks to select file
2. Client-side validation:
   - File type: .pdf, .docx only
   - File size: max 50MB
   - Show error toast if invalid
3. Show file preview (name, size, type icon)
4. On "Search" click:
   - Create FormData with file + search params
   - POST to /api/v1/cases/similar/upload
   - Show progress states (uploading → processing → searching)
5. Backend extracts text, generates embedding, searches
6. Navigate to /results with response data
```

### FileUploadZone Component Details

```typescript
// Uses react-dropzone
// Visual states:
//   - Default: dashed border, icon, instructional text
//   - Drag hover: border turns solid accent, background tints, scale up slightly
//   - File selected: shows filename, page count preview, remove button
//   - Uploading: progress bar overlay
//   - Error: red border, error message

// Accepted formats
const ACCEPTED_FORMATS = {
  "application/pdf": [".pdf"],
  "application/vnd.openxmlformats-officedocument.wordprocessingml.document": [".docx"],
}

const MAX_FILE_SIZE = 50 * 1024 * 1024  // 50MB
```

---

## 11. Real-Time Search UX

### Search Progress Experience

Since the search pipeline (embedding → vector search → reranking → analysis) takes several seconds, we provide a rich loading experience:

```
┌─────────────────────────────────────────────────────┐
│                                                     │
│     Searching 9,718 cases...                        │
│                                                     │
│     ✓ Generating embeddings          (0.8s)         │
│     ✓ Vector similarity search       (1.2s)         │
│     ● Reranking candidates...        (running)      │
│     ○ Analyzing similarity                          │
│     ○ Generating explanations                       │
│                                                     │
│     ████████████░░░░░░░░░░ 60%                      │
│                                                     │
│     Estimated time remaining: ~3s                   │
│                                                     │
└─────────────────────────────────────────────────────┘
```

**Implementation**: Since the backend returns a single response (not streaming), we simulate progress steps client-side based on typical timing:

```typescript
const SEARCH_STEPS = [
  { label: "Generating embeddings", estimatedMs: 800 },
  { label: "Searching vector database", estimatedMs: 1200 },
  { label: "Reranking candidates", estimatedMs: 1500 },
  { label: "Analyzing similarity dimensions", estimatedMs: 800 },
  { label: "Preparing results", estimatedMs: 500 },
]
```

When the actual response arrives, immediately jump to 100% and reveal results.

**Future**: When WebSocket support is added to the backend (`/ws/search`), replace simulated progress with real server-sent events.

---

## 12. Responsive Design & Accessibility

### Breakpoints

```
sm:  640px   (mobile landscape)
md:  768px   (tablet)
lg:  1024px  (small desktop)
xl:  1280px  (desktop)
2xl: 1536px  (large desktop)
```

### Responsive Behavior

| Component | Mobile (<768px) | Tablet (768-1024px) | Desktop (>1024px) |
|-----------|-----------------|---------------------|-------------------|
| Navbar | Hamburger menu (Sheet) | Full nav, no sidebar | Full nav + optional sidebar |
| Search Input | Full width, stacked mode toggle | Same | Side-by-side mode toggle |
| Filters | Bottom sheet (Sheet) | Collapsible sidebar | Inline collapsible panel |
| Result Cards | Full width, stacked | Full width | Max-width container |
| Case Detail | Single column, tabs | Same | Two-column (metadata + content) |
| Compare | Stacked vertically | Side by side (2 cols) | Side by side (2-3 cols) |
| Dimension Bars | Horizontal, compact | Standard | Standard with labels |

### Accessibility (WCAG 2.1 AA)

- All interactive elements are keyboard navigable (tab order, focus rings)
- ARIA labels on custom components (score rings, badges, upload zone)
- Color contrast ratio ≥ 4.5:1 for all text
- Verdict badges use both color AND text (not color-only)
- Screen reader announcements for search progress and results
- Reduced motion: respect `prefers-reduced-motion` media query
  ```typescript
  const prefersReducedMotion = useReducedMotion()
  // Disable Framer Motion animations when true
  ```
- Focus trapping in modals/sheets (handled by Radix UI)
- Alt text on all decorative images (or `aria-hidden` if purely decorative)

---

## 13. Performance Optimization

### Next.js Optimizations

- **App Router** with React Server Components where possible (layout, static sections)
- **Dynamic imports** for heavy components:
  ```typescript
  const CaseFullText = dynamic(() => import("@/components/case/case-full-text"), {
    loading: () => <Skeleton className="h-96" />,
  })
  ```
- **Image optimization**: `next/image` for any images/icons
- **Font optimization**: `next/font` for Inter and Playfair Display (self-hosted, no layout shift)
- **Metadata**: Dynamic `generateMetadata` for SEO on case pages

### Bundle Optimization

- **Tree shaking**: Import only needed Lucide icons: `import { Search } from "lucide-react"` (not `import * as Icons`)
- **Code splitting**: Each route is automatically code-split by Next.js
- **shadcn/ui**: Components are copied into the project (not a library import), so only used components are bundled
- **Recharts**: Dynamic import (only loaded on pages with charts)

### Data Optimization

- **TanStack Query caching**: Aggressive stale times for stable data (filters: 30min, cases: 1hr)
- **Prefetching**: On hover over result cards, prefetch case detail data
  ```typescript
  const queryClient = useQueryClient()
  const handleHover = (caseId: string) => {
    queryClient.prefetchQuery({
      queryKey: ["case", caseId],
      queryFn: () => getCaseById(caseId),
    })
  }
  ```
- **Search result caching**: Results are cached by query params, enabling instant back-navigation
- **Debounced inputs**: Text input is debounced (300ms) before enabling the search button

### Lighthouse Targets

| Metric | Target |
|--------|--------|
| Performance | ≥ 90 |
| Accessibility | ≥ 95 |
| Best Practices | ≥ 95 |
| SEO | ≥ 90 |
| LCP | < 2.5s |
| FID | < 100ms |
| CLS | < 0.1 |

---

## 14. Testing Strategy

### Unit Tests (Vitest)

- **Utility functions**: `format-date`, `format-score`, `verdict-color`, `court-utils`
- **Zustand stores**: All actions produce expected state changes
- **API client**: Mock fetch, test error handling, response parsing
- **Zod schemas**: Validate API response shapes

### Component Tests (Vitest + Testing Library)

- **ResultCard**: Renders all fields correctly, compare toggle works
- **SimilarityScoreRing**: Renders correct percentage
- **FileUploadZone**: Accepts/rejects correct file types, shows error states
- **SearchFilters**: Multi-select behavior, clear filters

### E2E Tests (Playwright)

- **Happy path**: Land on home → navigate to search → paste text → search → view results → click case → see detail
- **Upload flow**: Upload PDF → search → verify results
- **Compare flow**: Select 2 cases → compare → verify side-by-side
- **Error states**: Backend down → graceful error display
- **Responsive**: Run tests at mobile/tablet/desktop viewports

### Test File Structure

```
frontend/
├── __tests__/
│   ├── unit/
│   │   ├── utils/
│   │   │   ├── format-date.test.ts
│   │   │   ├── format-score.test.ts
│   │   │   └── verdict-color.test.ts
│   │   ├── stores/
│   │   │   ├── search-store.test.ts
│   │   │   └── compare-store.test.ts
│   │   └── api/
│   │       └── client.test.ts
│   │
│   ├── components/
│   │   ├── result-card.test.tsx
│   │   ├── file-upload-zone.test.tsx
│   │   └── similarity-score-ring.test.tsx
│   │
│   └── e2e/
│       ├── search-flow.spec.ts
│       ├── case-detail.spec.ts
│       └── compare-flow.spec.ts
```

---

## 15. Deployment & DevOps

### Docker Setup

Add a `Dockerfile` to the frontend directory:

```dockerfile
# frontend/Dockerfile
FROM node:20-alpine AS base
RUN corepack enable && corepack prepare pnpm@latest --activate

FROM base AS deps
WORKDIR /app
COPY package.json pnpm-lock.yaml ./
RUN pnpm install --frozen-lockfile

FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
ENV NEXT_TELEMETRY_DISABLED=1
RUN pnpm build

FROM base AS runner
WORKDIR /app
ENV NODE_ENV=production
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static
COPY --from=builder /app/public ./public
EXPOSE 3000
CMD ["node", "server.js"]
```

### Docker Compose Integration

Add to the existing `nyaya-ai/docker-compose.yml`:

```yaml
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    container_name: nyaya_frontend
    ports:
      - "3000:3000"
    environment:
      - NEXT_PUBLIC_API_URL=http://api:8000
    depends_on:
      - api
    networks:
      - nyaya_network

  api:
    build:
      context: .
      dockerfile: Dockerfile.api  # New: Dockerfile for FastAPI
    container_name: nyaya_api
    ports:
      - "8000:8000"
    env_file:
      - .env
    depends_on:
      - postgres
      - qdrant
      - redis
    networks:
      - nyaya_network
```

### Development Workflow

```bash
# Local development (frontend only, backend already running)
cd nyaya-ai/frontend
pnpm install
pnpm dev          # http://localhost:3000

# Full stack with Docker
cd nyaya-ai
docker-compose up -d          # All services including frontend

# Production build test
cd nyaya-ai/frontend
pnpm build
pnpm start
```

---

## 16. Implementation Phases & Task Breakdown

### Phase A-1: Project Scaffolding (Day 1-2)

| # | Task | Details |
|---|------|---------|
| 1 | Initialize Next.js 15 project | `pnpm create next-app frontend --typescript --tailwind --eslint --app --src-dir` |
| 2 | Install dependencies | shadcn/ui, framer-motion, tanstack-query, zustand, zod, react-dropzone, recharts, lucide-react |
| 3 | Configure shadcn/ui | `pnpm dlx shadcn@latest init` with custom theme colors |
| 4 | Add shadcn components | button, card, dialog, input, select, sheet, tabs, toast, badge, skeleton, slider, textarea, tooltip, separator, scroll-area, collapsible, popover, progress, command, dropdown-menu |
| 5 | Set up fonts | Playfair Display + Inter via `next/font` |
| 6 | Configure Tailwind theme | Custom colors, typography, spacing per design system |
| 7 | Set up CSS custom properties | Full color palette in `globals.css` |
| 8 | Create `.env.example` and `.env.local` | API URL and app metadata |
| 9 | Set up providers | `QueryProvider`, `ThemeProvider` in root layout |

### Phase A-2: Layout & Navigation (Day 2-3)

| # | Task | Details |
|---|------|---------|
| 10 | Build `Navbar` | Logo, nav links (Search active, future items grayed), theme toggle, mobile hamburger |
| 11 | Build `Footer` | Legal disclaimer, copyright, links |
| 12 | Build `PageHeader` | Title + breadcrumb component |
| 13 | Build root `layout.tsx` | Navbar + main content area + footer |
| 14 | Build `loading.tsx` | Global loading skeleton |
| 15 | Build `error.tsx` | Error boundary with retry |
| 16 | Build `not-found.tsx` | Styled 404 page |

### Phase A-3: API Integration Layer (Day 3-4)

| # | Task | Details |
|---|------|---------|
| 17 | Build `ApiClient` | Fetch wrapper with error handling and base URL |
| 18 | Define TypeScript types | All API response types matching backend schemas |
| 19 | Build API functions | `searchByText`, `searchByUpload`, `getCaseById`, `getSimilarCases`, `getSearchFilters`, `getAiExplanation` |
| 20 | Build Zod schemas | Runtime validation for API responses |
| 21 | Build TanStack Query hooks | `useSearchByText`, `useSearchByUpload`, `useCase`, `useSearchFilters` |
| 22 | Build Zustand stores | `search-store`, `compare-store`, `ui-store` |
| 23 | Build utility functions | `cn`, `format-date`, `format-score`, `verdict-color`, `court-utils`, `truncate` |

### Phase A-4: Shared Components (Day 4-5)

| # | Task | Details |
|---|------|---------|
| 24 | Build `VerdictBadge` | Color-coded pill based on verdict type |
| 25 | Build `CourtBadge` | Court identifier chip |
| 26 | Build `ConfidenceMeter` | Visual confidence bar/ring |
| 27 | Build `SimilarityScoreRing` | Animated circular progress (SVG + Framer Motion) |
| 28 | Build `DimensionScores` | Horizontal bar chart for 5 dimensions |
| 29 | Build `AnimatedCounter` | Number counting animation for stats |
| 30 | Build `GradientText` | Animated gradient heading text |
| 31 | Build `SpotlightCard` | Cursor-following spotlight effect card |
| 32 | Build `ShimmerCard` | Shimmer border animated card |
| 33 | Build `LegalDisclaimer` | Dismissable warning banner |
| 34 | Build `MultiSelect` | Multi-option dropdown (courts, types) |
| 35 | Build `DateRangePicker` | Year range selection with sliders |
| 36 | Build `CopyButton` | Copy-to-clipboard with toast feedback |

### Phase A-5: Landing Page (Day 5-6)

| # | Task | Details |
|---|------|---------|
| 37 | Build Hero section | Gradient background, animated heading, dual CTAs |
| 38 | Build Stats bar | Animated counters with scroll trigger |
| 39 | Build How It Works | 3-step process with staggered reveal |
| 40 | Build Features grid | Spotlight cards with descriptions |
| 41 | Assemble landing page | Compose all sections with scroll animations |

### Phase A-6: Search Page (Day 6-8)

| # | Task | Details |
|---|------|---------|
| 42 | Build `SearchModeToggle` | Tab-style text/upload toggle with animated underline |
| 43 | Build `TextSearchInput` | Textarea with live char counter and min-100 indicator |
| 44 | Build `FileUploadZone` | Drag-and-drop with visual states (idle, hover, selected, error) |
| 45 | Build `SimilarityModeSelect` | Combined/Factual/Legal radio group |
| 46 | Build `SearchFilters` | Collapsible filter panel with all filter controls |
| 47 | Build `SearchHistory` | Recent searches from localStorage |
| 48 | Assemble search page | Wire up stores, validation, navigation to results |

### Phase A-7: Results Page (Day 8-10)

| # | Task | Details |
|---|------|---------|
| 49 | Build `ResultsHeader` | Query summary, result count, action buttons |
| 50 | Build `ResultCard` | Full result card with score ring, dimensions, explanation, actions |
| 51 | Build `ResultSkeleton` | Loading skeleton matching card layout |
| 52 | Build `NoResults` | Empty state with suggestions |
| 53 | Build `ExportMenu` | Dropdown: PDF and JSON export |
| 54 | Build search progress overlay | Simulated step-by-step progress |
| 55 | Build floating Compare button | AnimatePresence button when cases selected |
| 56 | Assemble results page | Wire up mutations, handle loading/error/success states |
| 57 | Implement AI Explanation inline loading | "Get AI Explanation" button → inline shimmer → reveal |

### Phase A-8: Case Detail Page (Day 10-12)

| # | Task | Details |
|---|------|---------|
| 58 | Build `CaseHeader` | Title, court, date, verdict badge, confidence, actions |
| 59 | Build `CaseMetadataGrid` | Structured layout: parties, charges, sections |
| 60 | Build `CaseParties` | Petitioner vs Respondent display |
| 61 | Build `CaseArguments` | Tabbed or side-by-side arguments view |
| 62 | Build `CaseReasoning` | Court reasoning with headnotes highlighted |
| 63 | Build `CaseStatutes` | Cited statutes list |
| 64 | Build `CasePrecedents` | Cited precedents list |
| 65 | Build `CaseFullText` | Scrollable full judgment (dynamic import) |
| 66 | Build `CaseConfidence` | Per-field confidence indicators |
| 67 | Build `FindSimilarButton` | CTA that triggers similar case search |
| 68 | Assemble case detail page | Tabs, data fetching, prefetching on hover |

### Phase A-9: Compare Page (Day 12-13)

| # | Task | Details |
|---|------|---------|
| 69 | Build `ComparePanel` | Single case column in comparison |
| 70 | Build `CompareToolbar` | Field selector checkboxes |
| 71 | Build `CompareDiff` | Common field highlighting, divergent coloring |
| 72 | Assemble compare page | Read from store/URL params, responsive layout |

### Phase A-10: Polish & Testing (Day 13-16)

| # | Task | Details |
|---|------|---------|
| 73 | Responsive audit | Test all pages at mobile/tablet/desktop |
| 74 | Animation polish | Fine-tune timings, add reduced-motion support |
| 75 | Dark mode implementation | Full dark theme with toggle |
| 76 | Error state audit | Every API call has loading/error/empty states |
| 77 | Accessibility audit | Keyboard nav, ARIA labels, color contrast |
| 78 | Write unit tests | Utils, stores, API client |
| 79 | Write component tests | Key components (ResultCard, FileUpload, ScoreRing) |
| 80 | Write E2E tests | Happy paths for search, detail, compare |
| 81 | Performance audit | Lighthouse, bundle analysis, prefetch optimization |
| 82 | Docker setup | Dockerfile, docker-compose integration |

### Phase A-11: Integration Testing (Day 16-17)

| # | Task | Details |
|---|------|---------|
| 83 | Full stack smoke test | Frontend ↔ Backend ↔ Qdrant ↔ PostgreSQL |
| 84 | Test with real data | Upload real case PDFs, verify search quality |
| 85 | Test error scenarios | Backend down, slow responses, malformed data |
| 86 | Cross-browser testing | Chrome, Firefox, Safari, Edge |
| 87 | Final documentation | Update CLAUDE.md with frontend commands |

---

## 17. Future Expansion Hooks

The frontend architecture is designed to easily accommodate the remaining NyayaAI features:

### Route Group: `(future)/`

Pre-planned route stubs for Features 1, 2, and 4:

```
app/
├── (future)/
│   ├── courtroom/              # Feature 1: Simulated Courtroom
│   │   ├── page.tsx            # Upload case → multi-model deliberation
│   │   ├── session/[id]/
│   │   │   └── page.tsx        # View deliberation results
│   │   └── components/
│   │       ├── role-card.tsx           # Prosecution/Defense/etc. analysis display
│   │       ├── scoring-heatmap.tsx     # Model cross-evaluation heatmap
│   │       ├── consensus-meter.tsx     # Agreement visualization
│   │       ├── deliberation-timeline.tsx # Phase progression
│   │       └── synthesis-panel.tsx     # Judge's final synthesis
│   │
│   ├── assistant/              # Feature 2: Legal Research Assistant
│   │   ├── page.tsx            # Chat interface
│   │   └── components/
│   │       ├── chat-message.tsx        # Message bubble with citations
│   │       ├── citation-popover.tsx    # Inline statute/case reference
│   │       ├── context-panel.tsx       # Active case context sidebar
│   │       ├── confidence-indicator.tsx # Response confidence
│   │       └── source-list.tsx         # RAG source attribution
│   │
│   └── analytics/              # Feature 4: Outcome Tendency Analyzer
│       ├── page.tsx            # Statistical insights dashboard
│       └── components/
│           ├── outcome-distribution.tsx  # Pie/bar chart of verdicts
│           ├── sentence-distribution.tsx # Histogram of sentences
│           ├── factor-importance.tsx     # SHAP value visualization
│           ├── trend-chart.tsx          # Conviction rate over time
│           └── confidence-interval.tsx  # Statistical confidence display
```

### Sidebar Navigation (Ready for Expansion)

The `Navbar` component has placeholders for all four features:

```typescript
const navItems = [
  { label: "Similar Cases", href: "/search", icon: Search, active: true },
  { label: "Courtroom", href: "/courtroom", icon: Scale, disabled: true, badge: "Soon" },
  { label: "Assistant", href: "/assistant", icon: MessageSquare, disabled: true, badge: "Soon" },
  { label: "Analytics", href: "/analytics", icon: BarChart3, disabled: true, badge: "Soon" },
]
```

When each feature is ready, simply remove `disabled: true` and build the route.

### Shared Infrastructure Already in Place

These patterns scale to all features without modification:

| Pattern | Used By | Future Features |
|---------|---------|-----------------|
| API client + types | Similar Cases | All features (add new API functions) |
| TanStack Query | Search, Case Detail | Courtroom sessions, Chat, Analytics |
| Zustand stores | Search, Compare | Active case context, Chat history |
| Framer Motion system | Results, Landing | Deliberation timeline, Chat animations |
| shadcn components | All pages | All future pages |
| File upload | Search (PDF) | Courtroom (case upload), Assistant (context) |
| Export (PDF/JSON) | Results | Courtroom report, Analytics report |
| Legal disclaimer | Results | All features |
| Responsive layout | All pages | All future pages |

### WebSocket Readiness

The architecture anticipates WebSocket support for:
- **Courtroom**: Real-time deliberation progress (Phase 1 → 2 → 3 → 4)
- **Assistant**: Streaming chat responses
- **Search**: Real-time search progress (replacing simulated progress)

```typescript
// Future: lib/hooks/use-websocket.ts
// Will use native WebSocket or socket.io-client
// TanStack Query mutations will be replaced with WS subscriptions
// for real-time features
```

### Authentication Readiness

The layout and API client are structured to support auth when needed:
- Navbar has space for user avatar / login button
- API client can be extended with auth headers
- Protected routes via Next.js middleware

---

## Summary

This plan delivers a **production-quality, visually polished, and architecturally sound** frontend for NyayaAI's Similar Case Retrieval feature while laying the groundwork for all four planned features. The tech choices (Next.js 15, shadcn/ui, Framer Motion, Magic UI, TanStack Query) create a premium legal research experience that's fast, accessible, and extensible.

**Estimated timeline**: 17 working days for a complete Phase A implementation.

**Key architectural decisions**:
- App Router for server components and code splitting
- TanStack Query for server state (not Redux — overkill for this)
- Zustand for minimal client state (not Context — better DX)
- shadcn/ui for accessible, customizable base components
- Framer Motion + Magic UI for polished, professional animations
- Full TypeScript with Zod validation at API boundaries
