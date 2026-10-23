Analyzed README.md:L1-333

You can copy and paste the complete content below directly into the `README.md` editor in your GitHub repository:

# GridVision — Football Strategy & Tactical Analytics Platform

[![Live Production](https://img.shields.io/badge/Live_Site-https%3A%2F%2Ffp--project--v1--10.vercel.app-emerald?style=for-the-badge&logo=vercel)](https://fp-project-v1-10.vercel.app)
[![Next.js](https://img.shields.io/badge/Next.js-16.3.3-black?style=for-the-badge&logo=next.js)](https://nextjs.org/)
[![React](https://img.shields.io/badge/React-19.0-blue?style=for-the-badge&logo=react)](https://react.dev/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.7-blue?style=for-the-badge&logo=typescript)](https://www.typescriptlang.org/)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-v4-38bdf8?style=for-the-badge&logo=tailwindcss)](https://tailwindcss.com/)
[![Drizzle ORM](https://img.shields.io/badge/Drizzle_ORM-0.45-C5F74F?style=for-the-badge&logo=drizzle)](https://orm.drizzle.team/)

> **Academic Project Submission**  
> **Course:** BSc Applied Mathematics and Computing (AMC) — Semester 1  
> **Subject:** Foundations of Programming (FP)  
> **Project Developers:** **Paavani** & **Ria**  
> **Live Web Application:** [https://fp-project-v1-10.vercel.app](https://fp-project-v1-10.vercel.app)

---

## 1. Project Overview

**GridVision** is a full-stack football tactical analytics, player intelligence, and match calendar platform. Combining quantitative modeling (an association football Elo rating model and a probabilistic decision network) with an interactive web interface, GridVision allows users to explore head-to-head match simulations, browse player career records, design custom 11-player lineups, and evaluate tactical setups.

> [!NOTE]  
> All match predictions, win probabilities, and tactical suggestions provided by the platform are algorithmic model estimates intended for analytical and academic study, not guaranteed outcomes.

---

## 2. Core Features & System Modules

### 1. Dynamic Team Elo Ratings & Matchup Predictor (`/`, `/overview`)
- **Mathematical Elo Model:** Calculates club and national team ratings ($K = 32$, Home Advantage $+100$, margin-of-victory goal difference scaling $G$).
- **Head-to-Head Simulation:** Computes estimated win, draw, and loss probabilities with interactive team swap controls.
- **Context-Aware Modes:** Dynamically adapts labels, Elo rankings, and strength bars between domestic club leagues (Premier League, La Liga, Serie A) and international tournaments (FIFA World Cup).
- **Interactive Visualizations:** Responsive Recharts horizontal Elo leaderboards and Attack vs. Defense scatter plots with squad style inspection panels.

### 2. Player Intelligence & Career Profiles (`/players`, `/players/[id]`)
- **Curated Player Profiles:** Career records covering appearances, goals, and assists as of October 2026.
- **Filtering & Search:** Filter by Position Group (FWD, MID, DEF, GK), career status (`ACTIVE`, `RETIRED`, `UNCERTAIN`), and club or nationality.
- **Rating Clarity:** Clear tooltips and explanations for Overall (`OVR`) and Potential (`POT`) ratings.

### 3. Interactive Strategy Builder & Pitch Simulator (`/strategy`)
- **Formation Pitch Canvas:** Interactive 11-player tactical setup supporting standard formations (4-4-2, 4-3-3, 3-5-2, 3-4-3, 5-3-2, 4-5-1).
- **Dream Team Mode:** Populates a top-rated XI for any selected competition.
- **Tactical Evaluation:** Users adjust their lineup and click *"Evaluate Strategy"* to receive automated shape, tempo, pressing, and key-player feedback.
- **Personal Strategy Management:** Authenticated users can save, rename, duplicate, update, and manage persistent strategy setups.

### 4. Probabilistic Shot Advisor (`/strategy`)
- **Decision Network:** Evaluates shot location (six-yard box, penalty spot, edge of box, long range), assist type, keeper positioning, and defensive pressure.
- **Model Estimates:** Computes estimated goal probability and possession retention percentages to advise whether a player should shoot or pass.

### 5. Multi-Competition Match Calendar (`/calendar`)
- **Fixture Schedule:** Multi-competition schedule covering Premier League, La Liga, Serie A, and FIFA World Cup fixtures.
- **Flexible Views:** Month, Week, and Upcoming list views with localized kickoff times and match status badges (`SCHEDULED`, `LIVE`, `FINISHED`, `POSTPONED`).
- **Personal Tactical Notes:** Authenticated users can link personal notes or watch party events to fixtures.

### 6. User Authentication & Data Isolation (`/login`, `/signup`, `/verify-email`)
- **Security:** Salted PBKDF2 password hashing (100,000 iterations via `node:crypto`) and signed HMAC session cookies.
- **Email Verification:** Integrates with Resend to dispatch single-use, 1-hour expiration verification tokens with rate limiting and cooldown guards.
- **Data Isolation:** User-level isolation ensures saved strategies and personal calendar notes remain private to each account.

---

## 3. Technology Stack

| Layer | Technologies | Purpose |
| :--- | :--- | :--- |
| **Frontend Framework** | **Next.js 16.3.3** (App Router), **React 19** | Full-stack React framework with Server Components & Server Actions |
| **Language** | **TypeScript 5.7.3** | Strict compile-time type safety across all components and models |
| **Styling & Design** | **Tailwind CSS v4**, `@tailwindcss/postcss`, `tw-animate-css` | Modern styling with dark-mode aesthetic |
| **UI Components** | **Base UI** (`@base-ui/react`), **Lucide React** (`lucide-react`) | Accessible primitives, dropdown selects, and iconography |
| **Data Visualization** | **Recharts 3.8.0** | Responsive BarChart, ScatterPlot, and metric comparison charts |
| **Database & ORM** | **Drizzle ORM 0.45.2**, **PostgreSQL (`pg` 8.23.0)** | Type-safe PostgreSQL schema with Neon serverless connection pooling |
| **Resilient Fallback** | **Local JSON Store (`.data/`)** | Zero-config automatic local file fallback when running without a remote database |
| **Email Service** | **Resend REST API** | Transactional account verification emails |
| **Deployment** | **Vercel** | Global Edge Network production hosting with automated CI/CD builds |

---

## 4. System Architecture & Database Schema

GridVision utilizes a 7-table relational schema managed via Drizzle ORM (`lib/db/schema.ts`):

```mermaid
erDiagram
    USERS ||--o{ CALENDAR_EVENTS : "creates"
    USERS ||--o{ SAVED_STRATEGIES : "owns"
    USERS ||--o{ EMAIL_VERIFICATION_TOKENS : "receives"
    CALENDAR_EVENTS ||--o{ EVENT_SHARES : "shares with"
    FIXTURES ||--o{ CALENDAR_EVENTS : "links to"

    USERS {
        serial id PK
        text email UK
        text name
        text password_hash
        text salt
        boolean email_verified
        timestamp email_verified_at
        timestamp created_at
    }

    EMAIL_VERIFICATION_TOKENS {
        serial id PK
        text email
        text token UK
        timestamp expires_at
        timestamp created_at
    }

    FIXTURES {
        text id PK
        text competition
        text season
        text round
        text home_team
        text away_team
        integer home_score
        integer away_score
        text status
        timestamp kickoff_time
        text venue
        text dedup_key UK
    }

    CALENDAR_EVENTS {
        text id PK
        integer user_id FK
        text title
        text description
        text event_type
        timestamp start_date
        timestamp end_date
        boolean all_day
        text linked_fixture_id FK
        text visibility
    }

    EVENT_SHARES {
        serial id PK
        text event_id FK
        integer shared_with_user_id FK
        text permission
    }

    SYNC_LOGS {
        serial id PK
        text competition
        text status
        integer fixtures_count
        timestamp timestamp
    }

    SAVED_STRATEGIES {
        text id PK
        integer user_id FK
        text name
        text notes
        text mode
        text season
        text team_name
        text formation
        text lineup
        text tactical_plan
        text verdict
        timestamp updated_at
    }
```

---

## 5. Project Directory Structure

```text
fp-project-v1-10/
├── app/                          # Next.js App Router (Pages, Routes & Server Actions)
│   ├── actions/                  # Server Actions (auth.ts, strategy.ts, calendar.ts)
│   ├── api/                      # REST API route handlers (elo/, strategy/)
│   ├── calendar/                 # Calendar & match center page
│   ├── login/                    # Login page with PBKDF2 verification
│   ├── overview/                 # Clubs & leagues Elo leaderboard and charts
│   ├── players/                  # Player Explorer and player profile pages
│   │   └── [id]/                 # Dynamic player career profile route
│   ├── signup/                   # User registration page
│   ├── strategy/                 # Strategy Builder & tactical pitch canvas
│   ├── verify-email/             # One-time token verification page
│   ├── layout.tsx                # Root layout with SeasonProvider and global frame
│   └── page.tsx                  # Home / Dashboard landing page
├── components/                   # Reusable React UI components
│   ├── calendar/                 # Month, week, upcoming, and fixture detail modals
│   ├── strategy/                 # Strategy save, my-strategies, and confirm dialogs
│   ├── ui/                       # Base UI & Radix styled components (Card, Button, Select, etc.)
│   ├── app-frame.tsx             # Responsive desktop sidebar and mobile header
│   ├── matchup-selector.tsx      # Head-to-head Elo prediction simulator
│   ├── pitch.tsx                 # Interactive football pitch visualizer
│   ├── player-avatar.tsx         # Player portrait and fallback initials avatar
│   ├── season-context.tsx        # Global season/competition state provider
│   ├── shooting-advisor.tsx      # Probabilistic shooting decision advisor
│   └── tactical-plan.tsx         # Formation analysis and tactical output card
├── lib/                          # Core business logic, data models & database
│   ├── db/                       # Database client, Drizzle schema & storage layers
│   │   ├── auth-store.ts         # User authentication persistence (PostgreSQL / Local fallback)
│   │   ├── calendar-store.ts     # Fixtures and calendar events persistence
│   │   ├── strategy-store.ts     # Saved tactical strategies persistence
│   │   ├── index.ts              # Drizzle ORM PostgreSQL connection pool
│   │   └── schema.ts             # Drizzle PostgreSQL schema definitions
│   ├── fifa/                     # JSON player and team datasets
│   ├── auth-crypto.ts            # PBKDF2 hashing and crypto token generators
│   ├── auth-session.ts           # Signed HMAC session cookie handlers
│   ├── bayesian.ts               # Probabilistic shot decision logic
│   ├── data.ts                   # Team and season aggregate models
│   ├── email-service.ts          # Resend API email dispatch client
│   ├── players.ts                # Player career database
│   ├── prediction.ts             # GridVision Elo matchup prediction model
│   └── style-analysis.ts         # Squad tactical profile analyzer
├── public/                       # Static public assets, player images, and icons
├── scripts/                      # Data verification and database test utilities
│   ├── build-career-verifications.mjs # Career dataset validation script
│   └── test-db-persistence.mjs   # Standalone PostgreSQL persistence test suite
├── .env.example                  # Environment variable template with placeholders
├── package.json                  # Dependencies, scripts, and package metadata
├── postcss.config.mjs            # PostCSS configuration for Tailwind v4
└── tsconfig.json                 # Strict TypeScript configuration
```

---

## 6. Environment Variables Configuration

Create a `.env.local` file in the `fp-project-v1-10` directory by copying the provided `.env.example` template:

```bash
cp .env.example .env.local
```

| Variable | Description | Default / Example Value | Required? |
| :--- | :--- | :--- | :--- |
| `NEXT_PUBLIC_APP_URL` | Canonical base URL of the frontend application | `http://localhost:3000` | **Yes** |
| `AUTH_SECRET` | Secure random string used for HMAC session cookie signing | `your-secure-random-secret-key` | **Yes** |
| `RESEND_API_KEY` | Resend API key for sending verification emails | `re_your_api_key_here` | Optional in local dev |
| `EMAIL_FROM` | Sender email address | `GridVision <onboarding@resend.dev>` | Optional in local dev |
| `DATABASE_URL` | PostgreSQL connection URI (Neon, Supabase, Vercel Postgres) | `postgresql://user:password@ep-sample.neon.tech/gridvision?sslmode=require` | Optional (falls back to `.data/`) |

> [!NOTE]  
> If `DATABASE_URL` is omitted or left blank in local development, GridVision automatically uses its built-in resilient local file store (`.data/auth-store.json`), allowing instant local execution without needing to install or configure a database.

---

## 7. Local Setup & Running Instructions

### Prerequisites
- **Node.js**: `v18.17.0` or higher (tested on Node `v20` and `v24`)
- **Package Manager**: `pnpm` (recommended: `pnpm@12.3.4`) or `npm`

### Installation Steps

1. **Clone the repository:**
   ```bash
   git clone https://github.com/pava-y2k-add8/FP-Gridvision_-by-Paavani-Ria-.git
   cd FP-Gridvision_-by-Paavani-Ria-/fp-project-v1-10
   ```
   *(If working directly inside the cloned `fp-project-v1-10` application folder, ensure you are in that directory).*

2. **Install project dependencies:**
   ```bash
   pnpm install
   # or: npm install
   ```

3. **Configure environment variables:**
   ```bash
   cp .env.example .env.local
   ```
   *(Optionally add your `DATABASE_URL` and `RESEND_API_KEY` in `.env.local`)*.

4. **Run the local development server:**
   ```bash
   pnpm dev
   # or: npm run dev
   ```

5. **Open the web application:**  
   Navigate to [http://localhost:3000](http://localhost:3000) in your web browser.

---

## 8. Build & Verification Commands

To verify code quality, run the project's build and validation suite:

```bash
# 1. Typecheck: Verify strict TypeScript compilation with zero errors
pnpm exec tsc --project tsconfig.json --noEmit

# 2. Production Build: Compile the optimized Next.js webpack production bundle
pnpm build
# or: npm run build

# 3. Production Preview: Run the compiled production build locally
pnpm start
# or: npm start
```

---

## 9. Deployment to Vercel

The project is natively configured for deployment on the [Vercel](https://vercel.com) cloud platform:

1. Import the repository into the **Vercel Dashboard**.
2. Set the **Root Directory** to `fp-project-v1-10` (if deploying from a multi-folder repository).
3. Set the **Framework Preset** to **Next.js**.
4. In **Project Settings $\to$ Environment Variables**, configure:
   - `NEXT_PUBLIC_APP_URL`: `https://fp-project-v1-10.vercel.app`
   - `AUTH_SECRET`: A secure random string for session signing.
   - `DATABASE_URL`: Hosted Neon / PostgreSQL connection string with SSL enabled.
   - `RESEND_API_KEY`: Production Resend API key.
   - `EMAIL_FROM`: Verified sender address (e.g., `GridVision <onboarding@resend.dev>`).
5. Trigger the deployment:
   ```bash
   vercel --prod
   ```
   *Live Production Deployment:* **`https://fp-project-v1-10.vercel.app`**

---

## 10. Authors & Academic Credits

This project was developed by **Paavani** and **Ria** as part of the academic curriculum for the **BSc in Applied Mathematics and Computing (AMC)** program:

- **Institution / Program:** BSc Applied Mathematics and Computing (AMC)
- **Academic Semester:** Semester 1
- **Course / Subject:** Foundations of Programming (FP)
- **Project Developers:** Paavani & Ria
- **Year:** 2026
