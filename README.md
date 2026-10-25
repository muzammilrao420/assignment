# OpportunityHub — Full-Stack Opportunity Management Platform

A production-ready platform where students discover and apply for internships,
scholarships, jobs, courses, fellowships, and more. Companies manage opportunities
and applications; admins manage the entire platform. Includes an AI matching
engine, chatbot, CV analyzer, and auto-expiry system.

## Tech Stack

- **Backend:** Node.js, Express.js, Prisma ORM, SQLite, JWT auth
- **Frontend:** React 18, Vite, TailwindCSS, Recharts, react-hot-toast

## Quick Start (3 steps)

### 1. Install Node.js
Download & install Node.js 18+ from https://nodejs.org

### 2. Install dependencies
Open a terminal in the project root folder and run:

```bash
npm run install:all
```

This installs packages for the root, `server/`, and `client/` folders.

### 3. Set up the database and start

```bash
npm run db:push     # creates the SQLite database
npm run seed        # loads demo data (users, companies, opportunities)
npm run dev         # starts BOTH backend (port 5000) and frontend (port 5173)
```

Then open **http://localhost:5173** in your browser. Done!

## Demo Accounts

| Role        | Email                          | Password      |
|-------------|--------------------------------|---------------|
| Super Admin | superadmin@opportunityhub.com  | password123   |
| Admin       | admin@opportunityhub.com       | password123   |
| Student     | student@opportunityhub.com     | password123   |
| Company     | company@opportunityhub.com     | password123   |

## Features

### Core Platform
- 4 roles with JWT auth + RBAC (Super Admin, Admin, Company, Student) —
  the Super Admin bypasses all role checks
- Public registration is for **students only**; company accounts are created
  by admins (a temporary password is generated and shown once)
- Full opportunity lifecycle: create → admin approval → publish → apply → review → accept
- Applications with cover letters, bookmarks, notifications, feedback
- Company subscriptions (Free / Pro / Premium), admin dashboard with audit logs
- Legal pages (Terms of Service, Privacy Policy, Cookie Policy) linked in the footer
- Password reset flow (forgot password → token link → reset page)
- Standardized API responses: `{ success, message, data }` on success,
  `{ success, message, error }` on errors
- 12 opportunity types: Internship, Scholarship, Course, Job, Fellowship,
  Competition, Bootcamp, Workshop, Research, Volunteer, Exchange, Study Abroad

### AI & Smart Features ⭐
- **AI Recommendation System** — every opportunity gets a **Match Score (0–100%)**
  computed from skills (40%), interests (25%), location (15%), education (10%),
  and application history (10%), with human-readable match reasons
  (✓ Computer Science · ✓ Python · ⚠ AWS required)
- **Opportunity Chatbot** — floating "Ask OpportunityHub" chat on every page.
  Ask things like *"Remote internships for computer science students"* or
  *"Show me paid internships in San Francisco"* — it parses the query
  (type, location, work mode, paid/unpaid, skills, deadline urgency) and
  answers with live results + your match %
- **CV / Resume Analyzer** — upload a PDF (or paste text); the AI detects
  skills, education level, and experience, then shows *"Your profile matches
  8 available opportunities"* plus high-demand **missing skills** to learn
- **Smart Student Dashboard** — applications / shortlisted / saved stats,
  profile completion %, AI recommendations with match scores, and upcoming
  deadline warnings
- **Profile Completion Checklist** — 9-item checklist with progress bar;
  add your resume to reach 100% 🎉

### Trust & Automation
- **Company Verification** — only opportunities from **verified companies**
  appear in public listings
- **Auto-Expiry System** — opportunities past their deadline are automatically
  marked `EXPIRED` and removed from public listings (runs on server boot and
  every 6 hours; admins can also trigger it via `POST /api/admin/run-expiry`)
- **Deadline Notifications** — students who saved/applied get an automatic
  *"⏰ Deadline Approaching — in 2 days!"* notification when a deadline is
  within 3 days
- **Advanced Search & Filters** — type, category, location, remote/hybrid/
  on-site, **paid/unpaid**, **education level**, **skills**, deadline window,
  plus "Best Match (AI)" sorting for logged-in students
- **Admin Analytics** — success rate, most applied opportunities, popular
  categories, monthly registrations, type/status/work-mode distributions

## Folder Structure

```
├── package.json              Root scripts (dev, seed, db:push, install:all)
├── server/                   Backend
│   ├── .env                  Environment variables (DB URL, JWT secret)
│   ├── prisma/
│   │   ├── schema.prisma     18 database models
│   │   ├── seed.js           Demo data seeder (incl. remote + closing-soon opps)
│   │   └── dev.db            SQLite database (created by db:push)
│   ├── uploads/              Uploaded files (CVs, logos)
│   └── src/
│       ├── index.js          Express app entry
│       ├── middleware/       Auth (JWT/RBAC), uploads, error handler
│       ├── utils/
│       │   ├── matcher.js    AI matching engine (scoring + CV analysis)
│       │   └── expiry.js     Auto-expiry + deadline notification scheduler
│       └── routes/           12 API route modules (auth, opportunities,
│                             applications, students, companies, admin,
│                             notifications, feedback, categories,
│                             bookmarks, upload, chatbot)
└── client/                   Frontend
    ├── vite.config.js        Dev proxy /api -> localhost:5000
    └── src/
        ├── App.jsx           All routes + role guards
        ├── context/          AuthContext (global auth state)
        ├── lib/api.js        API client + helpers
        ├── components/       Layouts, cards, MatchScore, ChatbotWidget
        └── pages/            30+ pages
            ├── (public)      Landing, listing, detail, auth pages
            ├── student/      Dashboard, AI recommendations, CV analyzer,
            │                 profile, applications, saved, notifications...
            ├── company/      Dashboard, create/manage, analytics, subscription...
            └── admin/        Dashboard, users, approvals, categories, analytics...
```

## API Highlights

| Endpoint                          | Description                                  |
|-----------------------------------|----------------------------------------------|
| `GET /api/opportunities`          | Advanced filters: `paid`, `education`, `skill`, `deadlineBefore`, `workMode`, `categorySlug`… (match scores attached for students) |
| `GET /api/opportunities/recommended/student` | AI-ranked recommendations with match scores + reasons |
| `GET /api/opportunities/:id`      | Detail incl. your personal match score       |
| `POST /api/chatbot/query`         | Chatbot — `{ message }` → reply + results    |
| `POST /api/students/analyze-cv`   | CV analysis — PDF upload or `{ text }`       |
| `GET /api/students/dashboard`     | Smart dashboard (stats, recs, deadlines)     |
| `GET /api/students/profile/completion` | 9-item completion checklist              |
| `POST /api/admin/companies`       | Admin creates a company account (returns one-time temp password) |
| `POST /api/auth/reset-password`   | Reset password with token from forgot-password email |
| `GET /api/admin/analytics`        | Success rate, most applied, popular categories |
| `POST /api/admin/run-expiry`      | Manually trigger expiry check + notifications |

## Other Useful Commands

| Command             | What it does                              |
|---------------------|-------------------------------------------|
| `npm run dev`       | Start backend + frontend together         |
| `npm run seed`      | Reset database with fresh demo data       |
| `npm run db:push`   | Sync schema.prisma changes to database    |
| `npm run build`     | Production build of the frontend          |

## Troubleshooting

- **"Can't reach database"** — make sure you ran `npm run db:push`
- **Port already in use** — stop other apps on port 5000/5173, or change
  `PORT` in `server/.env`
- **Blank page** — make sure the backend is running; the frontend proxies
  API calls to `http://localhost:5000`
- **Reset everything** — delete `server/prisma/dev.db`, then run
  `npm run db:push` and `npm run seed`
- **Scanned PDF CV gives no text** — the CV analyzer can't read images from
  scanned PDFs; paste the CV text instead
