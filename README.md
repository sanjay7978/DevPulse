# DevPulse 🚀

> Turn your GitHub activity into a beautiful, shareable developer card.

![Status](https://img.shields.io/badge/status-in%20development-yellow)
![Stack](https://img.shields.io/badge/stack-React%20%2B%20Node.js%20%2B%20PostgreSQL-blue)
![Day](https://img.shields.io/badge/build%20day-1%20of%206-orange)

---

## 💡 What is DevPulse?

DevPulse is a full-stack web app that connects to your GitHub account and transforms your raw activity into a **beautiful, shareable developer card** — like a visual résumé that proves your skills and consistency at a glance.

Recruiters spend less than 10 seconds on a GitHub profile. DevPulse gives them something they actually remember.

---

## 🎯 The Problem It Solves

Every developer has a GitHub profile. But raw GitHub profiles are hard to read — green squares, a wall of repos, numbers with no context. There's no easy way to highlight what matters: your streak, your top languages, your best work.

**DevPulse fixes that.**

---

## 👤 Who It's For

Developers actively job hunting — especially juniors and bootcamp grads — who have real GitHub activity but no polished way to showcase it.

---

## ✨ Core Features (MVP)

- 🔐 **GitHub OAuth login** — one click, read-only access
- 📊 **Auto-generated Dev Card** — streak, languages, heatmap, top repos
- 🔗 **Shareable public URL** — `devpulse.app/u/yourusername`
- 🖼 **PNG export** — download your card, paste it anywhere
- 🎨 **Customisable** — toggle sections, pick your accent colour

---

## 🛠 Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React (Vite) + Recharts |
| Backend | Node.js + Express |
| Auth | GitHub OAuth + Passport.js + JWT |
| Database | PostgreSQL |
| Export | html2canvas |
| Deploy | Vercel (frontend) + Railway (backend + DB) |

---

## 📅 Build Plan

This project is being built in **6 days**, with a GitHub push every single day.

| Day | Focus | Status |
|---|---|---|
| Day 1 | Project setup, README, requirements doc, repo structure | ✅ Done |
| Day 2 | Backend foundation — Express server, PostgreSQL, GitHub OAuth | 🔲 |
| Day 3 | GitHub API integration — data fetching, caching layer | 🔲 |
| Day 4 | Frontend — Dashboard UI, charts, Dev Card component | 🔲 |
| Day 5 | Public profile page, PNG export, settings | 🔲 |
| Day 6 | Polish, deploy to Railway + Vercel, final README | 🔲 |

---

## 📁 Project Structure

```
devpulse/
├── client/                  # React (Vite)
│   └── src/
│       ├── components/      # DevCard, HeatMap, Charts, Skeletons
│       ├── pages/           # Login, Dashboard, PublicProfile, Settings
│       ├── hooks/           # useGithubStats
│       └── App.jsx
├── server/                  # Node + Express
│   ├── routes/              # auth, stats, settings
│   ├── services/            # githubService, cacheService
│   ├── middleware/          # authMiddleware
│   ├── db/                  # PostgreSQL pool
│   └── index.js
├── docs/
│   └── requirements.md      # Full idea & requirements document
├── .env.example
└── README.md
```

---

## 🗄 Database Schema (Overview)

```sql
users           -- GitHub OAuth users
github_cache    -- Cached API responses (JSONB, 60min TTL)
user_settings   -- Card preferences per user
```

---

## 🚀 Local Setup

> Coming soon — will be updated on Day 6 with full setup instructions.

```bash
# Clone the repo
git clone https://github.com/YOURUSERNAME/devpulse.git
cd devpulse

# Backend
cd server && npm install
cp .env.example .env   # fill in your GitHub OAuth credentials

# Frontend
cd ../client && npm install
npm run dev
```

---

## 📄 Docs

- [Idea & Requirements](./docs/requirements.md)

---

## 📈 Build Progress

Follow along — a new commit goes up every day.

---

## 🧑‍💻 Author

Built by **[R.Sanjay]** — [github.com/sanjay7978](https://github.com/sanjay7978)

