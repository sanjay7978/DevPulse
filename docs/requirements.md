# DevPulse — Idea & Requirements Document

> **One-line pitch:** A GitHub activity dashboard that turns your commit history into a shareable developer résumé card.

**Version:** 1.0  
**Stack:** React + Node.js + PostgreSQL  
**Target timeline:** 10 days  
**Goal:** Portfolio / Job hunting

---

## 1. Problem Statement

GitHub profiles show raw activity but tell no story. Recruiters spend fewer than 10 seconds scanning them. Developers have no way to highlight what actually matters — consistency streaks, language growth, project quality — in a visually compelling, shareable format.

**DevPulse solves this** by transforming raw GitHub data into a beautiful, public developer card that can be linked in emails, embedded in portfolios, and downloaded as an image.

---

## 2. Target Users

| User | Pain Point |
|---|---|
| Junior developers | No polished way to show activity beyond a green grid |
| Mid-level devs job hunting | Need proof of consistency, not just a list of repos |
| Bootcamp graduates | No work history — GitHub IS the résumé |
| Freelancers | Want a credibility link to send to clients |

**Primary persona:** A developer with 6–24 months of experience actively job hunting, who has meaningful GitHub activity but no easy way to showcase it.

---

## 3. Core MVP Features

### 3.1 Authentication
- GitHub OAuth login (read-only scope)
- JWT stored in `httpOnly` cookie
- Auto-redirect to dashboard after login

### 3.2 GitHub Data Fetching
- Fetch from GitHub REST API:
  - Repositories (name, stars, language, last pushed)
  - Commit activity (last 52 weeks)
  - Language breakdown across all repos
  - Total contributions (current year)
  - Contribution streak (current + longest)
- Cache results in PostgreSQL for 60 minutes (rate limit protection)
- Background re-fetch when cache is stale

### 3.3 Dev Card
- Visual card displaying:
  - Avatar + username + bio
  - Total contributions (current year)
  - Current streak / longest streak
  - Top 5 languages (donut chart)
  - Contribution heatmap (last 12 weeks)
  - Commit activity bar chart (last 6 months)
  - 3 pinned/top repos with stars + language tag
- Accent colour customisable by user

### 3.4 Public Profile Page
- Route: `/u/:username`
- Viewable without login
- Open Graph meta tags for rich link previews (Slack, Twitter, email)
- Copy shareable link button with toast confirmation

### 3.5 Export
- "Download Card" button → exports Dev Card as PNG
- Scale 2x for retina quality
- Subtle DevPulse watermark on export

### 3.6 Settings
- Toggle visibility of each card section
- Choose accent colour (preset palette)
- Settings persisted in PostgreSQL

---

## 4. Out of Scope (MVP)

These are deliberately excluded to keep the build within 10 days:

- ❌ Private repo stats (requires broader OAuth scope — adds complexity)
- ❌ Team / org views
- ❌ GitLab / Bitbucket support
- ❌ Notifications or email alerts
- ❌ Analytics on profile views
- ❌ Custom domain for public profiles
- ❌ Payments / premium tier

---

## 5. Tech Stack

| Layer | Technology | Reason |
|---|---|---|
| Frontend | React (Vite) | Fast dev server, modern tooling |
| Charts | Recharts | Simpler API than D3, good docs |
| Routing | React Router v6 | Industry standard |
| Backend | Node.js + Express | Lightweight, familiar for JS devs |
| Auth | Passport.js (`passport-github2`) | Handles GitHub OAuth cleanly |
| Sessions | JWT + httpOnly cookies | Secure, stateless |
| Database | PostgreSQL (via `pg`) | Relational + JSONB for cache |
| Export | `html2canvas` | Client-side PNG, no server cost |
| Deployment | Railway (backend + DB) + Vercel (frontend) | Free tier, zero DevOps overhead |

---

## 6. Database Schema

```sql
-- Authenticated users
CREATE TABLE users (
  id            SERIAL PRIMARY KEY,
  github_id     VARCHAR UNIQUE NOT NULL,
  username      VARCHAR NOT NULL,
  avatar_url    TEXT,
  bio           TEXT,
  access_token  TEXT,
  created_at    TIMESTAMP DEFAULT NOW()
);

-- Cached GitHub API responses (JSONB = schema-flexible)
CREATE TABLE github_cache (
  id          SERIAL PRIMARY KEY,
  user_id     INT REFERENCES users(id) ON DELETE CASCADE UNIQUE,
  data        JSONB NOT NULL,
  fetched_at  TIMESTAMP DEFAULT NOW()
);

-- Per-user card display preferences
CREATE TABLE user_settings (
  id              SERIAL PRIMARY KEY,
  user_id         INT REFERENCES users(id) ON DELETE CASCADE UNIQUE,
  accent_color    VARCHAR(7) DEFAULT '#6366f1',
  show_streak     BOOLEAN DEFAULT TRUE,
  show_languages  BOOLEAN DEFAULT TRUE,
  show_heatmap    BOOLEAN DEFAULT TRUE,
  show_repos      BOOLEAN DEFAULT TRUE,
  show_chart      BOOLEAN DEFAULT TRUE
);
```

---

## 7. API Endpoints

### Auth
| Method | Route | Description |
|---|---|---|
| GET | `/auth/github` | Redirect to GitHub OAuth |
| GET | `/auth/github/callback` | OAuth callback, issue JWT |
| POST | `/auth/logout` | Clear cookie |
| GET | `/auth/me` | Return current user from JWT |

### Stats
| Method | Route | Description |
|---|---|---|
| GET | `/api/stats/:username` | Return cached or fresh GitHub stats |
| POST | `/api/stats/refresh` | Force re-fetch (authenticated) |

### Settings
| Method | Route | Description |
|---|---|---|
| GET | `/api/settings` | Get current user settings |
| PATCH | `/api/settings` | Update settings |

---

## 8. Frontend Routes

| Route | Component | Auth Required |
|---|---|---|
| `/` | `Login.jsx` | No |
| `/dashboard` | `Dashboard.jsx` | Yes |
| `/settings` | `Settings.jsx` | Yes |
| `/u/:username` | `PublicProfile.jsx` | No |

---

## 9. Technical Challenges

### Challenge 1 — GitHub OAuth + Secure Token Storage
Implementing the full OAuth handshake (redirect → callback → token exchange → JWT issue) and storing the GitHub access token securely in the DB. Teaches you real-world auth architecture used in every production app.

### Challenge 2 — API Rate Limiting & Cache Strategy
GitHub REST API allows 5,000 requests/hour per token. With multiple users hitting the API, you need smart caching. The `github_cache` table with a TTL check teaches you cache invalidation — one of the genuinely hard problems in software engineering.

### Challenge 3 — Dynamic Card Layout Engine
The Dev Card must look visually balanced whether a user has 2 repos or 200, a bio or none, 10 commits or 3,000. Building a layout that gracefully handles variable data shapes is a real front-end design challenge.

---

## 10. Folder Structure

```
devpulse/
├── client/                        # React (Vite)
│   └── src/
│       ├── components/
│       │   ├── DevCard.jsx        # Exportable card
│       │   ├── HeatMap.jsx        # 12-week contribution grid
│       │   ├── LanguageChart.jsx  # Donut chart
│       │   ├── RepoCard.jsx       # Individual repo tile
│       │   └── SkeletonCard.jsx   # Loading state
│       ├── pages/
│       │   ├── Login.jsx
│       │   ├── Dashboard.jsx
│       │   ├── PublicProfile.jsx
│       │   └── Settings.jsx
│       ├── hooks/
│       │   └── useGithubStats.js
│       └── App.jsx
│
├── server/                        # Node + Express
│   ├── routes/
│   │   ├── auth.js
│   │   ├── stats.js
│   │   └── settings.js
│   ├── services/
│   │   ├── githubService.js       # All GitHub API calls
│   │   └── cacheService.js        # DB cache read/write
│   ├── middleware/
│   │   └── authMiddleware.js      # JWT verification
│   ├── db/
│   │   └── index.js               # pg pool
│   └── index.js
│
├── .env
└── README.md
```

---

## 11. Environment Variables

```env
# Server
PORT=5000
DATABASE_URL=postgresql://user:pass@localhost:5432/devpulse
JWT_SECRET=your_jwt_secret_here
GITHUB_CLIENT_ID=your_github_client_id
GITHUB_CLIENT_SECRET=your_github_client_secret
GITHUB_CALLBACK_URL=http://localhost:5000/auth/github/callback
CLIENT_URL=http://localhost:5173

# Client
VITE_API_URL=http://localhost:5000
```

---

## 12. Monetization Angle (Post-MVP)

| Tier | Price | Features |
|---|---|---|
| Free | $0 | Public card, standard themes, 1 card |
| Pro | $4/mo | Custom accent colours, no watermark on export, analytics (who viewed your card), priority data refresh |

---

## 13. What Makes DevPulse Different

| Feature | github-readme-stats | Sourcerer (defunct) | **DevPulse** |
|---|---|---|---|
| Interactive web app | ❌ Static SVG | ✅ | ✅ |
| Public shareable URL | ❌ | ✅ | ✅ |
| PNG export | ❌ | ❌ | ✅ |
| Customisable card | ❌ | Limited | ✅ |
| Open Graph previews | ❌ | ❌ | ✅ |
| No install needed | ✅ | ❌ | ✅ |

---

## 14. Success Criteria (MVP Done When...)

- [ ] GitHub OAuth login works end-to-end
- [ ] Dashboard displays real stats for the logged-in user
- [ ] Public `/u/:username` page works without login
- [ ] PNG export downloads a clean, high-res card
- [ ] Settings persist across sessions
- [ ] App is deployed and accessible via public URL
- [ ] README has screenshots + live demo link

---

*Document generated for DevPulse v1.0 — built with React + Node.js + PostgreSQL*
