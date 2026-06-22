# DevPulse AI — Phase 1

## IMPORTANT

This repository implements **Phase 1 only**.

### Build ONLY

* GitHub Username Input
* GitHub API Integration
* Gemini Summary Generation
* Results Dashboard

### Do NOT Implement

* Authentication
* Database
* User Accounts
* Payments
* PDF Export
* Recruiter Reports
* Resume Generation
* Interview Questions
* Developer Scoring
* Future Phases

Focus exclusively on the **GitHub Profile AI Summary** feature.

---

# Overview

DevPulse AI is an AI-powered Developer Intelligence Platform that analyzes GitHub profiles and generates professional AI summaries for developers.

Phase 1 focuses exclusively on the GitHub Profile AI Summary feature.

---

# Phase 1 Goal

Allow users to:

1. Enter a GitHub username
2. Fetch GitHub profile data
3. Analyze repositories
4. Generate an AI-powered developer summary using Gemini
5. Display results in a professional dashboard

---

# Tech Stack

## Frontend

* Next.js 15 (App Router)
* TypeScript
* Tailwind CSS
* shadcn/ui

## Backend

* Next.js API Routes

## AI

* Gemini API

## Deployment

* Vercel

---

# Core Feature

## GitHub Profile AI Summary

### Input

GitHub Username

Examples:

```txt
torvalds
vercel
gaearon
```

### Output

Professional Developer Intelligence Report containing:

#### Developer Overview

Short professional summary.

#### Main Technologies

Languages and tools inferred from repositories.

#### Experience Assessment

Beginner / Intermediate / Advanced.

#### Project Highlights

Most notable projects and strengths.

---

# User Flow

### Step 1

User visits homepage.

### Step 2

User enters GitHub username.

### Step 3

User clicks:

```txt
Analyze Profile
```

### Step 4

Frontend sends request to:

```txt
POST /api/analyze
```

### Step 5

Backend fetches data from GitHub.

#### User Profile

```txt
GET /users/{username}
```

#### User Repositories

```txt
GET /users/{username}/repos
```

### Step 6

Backend extracts profile and repository information.

### Step 7

Backend identifies technologies.

### Step 8

Backend sends data to Gemini.

### Step 9

Gemini generates AI summary.

### Step 10

Frontend displays results.

---

# Landing Page Requirements

Modern SaaS-style landing page containing:

### Hero Section

Title:

```txt
DevPulse AI
```

Subtitle:

```txt
AI-Powered Developer Intelligence Platform
```

### Username Input

```txt
GitHub Username
```

### Analyze Button

```txt
Analyze Profile
```

---

# GitHub Integration

Use GitHub REST API.

## Profile Data

Collect:

* Name
* Username
* Bio
* Avatar URL
* Followers
* Following
* Public Repositories

## Repository Data

Collect:

* Repository Name
* Description
* Primary Language
* Stars
* Forks
* Repository URL

---

# Repository Rules

To avoid excessive API usage:

* Fetch maximum 30 repositories
* Sort repositories by stars descending
* Select top 5 repositories for display
* Send only top repositories to Gemini

---

# Technology Extraction Rules

Do NOT use Gemini for technology extraction.

Extract technologies directly from GitHub repositories.

Rules:

* Count language occurrences
* Sort by frequency
* Return top technologies
* Maximum 8 technologies

Example:

```json
[
  "TypeScript",
  "JavaScript",
  "Python",
  "Go"
]
```

---

# AI Summary Generation

Gemini receives:

### Profile Information

```json
{
  "name": "",
  "bio": "",
  "followers": 0,
  "following": 0
}
```

### Repository Information

```json
[
  {
    "name": "",
    "description": "",
    "language": "",
    "stars": 0
  }
]
```

---

# Gemini Prompt

```txt
You are a senior engineering recruiter.

Analyze the following GitHub profile.

Generate:

1. Developer Overview
2. Main Technologies
3. Experience Assessment
4. Project Highlights

Keep the response between 150 and 200 words.

Use professional recruiter-friendly language.

Return JSON only.
```

---

# Gemini Response Format

Return JSON only:

```json
{
  "summary": "Developer summary...",
  "experienceLevel": "Intermediate"
}
```

Allowed values:

```txt
Beginner
Intermediate
Advanced
```

---

# API Design

<<<<<<< HEAD
Built by **[R.Sanjay]** — [github.com/sanjay7978](https://github.com/sanjay7978)
=======
## POST /api/analyze
>>>>>>> 38deef0 (phase 1 readme file)

### Request

```json
{
  "username": "torvalds"
}
```

### Response

```json
{
  "profile": {},
  "repositories": [],
  "technologies": [
    "React",
    "TypeScript",
    "Node.js"
  ],
  "summary": "Developer summary...",
  "experienceLevel": "Intermediate"
}
```

---

# Results Dashboard

Display:

## Profile Card

Show:

* Avatar
* Name
* Username
* Followers
* Following
* Repository Count

## AI Summary Card

Show:

* Summary
* Experience Level

## Technology Badges

Display extracted technologies.

## Top Repositories

Show:

* Repository Name
* Description
* Language
* Stars
* Forks

Display top 5 repositories sorted by stars.

---

# Error Handling

Handle all scenarios gracefully.

## Empty Username

```txt
Please enter a GitHub username.
```

## Invalid Username

```txt
Invalid GitHub username.
```

## User Not Found

```txt
GitHub profile not found.
```

## GitHub Rate Limit

```txt
GitHub API rate limit exceeded.
```

## Gemini Failure

```txt
AI summary currently unavailable.
```

## Network Errors

```txt
Something went wrong.
```

---

# AI Failure Fallback

If Gemini fails:

* Still display profile information
* Still display repositories
* Still display technologies
* Show fallback message:

```txt
AI summary currently unavailable.
```

Application must never crash because of AI failure.

---

# GitHub API Requirements

Use:

```env
GITHUB_TOKEN=
```

Rules:

* Use authenticated requests when token exists
* Fall back to unauthenticated requests when token is absent
* Handle GitHub rate limits gracefully

---

# Security Requirements

* Validate GitHub username input
* Sanitize user input
* Limit username length to 39 characters
* Reject invalid GitHub usernames
* Never expose API keys to the client
* Server-side API calls only

---

# Non-Functional Requirements

* TypeScript strict mode enabled
* No use of `any`
* Responsive design
* Mobile friendly
* Tablet friendly
* Desktop friendly
* Reusable UI components
* Loading states required
* Error states required
* Clean architecture
* Maintainable codebase

---

# Folder Structure

```txt
src/
│
├── app/
│   ├── api/
│   │   └── analyze/
│   │       └── route.ts
│   │
│   ├── page.tsx
│   ├── layout.tsx
│   └── globals.css
│
├── components/
│   ├── github-form.tsx
│   ├── profile-card.tsx
│   ├── summary-card.tsx
│   ├── repo-card.tsx
│   ├── tech-badges.tsx
│   └── loading-state.tsx
│
├── services/
│   ├── github.service.ts
│   └── gemini.service.ts
│
├── lib/
│   ├── utils.ts
│   └── constants.ts
│
├── types/
│   ├── github.ts
│   └── analysis.ts
│
└── middleware/
```

---

# Environment Variables

Create:

```env
GITHUB_TOKEN=

GEMINI_API_KEY=
```

---

# Definition of Done

The project is complete when:

* User can enter any valid GitHub username
* GitHub profile data is fetched successfully
* Repositories are fetched successfully
* Technologies are extracted correctly
* Gemini generates a summary
* Experience level is generated
* Results are displayed in dashboard
* Loading states work correctly
* Error states work correctly
* Gemini failure fallback works
* Project runs locally
* Project builds successfully
* Project is deployable on Vercel

---

# Testing Checklist

## Valid User

Test:

```txt
torvalds
```

Expected:

* Profile loads
* Repositories load
* Summary generated

---

## Invalid User

Test:

```txt
randomuserthatdoesnotexist12345
```

Expected:

* Proper error message

---

## Empty Input

Expected:

* Validation error shown

---

## Gemini Failure

Expected:

* Profile still displays
* Repository data still displays
* Fallback summary message shown

---

# Deployment

## Vercel

1. Push repository to GitHub
2. Import repository into Vercel
3. Configure environment variables
4. Deploy

Required Variables:

```env
GITHUB_TOKEN=
GEMINI_API_KEY=
```

---

# Success Criteria

A user can:

1. Enter a GitHub username
2. Click Analyze
3. Receive GitHub profile information
4. Receive AI-generated summary
5. View technologies
6. View top repositories
7. Understand developer experience level

All within a modern SaaS dashboard experience.

---

# Future Phases (Out of Scope)

These are NOT part of Phase 1:

## Phase 2

* Developer Score
* Skill Radar

## Phase 3

* Interview Question Generator

## Phase 4

* Recruiter PDF Reports

## Phase 5

* GitHub vs Job Description Matching

## Phase 6

* Public Shareable Developer Portfolio

```
```
