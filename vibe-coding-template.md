# Vibe Coding Pre-Build Template
### 6 Documents to Prepare Before You Write a Single Line of Code

> **How to use this template:**
> 1. Fill in every section marked `[FILL IN]`
> 2. Delete placeholder text and instructions once done
> 3. Feed all 6 completed documents to your AI coding tool
> 4. Use the Master Prompt at the end to kick off the build

---

# DOCUMENT 1 — Product Requirements Document (PRD)

> **Purpose:** Tells the AI *what* you're building — the idea, users, problems, and features.

---

## App Overview

| Field | Your Answer |
|---|---|
| **App Name** | [FILL IN] |
| **One-Line Description** | [FILL IN — e.g., "A habit tracker that sends WhatsApp reminders"] |
| **App Category** | [FILL IN — e.g., SaaS / Mobile App / Internal Tool / Marketplace] |
| **Platform** | [FILL IN — e.g., Web, iOS, Android, Both] |

---

## Target Users

**Primary User:**
[FILL IN — describe in 2–3 sentences. Who are they? What's their job/context? How tech-savvy?]

**Secondary User (if any):**
[FILL IN or delete]

**User Personas:**

| Persona | Age Range | Goal | Pain Point |
|---|---|---|---|
| [Name] | [Range] | [What they want to achieve] | [What frustrates them today] |
| [Name] | [Range] | [What they want to achieve] | [What frustrates them today] |

---

## Problem Statement

**Current Situation:**
[FILL IN — What does the user do today without your app? What tools/workarounds do they use?]

**Core Problem:**
[FILL IN — What is the single biggest pain point you're solving?]

**Why Existing Solutions Fall Short:**
[FILL IN — Why don't current apps/tools solve this well?]

---

## User Roles

| Role | Description | Permissions |
|---|---|---|
| [e.g., Admin] | [What they do] | [What they can access/control] |
| [e.g., Regular User] | [What they do] | [What they can access/control] |
| [e.g., Viewer] | [What they do] | [What they can access/control] |

---

## Core Features (MVP)

List only what must be in Version 1. Be ruthless — if it's not essential for launch, move it to V2.

| # | Feature | Description | Priority |
|---|---|---|---|
| 1 | [Feature name] | [What it does, briefly] | Must Have |
| 2 | [Feature name] | [What it does, briefly] | Must Have |
| 3 | [Feature name] | [What it does, briefly] | Must Have |
| 4 | [Feature name] | [What it does, briefly] | Should Have |
| 5 | [Feature name] | [What it does, briefly] | Nice to Have |

---

## User Stories

Format: *As a [user role], I want to [action] so that [outcome].*

```
1. As a [role], I want to [action] so that [outcome].
2. As a [role], I want to [action] so that [outcome].
3. As a [role], I want to [action] so that [outcome].
4. As a [role], I want to [action] so that [outcome].
5. As a [role], I want to [action] so that [outcome].
```

---

## Success Metrics

How will you know the app is working?

| Metric | Target |
|---|---|
| [e.g., User signups in Month 1] | [e.g., 100 users] |
| [e.g., Task completion rate] | [e.g., >60%] |
| [e.g., Daily active users] | [e.g., 30% of signups] |
| [e.g., Retention at Day 7] | [e.g., >40%] |

---

## Out of Scope (V1)

Features you are intentionally NOT building in Version 1:

- [ ] [Feature — e.g., Mobile app (web only for now)]
- [ ] [Feature — e.g., Team/multi-user workspaces]
- [ ] [Feature — e.g., API access for third parties]
- [ ] [Feature — e.g., Advanced analytics dashboard]
- [ ] [Feature — add more]

---
---

# DOCUMENT 2 — Technical Requirements Document (TRD)

> **Purpose:** Tells the AI *how* to build it — stack, architecture, APIs, auth, deployment.

---

## Tech Stack

| Layer | Choice | Reason |
|---|---|---|
| **Frontend** | [e.g., Next.js 14, React, Vue] | [Why this choice] |
| **Backend** | [e.g., Node.js/Express, FastAPI, Supabase Functions] | [Why this choice] |
| **Database** | [e.g., PostgreSQL via Supabase, MongoDB, PlanetScale] | [Why this choice] |
| **Auth** | [e.g., Supabase Auth, Clerk, NextAuth, Firebase Auth] | [Why this choice] |
| **File Storage** | [e.g., Supabase Storage, Cloudflare R2, AWS S3] | [Why this choice] |
| **Hosting/Deploy** | [e.g., Vercel, Render, Railway, Fly.io] | [Why this choice] |
| **CSS/Styling** | [e.g., Tailwind CSS + shadcn/ui] | [Why this choice] |

---

## Architecture Overview

**Architecture Pattern:**
[FILL IN — e.g., Monolithic / Microservices / Serverless / JAMstack]

**High-Level Flow:**
```
[Describe in plain text how data flows through your app]
Example:
User → Next.js Frontend → API Routes → Supabase (DB + Auth + Storage)
                       → External APIs (Stripe, OpenAI, etc.)
```

---

## Authentication & Authorization

**Auth Method:** [FILL IN — e.g., Email/Password, Google OAuth, Magic Link]

**Session Handling:** [FILL IN — e.g., JWT tokens, Supabase sessions, cookies]

**Authorization Rules:**
- [Role] can [action]
- [Role] can [action]
- [Role] CANNOT [action]

---

## External APIs & Integrations

| Service | Purpose | API/SDK |
|---|---|---|
| [e.g., Stripe] | [e.g., Payments & subscriptions] | [e.g., Stripe SDK v3] |
| [e.g., OpenAI] | [e.g., AI content generation] | [e.g., OpenAI API gpt-4o] |
| [e.g., Resend] | [e.g., Transactional email] | [e.g., Resend SDK] |
| [e.g., Twilio] | [e.g., SMS notifications] | [e.g., Twilio REST API] |

---

## Environment Variables Needed

```env
# Auth
AUTH_SECRET=
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=

# Payments
STRIPE_SECRET_KEY=
STRIPE_WEBHOOK_SECRET=
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=

# AI
OPENAI_API_KEY=

# Email
RESEND_API_KEY=

# App
NEXT_PUBLIC_APP_URL=
```

---

## Performance Requirements

| Requirement | Target |
|---|---|
| Page load time | [e.g., < 2 seconds] |
| API response time | [e.g., < 500ms] |
| Uptime SLA | [e.g., 99.9%] |
| Concurrent users (MVP) | [e.g., up to 500] |

---

## Security Requirements

- [ ] All API routes protected by auth middleware
- [ ] Row-level security (RLS) enabled on database
- [ ] Input sanitization and validation on all forms
- [ ] Environment variables never exposed to client
- [ ] HTTPS enforced everywhere
- [ ] Rate limiting on auth endpoints
- [ ] [Add any app-specific security rules]

---
---

# DOCUMENT 3 — App Flow Document

> **Purpose:** Tells the AI every screen, every action, and every state — so it never has to guess.

---

## Full Screen/Page List

| Screen | Route | Description | Auth Required? |
|---|---|---|---|
| Landing Page | `/` | Marketing page with CTA | No |
| Sign Up | `/signup` | New user registration | No |
| Sign In | `/login` | Existing user login | No |
| Dashboard | `/dashboard` | Main user home | Yes |
| [Screen] | `/[route]` | [Description] | [Yes/No] |
| [Screen] | `/[route]` | [Description] | [Yes/No] |
| Settings | `/settings` | User profile & preferences | Yes |
| Billing | `/billing` | Subscription management | Yes |
| 404 Page | `*` | Not found fallback | No |

---

## User Journeys

### Journey 1: New User Onboarding

```
1. User lands on Landing Page
   → Sees headline, value prop, and "Get Started" CTA button

2. Clicks "Get Started"
   → Redirected to /signup

3. Fills in name, email, password → Clicks "Create Account"
   → Account created
   → Verification email sent (if applicable)
   → Redirected to /onboarding

4. Onboarding Step 1: [First setup step]
   → User fills in [fields]
   → Clicks "Next"

5. Onboarding Step 2: [Second setup step]
   → User fills in [fields]
   → Clicks "Finish Setup"
   → Redirected to /dashboard

6. Dashboard loads
   → Shows empty state with guided prompt to [first action]
```

---

### Journey 2: Core Feature Flow — [Feature Name]

```
1. User is on Dashboard
   → Clicks [button/link]

2. [Next screen] loads
   → User sees [content]
   → User does [action]

3. [Result screen or state]
   → Success: [what happens]
   → Error: [what happens]
```

---

### Journey 3: [Another Key Flow — e.g., Payment/Upgrade]

```
1. [Step 1]
2. [Step 2]
3. [Step 3]
   → Success state: [describe]
   → Error state: [describe]
```

---

## States for Each Key Screen

### Dashboard

| State | What User Sees |
|---|---|
| **Loading** | Skeleton loaders for cards and table |
| **Empty** | Illustration + "Create your first [X]" CTA button |
| **Populated** | [List of items / table / cards with data] |
| **Error** | "Something went wrong. Try refreshing." + retry button |

### [Screen Name]

| State | What User Sees |
|---|---|
| **Loading** | [Describe] |
| **Empty** | [Describe] |
| **Populated** | [Describe] |
| **Error** | [Describe] |

---

## Navigation Structure

```
Top Navigation (logged in):
  Logo → /dashboard
  [Nav Item] → /[route]
  [Nav Item] → /[route]
  Notifications icon
  User avatar → dropdown (Profile, Settings, Sign Out)

Sidebar (if applicable):
  [Section] → /[route]
  [Section] → /[route]
  [Section] → /[route]

Footer (public pages):
  About | Privacy Policy | Terms | Contact
```

---
---

# DOCUMENT 4 — UI/UX Design Brief

> **Purpose:** Tells the AI exactly how the app should look and feel — no guessing on style.

---

## Design Identity

| Field | Your Choice |
|---|---|
| **Design Style** | [e.g., Clean & minimal / Bold & modern / Playful / Corporate professional] |
| **Personality** | [e.g., Trustworthy, Fast, Friendly, Premium, Technical] |
| **Inspiration Apps** | [e.g., Linear, Notion, Vercel dashboard, Stripe] |
| **Target Feel** | [e.g., "Like Linear meets Notion — clean, fast, no clutter"] |

---

## Color Palette

| Role | Hex Code | Usage |
|---|---|---|
| **Primary** | `#[FILL]` | Main CTAs, active states, highlights |
| **Secondary** | `#[FILL]` | Supporting accents |
| **Background** | `#[FILL]` | Page background (e.g., white or near-black) |
| **Surface** | `#[FILL]` | Card backgrounds, panels |
| **Border** | `#[FILL]` | Dividers, input borders |
| **Text Primary** | `#[FILL]` | Headings, important text |
| **Text Secondary** | `#[FILL]` | Labels, subtitles, metadata |
| **Success** | `#[FILL]` | Success toasts, confirmations |
| **Warning** | `#[FILL]` | Warnings |
| **Error** | `#[FILL]` | Errors, destructive actions |

**Dark Mode:** [Yes / No / Optional]

---

## Typography

| Role | Font | Weight | Size |
|---|---|---|---|
| **Display / Hero** | [e.g., Inter] | 700 | 48–64px |
| **H1** | [e.g., Inter] | 700 | 32–40px |
| **H2** | [e.g., Inter] | 600 | 24–28px |
| **H3** | [e.g., Inter] | 600 | 18–20px |
| **Body** | [e.g., Inter] | 400 | 14–16px |
| **Small / Label** | [e.g., Inter] | 400–500 | 12px |
| **Code** | [e.g., JetBrains Mono] | 400 | 13–14px |

---

## Component Style

| Component | Style Direction |
|---|---|
| **Buttons (Primary)** | [e.g., Rounded-lg, solid fill, no shadow, 40px height] |
| **Buttons (Secondary)** | [e.g., Outlined, same border-radius] |
| **Cards** | [e.g., Subtle border, 8px radius, no shadow OR soft shadow] |
| **Inputs** | [e.g., Outlined, 40px height, focus ring in primary color] |
| **Badges/Tags** | [e.g., Pill shape, soft background tints] |
| **Modals/Dialogs** | [e.g., Centered, backdrop blur, 12px radius] |
| **Tables** | [e.g., Minimal borders, alternating row tint or none] |
| **Toast/Alerts** | [e.g., Bottom-right position, icon + message] |
| **Navigation** | [e.g., Top bar fixed / Left sidebar collapsible] |

---

## Layout Rules

**Grid System:** [e.g., 12-column grid, max-width 1280px, 24px gutters]

**Spacing Scale:** [e.g., Tailwind default — 4px base unit]

**Page Padding:** [e.g., 24px mobile, 40px desktop]

**Dashboard Layout:**
```
[Describe the dashboard layout]
Example:
- Fixed top navbar (64px height)
- Collapsible left sidebar (240px expanded, 60px collapsed)
- Main content area with 32px padding
- Right panel optional for details/context
```

---

## Responsive Behavior

| Breakpoint | Layout Change |
|---|---|
| Mobile (< 640px) | [e.g., Single column, sidebar hidden behind hamburger menu] |
| Tablet (640–1024px) | [e.g., Two-column layout, sidebar collapsed] |
| Desktop (> 1024px) | [e.g., Full layout with expanded sidebar] |

---

## UX Principles

1. **Speed first** — [e.g., Optimistic UI updates, skeleton loaders, no full-page reloads]
2. **Minimal clicks** — [e.g., Key actions always within 2 clicks from dashboard]
3. **Clear feedback** — [e.g., Every action has a loading, success, and error state]
4. **Progressive disclosure** — [e.g., Show advanced options only when needed]
5. [Add your own principle]

---
---

# DOCUMENT 5 — Backend Schema Document

> **Purpose:** Defines every database table, column, relationship, and permission — the backbone of your app.

---

## Database Platform

**Database:** [e.g., PostgreSQL (via Supabase)]
**ORM/Query Layer:** [e.g., Prisma / Drizzle / Supabase JS client / raw SQL]

---

## Tables

### `users`
*Managed by auth provider — extend with a profiles table if needed.*

| Column | Type | Constraints | Notes |
|---|---|---|---|
| `id` | `uuid` | PRIMARY KEY | From auth provider |
| `email` | `text` | UNIQUE, NOT NULL | |
| `created_at` | `timestamptz` | DEFAULT now() | |
| `updated_at` | `timestamptz` | DEFAULT now() | |

---

### `profiles`
*Extended user data not stored by auth provider.*

| Column | Type | Constraints | Notes |
|---|---|---|---|
| `id` | `uuid` | PRIMARY KEY, FK → users.id | |
| `full_name` | `text` | | |
| `avatar_url` | `text` | | Stored in file storage |
| `plan` | `text` | DEFAULT 'free' | 'free' \| 'pro' \| 'enterprise' |
| `onboarding_complete` | `boolean` | DEFAULT false | |
| `created_at` | `timestamptz` | DEFAULT now() | |
| `updated_at` | `timestamptz` | DEFAULT now() | |

---

### `[main_entity]`
*[Describe what this table stores — e.g., "Stores the user's projects"]*

| Column | Type | Constraints | Notes |
|---|---|---|---|
| `id` | `uuid` | PRIMARY KEY, DEFAULT gen_random_uuid() | |
| `user_id` | `uuid` | NOT NULL, FK → users.id ON DELETE CASCADE | Owner |
| `name` | `text` | NOT NULL | |
| `description` | `text` | | Optional |
| `status` | `text` | DEFAULT 'active' | 'active' \| 'archived' |
| `metadata` | `jsonb` | | Flexible extra data |
| `created_at` | `timestamptz` | DEFAULT now() | |
| `updated_at` | `timestamptz` | DEFAULT now() | |

---

### `[second_entity]`
*[Describe — e.g., "Items within a project"]*

| Column | Type | Constraints | Notes |
|---|---|---|---|
| `id` | `uuid` | PRIMARY KEY, DEFAULT gen_random_uuid() | |
| `[parent_id]` | `uuid` | NOT NULL, FK → [parent_table].id ON DELETE CASCADE | |
| `user_id` | `uuid` | NOT NULL, FK → users.id | For RLS |
| `title` | `text` | NOT NULL | |
| `body` | `text` | | |
| `position` | `integer` | DEFAULT 0 | For ordering |
| `created_at` | `timestamptz` | DEFAULT now() | |
| `updated_at` | `timestamptz` | DEFAULT now() | |

---

### `subscriptions` *(if using Stripe)*

| Column | Type | Constraints | Notes |
|---|---|---|---|
| `id` | `uuid` | PRIMARY KEY | |
| `user_id` | `uuid` | FK → users.id ON DELETE CASCADE | |
| `stripe_customer_id` | `text` | UNIQUE | |
| `stripe_subscription_id` | `text` | UNIQUE | |
| `plan` | `text` | NOT NULL | 'pro' \| 'enterprise' |
| `status` | `text` | NOT NULL | 'active' \| 'canceled' \| 'past_due' |
| `current_period_start` | `timestamptz` | | |
| `current_period_end` | `timestamptz` | | |
| `created_at` | `timestamptz` | DEFAULT now() | |

---

## Relationships Diagram

```
users (1) ──── (1) profiles
users (1) ──── (N) [main_entity]
[main_entity] (1) ──── (N) [second_entity]
users (1) ──── (1) subscriptions
```

---

## Indexes

```sql
-- Speed up lookups by user_id on all major tables
CREATE INDEX idx_[main_entity]_user_id ON [main_entity](user_id);
CREATE INDEX idx_[second_entity]_[parent]_id ON [second_entity]([parent_id]);
CREATE INDEX idx_[second_entity]_user_id ON [second_entity](user_id);
CREATE INDEX idx_subscriptions_user_id ON subscriptions(user_id);
CREATE INDEX idx_subscriptions_stripe_customer ON subscriptions(stripe_customer_id);
```

---

## Row-Level Security (RLS) Policies

```sql
-- profiles: users can only read/write their own profile
CREATE POLICY "Users can view own profile" ON profiles
  FOR SELECT USING (auth.uid() = id);

CREATE POLICY "Users can update own profile" ON profiles
  FOR UPDATE USING (auth.uid() = id);

-- [main_entity]: users can only CRUD their own records
CREATE POLICY "Users can view own [main_entity]" ON [main_entity]
  FOR SELECT USING (auth.uid() = user_id);

CREATE POLICY "Users can insert own [main_entity]" ON [main_entity]
  FOR INSERT WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can update own [main_entity]" ON [main_entity]
  FOR UPDATE USING (auth.uid() = user_id);

CREATE POLICY "Users can delete own [main_entity]" ON [main_entity]
  FOR DELETE USING (auth.uid() = user_id);
```

---
---

# DOCUMENT 6 — Implementation Plan

> **Purpose:** The exact build sequence. AI follows this phase by phase — no skipping, no guessing order.

---

## Build Rules (Read Before Starting)

1. **Do not build Phase N+1 until Phase N is tested and confirmed working.**
2. After each phase, test manually before proceeding.
3. Never break existing working features when adding new ones.
4. Commit to version control at the end of each phase.

---

## Phase 0 — Project Setup

**Deliverables:**
- [ ] Repo created (GitHub/GitLab)
- [ ] Framework initialized (e.g., `npx create-next-app@latest`)
- [ ] Folder structure established (`/app`, `/components`, `/lib`, `/hooks`, `/types`)
- [ ] Tailwind CSS + shadcn/ui configured
- [ ] `.env.local` file created with all required variable names (values empty)
- [ ] ESLint + Prettier configured
- [ ] Git initial commit pushed

---

## Phase 1 — Database & Auth Setup

**Deliverables:**
- [ ] Supabase project created (or chosen DB platform configured)
- [ ] All tables created with correct schema
- [ ] All indexes applied
- [ ] RLS policies enabled and tested
- [ ] Auth provider configured (email/password + OAuth if needed)
- [ ] `profiles` table auto-created on user signup via database trigger
- [ ] Supabase client initialized in `/lib/supabase.ts`
- [ ] Auth helper functions written (`getUser`, `signIn`, `signOut`, `signUp`)

**Test:** Create a test user via Supabase dashboard. Confirm profile row is auto-created.

---

## Phase 2 — Authentication UI

**Deliverables:**
- [ ] `/signup` page — form with name, email, password
- [ ] `/login` page — form with email, password + "Forgot password" link
- [ ] `/forgot-password` page
- [ ] Auth middleware — redirect unauthenticated users from protected routes to `/login`
- [ ] Auth redirect — redirect authenticated users from `/login` to `/dashboard`
- [ ] Loading and error states on all auth forms

**Test:** Sign up, log out, log in, access `/dashboard` without auth (should redirect).

---

## Phase 3 — App Shell & Navigation

**Deliverables:**
- [ ] Top navigation bar (logo, nav links, user avatar dropdown)
- [ ] Sidebar (if applicable) with all nav items
- [ ] Dashboard layout wrapper component
- [ ] 404 page
- [ ] Loading skeleton component (reusable)
- [ ] Toast notification system configured

**Test:** Navigate all routes. Confirm nav highlights active route. Confirm sign out works.

---

## Phase 4 — Core Feature: [Feature Name]

**Deliverables:**
- [ ] [Specific UI component 1]
- [ ] [Specific UI component 2]
- [ ] API route: `POST /api/[entity]` — create
- [ ] API route: `GET /api/[entity]` — list
- [ ] API route: `PATCH /api/[entity]/[id]` — update
- [ ] API route: `DELETE /api/[entity]/[id]` — delete
- [ ] Empty state UI
- [ ] Loading state UI
- [ ] Error handling on all API calls

**Test:** Create, read, update, and delete a [entity]. Confirm data persists after refresh.

---

## Phase 5 — Core Feature: [Second Feature Name]

**Deliverables:**
- [ ] [List specific components and API routes]
- [ ] [Add all UI states]

**Test:** [Describe how to manually verify this feature works end-to-end]

---

## Phase 6 — Payments & Billing *(if applicable)*

**Deliverables:**
- [ ] Stripe account configured, products and prices created
- [ ] `POST /api/stripe/create-checkout` — create Stripe checkout session
- [ ] `POST /api/stripe/webhook` — handle Stripe events (subscription created, canceled, updated)
- [ ] `/billing` page — current plan, upgrade/downgrade button
- [ ] Feature gating — pro features locked for free users with upgrade prompt
- [ ] Webhook endpoint registered in Stripe dashboard

**Test:** Complete a test checkout with Stripe test card `4242 4242 4242 4242`. Confirm plan upgrades in DB.

---

## Phase 7 — User Settings & Profile

**Deliverables:**
- [ ] `/settings` page with profile edit form (name, avatar upload)
- [ ] Avatar upload to file storage
- [ ] Password change flow
- [ ] Account deletion option (with confirmation dialog)
- [ ] Email notification preferences (if applicable)

**Test:** Update name, upload avatar, change password. Confirm changes persist.

---

## Phase 8 — Polish & Edge Cases

**Deliverables:**
- [ ] All empty states have illustrations or helpful copy (not just blank screens)
- [ ] All loading states covered
- [ ] All error states handled gracefully (no raw error messages shown to users)
- [ ] Form validation on every form (client + server side)
- [ ] Responsive layout tested on mobile, tablet, desktop
- [ ] Favicon, Open Graph meta tags, page titles set on every route
- [ ] Console.log statements removed from production code

---

## Phase 9 — Testing

**Deliverables:**
- [ ] Core user journeys manually tested end-to-end
- [ ] Auth flows tested (signup, login, logout, password reset)
- [ ] Payment flows tested with Stripe test mode
- [ ] Mobile layout tested on real device or browser devtools
- [ ] Performance tested (Lighthouse score > 80)
- [ ] All broken links fixed
- [ ] No unhandled promise rejections in console

---

## Phase 10 — Deployment

**Deliverables:**
- [ ] Production environment variables set in hosting provider
- [ ] Domain connected (custom domain or platform subdomain)
- [ ] Deployed to production (Vercel / Render / Railway)
- [ ] Stripe webhook URL updated to production URL
- [ ] Final smoke test on production (sign up, core feature, payment)
- [ ] Error monitoring configured (e.g., Sentry)
- [ ] Analytics configured (e.g., PostHog, Plausible)

---
---

# MASTER PROMPT — Use This to Start Building

Copy this prompt, paste your 6 completed documents, and send to your AI coding tool:

```
I am going to build an app using AI coding tools.

Below are my 6 pre-build documents:
1. Product Requirements Document (PRD)
2. Technical Requirements Document (TRD)
3. App Flow Document
4. UI/UX Design Brief
5. Backend Schema Document
6. Implementation Plan

---

[PASTE ALL 6 DOCUMENTS HERE]

---

Before writing any code:
1. Read all documents carefully
2. Summarize what you understood about the app in 5–7 bullet points
3. Flag any gaps, contradictions, or missing details
4. Confirm the tech stack and architecture you will use
5. Identify which Phase from the Implementation Plan we will start with

Do NOT generate any code yet. Wait for my confirmation before starting Phase 0.
```

---

*Template by: Vibe Coding Pre-Build Framework*
*Fill in all [FILL IN] fields and replace all placeholder text before use.*
