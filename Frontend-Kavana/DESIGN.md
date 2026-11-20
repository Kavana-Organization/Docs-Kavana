# Kavana Design System & UI/UX Architecture (`DESIGN.md`)

> **Product:** KAVANA — Sistem Bimbingan Online untuk Proyek dan Internship Mahasiswa (D4 Teknik Informatika, ULBI)  
> **PRD Reference:** `PRD_Rombak_Desain_UI_UX_KAVANA_v1.2.md` (Versi 1.2 · 17 Nov 2026 · Pemilik: Renzip)  
> **Codebase Scope:** `Frontend-Kavana/src/app`, `Frontend-Kavana/src/components`, `Frontend-Kavana/src/lib`  
> **Tech Stack:** Next.js 16 (App Router, React 19), Tailwind CSS v4 (`@theme inline`), Radix UI (`radix-ui`), Class Variance Authority (`cva`), Framer Motion, Recharts, Lucide React, Sonner

---

## 1. Visual Direction & Design Philosophy (`DS-1`, `DS-9`, `DS-10`)

### 1.1 Selected Visual Direction: *Institutional Academic Operations (Warm Slate & ULBI Blue)*
KAVANA serves 7 distinct academic roles (**Mahasiswa**, **Dosen Pembimbing**, **Kepala Prodi**, **Koordinator**, **Dosen Penguji**, **Administrator**, **Developer**) working with dense daily academic workflows: proposal review, 8-session logbook tracking, defense (`sidang`) scheduling, and post-defense revision verification.

To eliminate generic "AI slop" (purple/indigo gradients, glassmorphism blur everywhere, uniform 4-card KPI strips with fabricated trend arrows, and `rounded-2xl` nested cards), KAVANA adopts a **single, cohesive institutional operations direction**:

1. **Surface-First Hierarchy, Not Card-Inside-Card (`DS-5`, `DS-10`):**
   - Primary workspace sits on a warm neutral canvas (`--surface-page`).
   - Containers use a flat raised surface (`--surface-raised`) with a crisp `1px` border (`--line`), while table headers, input wells, and code blocks recess into `--surface-sunken`.
   - Shadows are never used for static card elevation; `--shadow-overlay` is reserved strictly for floating overlays (modals, sheets, popovers, dropdowns, and toasts).
2. **One Design System, Seven Task-Shaped Dashboards (`DS-9`, `DB-1..7`):**
   - Every role shares the exact same typography, semantic tokens, status badges, and primitive controls.
   - Visual differentiation comes from **information priority, layout structure, and density**:
     - *Mahasiswa (`DB-1`)*: Guided & spacious (`52px` rows), led by a "Langkah berikutnya" CTA block, a 7-stage horizontal stepper, and an 8-slot segmented logbook progress bar.
     - *Dosen (`DB-2`)*: High-density work inbox (`44px` compact rows), led by an oldest-first review queue (`Antrean Tinjauan Teratas`) with category filters before the supervised student table.
     - *Kaprodi (`DB-3`)*: Executive analytical summary pairing cohort distribution charts (with accessible companion data tables) and raw-data degradation lists for students needing attention.
     - *Koordinator (`DB-4`)*: Operational control center combining an active academic period calendar, incomplete supervisor assignment alerts, and defense schedule conflict detection.
     - *Penguji (`DB-5`)*: Distraction-free focus layout spotlighting today's/upcoming defense sessions with direct entry into the segmented evaluation form.
     - *Admin (`DB-6`)*: Utilitarian data management workspace with direct user/role filtering, search, and master data actions.
     - *Developer (`DB-7`)*: High-density technical observability console using `JetBrains Mono` strictly for latency, hashes, IPs, Redis keys, and JSON payloads.
3. **Authentic Campus Language & Canonical Status Glossary (`§5`, `§8`):**
   - All UI copy uses natural Indonesian academic terminology (`Bimbingan ke-3`, `Sidang Proyek 2`, `Logbook`, `Luaran Proyek`, `Berita Acara`).
   - Status labels follow the strict 5-term glossary without synonyms: **`Menunggu`**, **`Revisi`**, **`ACC`**, **`Ditolak`**, **`Selesai`** (plus operational state badges `Aktif` and `Terjadwal`), always rendered with **Color + Distinct Icon + Text (`A11Y-4`)**.
   - Dates use the standard Indonesian format **`"16 Nov 2026"`** in **WIB (`Asia/Jakarta`)**.

### 1.2 Architectural Rationale (Why This Direction Fits KAVANA)
- **Linear & Stripe Dashboard Principles:** High contrast between structural hairlines (`--line`) and tabular numeric data (`font-mono tabular-nums`) lets lecturers and coordinators scan 50+ student rows without visual fatigue.
- **Notion & GitHub Review Principles:** Collapsible grouped navigation (`264px` expanded / `72px` icon rail) and side-by-side / drawer review panels keep context visible while approving logbooks or revisions.
- **Zero Purple/Indigo Policy:** Replaced all legacy purple/violet hues (`258°`) with warm academic teal-slate (`195°`) and ULBI royal blue (`221°`), passing WCAG AA (`>= 4.5:1` text, `>= 3:1` UI boundaries).

---

## 2. Design Tokens (`DS-2..8`, `ENF-1..3`)

All tokens live in `src/app/globals.css` and are enforced by `scripts/slop-check.mjs` (which rejects raw `#hex`, arbitrary `rounded-[`, `rounded-2xl`, `rounded-3xl`, `backdrop-blur` outside overlays, `bg-gradient-to-`, `font-black`, and `shadow-[0_`).

### 2.1 Color System (`DS-2`)

| Semantic Token | Light Value (`H S% L%`) | Purpose & Usage |
| :--- | :--- | :--- |
| `--brand` / `--accent` | `hsl(221 83% 45%)` | Primary brand dominant action, active navigation indicator, focus ring |
| `--accent-soft` | `hsl(221 83% 45% / 0.08)` | Active nav fill, selected table row / item highlight |
| `--ctp-teal` | `hsl(201 89% 32%)` | Secondary accent (academic track badges, secondary series) |
| `--surface-page` | `hsl(210 40% 98%)` | Flat document `<body>` canvas (no grid or noise overlays) |
| `--surface-raised` | `hsl(0 0% 100%)` | Primary containers: cards, sticky header, sidebar, modals, sheets |
| `--surface-sunken` | `hsl(210 40% 96%)` | Recessed wells: table headers, form inputs, code blocks, filter bars |
| `--line` | `hsl(214 32% 91%)` | All `1px` borders, table row dividers, and section hairlines |
| `--text-strong` | `hsl(222 47% 11%)` | Headings, primary record names, key metric values (`> 15:1` contrast) |
| `--text-body` | `hsl(215 19% 35%)` | Body copy, table cells, descriptions (`> 7:1` contrast) |
| `--text-muted` | `hsl(215 18% 38%)` | Captions, timestamps, helper text, column headers (`>= 4.5:1` contrast) |
| `--status-success` | `hsl(161 90% 24%)` | Semantic status: `ACC`, `Selesai`, `Aktif` |
| `--status-warning` | `hsl(24 94% 32%)` | Semantic status: `Menunggu`, `Revisi` |
| `--status-danger` | `hsl(0 74% 40%)` | Semantic status: `Ditolak`, destructive actions, error alerts |
| `--status-info` | `hsl(221 83% 45%)` | Semantic status: `Terjadwal`, informational callouts |

### 2.2 Typography Scale (`DS-3`)

Self-hosted via `next/font/google` in `src/app/layout.js`:
- **UI & Heading Font (`--font-sans`):** `Public Sans` (`400`, `500`, `600`, `700`) — neutral, high x-height institutional grotesque optimized for dense data tables and Indonesian multi-syllable words.
- **Monospace Font (`--font-mono`):** `JetBrains Mono` (`400`, `500`, `600`) — used strictly for NPM (`9 digits`), NIDN, session numbers (`3/8`), dates/timestamps, latency (`14 ms`), IP addresses, Redis keys, and JSON payloads. All numeric columns enforce `font-variant-numeric: tabular-nums`.

| Token | Size | Tailwind Class | Line Height | Usage |
| :--- | :--- | :--- | :--- | :--- |
| `--font-size-12` | `12px` (`0.75rem`) | `text-xs` | `1.4` | Badges, timestamps, table metadata, helper hints, group headers |
| `--font-size-14` | `14px` (`0.875rem`) | `text-sm` | `1.5` | Default body text, table cells, form inputs, navigation links |
| `--font-size-16` | `16px` (`1rem`) | `text-base` | `1.5` | Card titles, dialog titles, primary record titles |
| `--font-size-20` | `20px` (`1.25rem`) | `text-xl` | `1.25` | `PageHeader` `h1` title, primary section headings |
| `--font-size-28` | `28px` (`1.75rem`) | `text-2xl` / `text-[28px]` | `1.2` | Metric strip primary numbers, verification document header |
| `--font-size-36` | `36px` (`2.25rem`) | `text-4xl` | `1.15` | Public landing hero display heading |

### 2.3 Spacing Scale (`DS-4` — Strict 4/8px System)

| Token | Value | Tailwind Equivalent | Typical Usage |
| :--- | :--- | :--- | :--- |
| `--space-1` | `4px` (`0.25rem`) | `p-1` / `gap-1` | Tight inline badge gaps, icon-to-label micro spacing |
| `--space-2` | `8px` (`0.5rem`) | `p-2` / `gap-2` | Button groups, compact cell padding, nav item spacing |
| `--space-3` | `12px` (`0.75rem`) | `p-3` / `gap-3` | Input horizontal padding (`12px`), table cell padding, compact cards |
| `--space-4` | `16px` (`1rem`) | `p-4` / `gap-4` | Mobile page padding (`16px`), grid gap between cards |
| `--space-6` | `24px` (`1.5rem`) | `p-6` / `gap-6` | Desktop page padding (`24px`), vertical section rhythm (`space-y-6`) |
| `--space-8` | `32px` (`2rem`) | `p-8` / `gap-8` | Major layout pane separation, empty/error state vertical padding |
| `--space-12` | `48px` (`3rem`) | `py-12` | Public landing section padding |
| `--space-16` | `64px` (`4rem`) | `py-16` | Public hero vertical breathing room |

### 2.4 Border Radius & Elevation (`DS-5`)

- **Control Radius (`--radius-control` = `8px` / `rounded-lg`):** Buttons, Inputs, Selects, Tabs triggers, Sidebar links (`rounded-md` `6px` for compact inline `StatusBadge` pills).
- **Container Radius (`--radius-container` = `12px` / `rounded-xl`):** Cards, Tables, Modals (`Dialog`), Drawers (`Sheet`), Dropdowns, Toasts (`Sonner`), Empty/Error states.
- **Elevation Hierarchy:**
  1. **Level 0 (Canvas):** `bg-[var(--surface-page)]`
  2. **Level 1 (Container / Card):** `bg-[var(--surface-raised)] border border-[var(--line)] shadow-none`
  3. **Level -1 (Recessed Well):** `bg-[var(--surface-sunken)] border border-[var(--line)]`
  4. **Level 2 (Floating Overlay Only):** `bg-[var(--surface-raised)] border border-[var(--line)] shadow-overlay` (`0 8px 24px hsl(222 47% 11% / 0.12)`)

### 2.5 Iconography (`DS-6`)

- **Library:** `lucide-react` exclusively across all 7 roles.
- **Stroke & Sizing:** `strokeWidth={1.75}`; `16px` (`h-4 w-4`) inside buttons/headers, `18px` (`h-[18px] w-[18px]`) in sidebar navigation, `14px` (`h-3.5 w-3.5`) inside `StatusBadge`.
- **Anti-Slop Rule:** Zero emoji icons (`🚀`, `👋`, `✅`) anywhere in UI headings, greetings, or navigation.

---

## 3. Core Component Catalog (`DS-7`, `A11Y-1..6`)

| Component | File | Key Specifications & Accessibility |
| :--- | :--- | :--- |
| **1. `Button`** | `src/components/ui/button.jsx` | Default height `40px` (`h-10`), compact `32px` (`h-8` / `size="sm"` `h-9`), mobile touch target `>= 44px` (`[@media(pointer:coarse)]:min-h-11`). Variants: `default`, `secondary`, `outline`, `ghost`, `destructive`, `link`. Radius `8px` (`rounded-lg`), `150ms` transition, no hover lift (`hover:-translate-y` banned). |
| **2. `Input` & `Field`** | `src/components/ui/input.jsx`, `src/components/ui/field.jsx` | Height `40px` (`h-10`), mobile `44px`, radius `8px` (`rounded-lg`). `Field` automatically binds `<label htmlFor={id}>`, `aria-describedby` (hint + error), and `aria-invalid` via React `useId()`. |
| **3. `Select`** | `src/components/ui/select.jsx` | Default height `40px` (`h-10`), compact `32px` (`size="sm"` `h-8`), mobile `44px`. Popover uses `rounded-xl border-[var(--line)] shadow-overlay`. |
| **4. `Table`** | `src/components/ui/table.jsx` | Supports `density="comfortable"` (`52px` rows) and `density="compact"` (`44px` rows), sticky header (`sticky top-0 z-10 bg-[var(--surface-sunken)]`), right-aligned action columns, `stackOnMobile` card-stacking below `640px`, and `.table-scroll-container` horizontal scroll shadow indicator (`DIM-1`). |
| **5. `StatusBadge`** | `src/components/shared/status-badge.jsx` | Enforces **Color + Distinct Icon + Text (`A11Y-4`)** across the §5 glossary: `Menunggu` (`Clock` + amber), `Revisi` (`RotateCcw` + amber), `ACC` (`CheckCircle2` + emerald), `Ditolak` (`XCircle` + red), `Selesai` (`CheckCircle2` + emerald), `Terjadwal` (`Calendar` + blue). |
| **6. `Tabs`** | `src/components/ui/tabs.jsx` | Recessed list `bg-[var(--surface-sunken)] rounded-lg p-1`, active trigger `bg-[var(--surface-raised)] rounded-md`, full keyboard arrow navigation and `44px` coarse-pointer target. |
| **7. `Dialog` & `Sheet`** | `src/components/ui/dialog.jsx`, `src/components/ui/sheet.jsx` | Focus-trapped modal (`Dialog`) and slide-over drawer (`Sheet`), `Escape` to close, and deterministic focus restoration to trigger (`DialogFocusCapture` / `SheetFocusCapture`, `A11Y-6`). |
| **8. `Sonner` (`Toaster`)** | `src/components/ui/sonner.jsx` | `12px` radius (`rounded-xl`), `border-[var(--line)]`, `shadow-overlay`, and `4px` semantic left border (`border-l-4`). |
| **9. `EmptyState`, `ErrorState`, `ForbiddenState`** | `src/components/shared/empty-state.jsx` | Covers `ST-1` states: dashed empty state with contextual CTA, inline `ErrorState` (`role="alert"`) with retry button, and `ForbiddenState` for unauthorized role access. |
| **10. `LoadingState` & `Skeleton`** | `src/components/shared/loading-state.jsx` | Accessible skeleton loader (`role="status" aria-live="polite"`) with shape-matched variants: `dashboard`, `table`, and `form`. |
| **11. `Breadcrumb` & `PageHeader`** | `src/components/layout/dashboard-layout.jsx`, `src/components/layout/page-header.jsx` | Sticky topbar renders `Role / Page` `<nav aria-label="Breadcrumb">`. `PageHeader` renders a flat `20px` (`text-xl font-semibold`) `h1` with optional action slot and zero duplicate eyebrows. |

---

## 4. App Shell & Navigation (`§6.2`, `§6.6`)

- **Desktop Sidebar (`DesktopSidebar`):**
  - Expanded width: **`264px`** (`w-[264px]`); Collapsed icon-rail width: **`72px`** (`w-[72px]`).
  - Viewport height: `h-screen h-[100dvh]` (`§6.6`).
  - Toggle button (`>= 44px` target) + keyboard shortcut **`Ctrl+B` / `Cmd+B`**, with collapsed state persisted in `localStorage` (`kavana-sidebar-collapsed`).
  - Grouped accordion sections (`src/lib/menu-groups.js`) over `MENU_CONFIG`: parent group header highlights when any child route is active (`NAV-4`), and collapsed rail items expose **keyboard-focusable Radix Tooltips (`A11Y-5`)** plus **Popover flyout submenus (`NAV-3`)**.
- **Mobile & Tablet Drawer (`MobileSidebar`):**
  - Off-canvas Radix `Sheet` (`side="left"`), auto-closes on link navigation, focus-trapped (`NAV-5`).
- **Sticky Header (`56px` / `h-14`):**
  - Left: Mobile drawer trigger + `Role / Page` Breadcrumb.
  - Right: Quick Search (`Ctrl/Cmd+K` dialog jumping to role pages, `NAV-8`), Multi-Role Switcher for accounts with multiple roles (`NAV-7`), Date in WIB (`xl`), Notifications dropdown (`44px` target), Theme toggle, and User profile dropdown.
- **Viewport & Layout Dimensions (`§6.6`):**
  - Content max-width: `1280px` (`max-w-[1280px]`); tables and calendars expand full-width within the container.
  - Page padding: `24px` desktop (`lg:px-6 lg:py-6`), `16px` mobile (`px-4 py-4`).
  - Tested across all 6 required viewports: **`1366x768`**, **`1440x900`**, **`1920x1080`**, **`1024x768`**, **`768x1024`**, and **`390x844`** (plus `320px` narrow mobile guard) with zero body horizontal overflow (`DIM-1`).
