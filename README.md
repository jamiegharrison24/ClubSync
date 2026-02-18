# ClubSync

A platform for **Monash University clubs** to manage budget tracking and administration. Built as a final-year Computer Science degree project by a team of four.

> **Note:** This project is no longer actively maintained. It is preserved here for portfolio and reference.

---

## What is ClubSync?

ClubSync gives university clubs a single place to:

- **Track budgets** — Allocate and spend budget per project, with available vs. spent visibility
- **Run administration** — Organise teams, projects, and task boards (todo statuses, assignment, approval workflows)
- **Manage events** — Create events, send RSVP invites by email, and track accept/decline responses

The app supports multiple teams per user, role-based access (e.g. exec vs standard members), and a public event calendar so anyone can discover club events.

---

## Tech overview

| Layer      | Stack |
|-----------|--------|
| **Frontend** | React 19, TypeScript, Vite, Tailwind CSS, Radix UI, Redux Toolkit, React Router, React Hook Form, Zod |
| **Backend**  | FastAPI, Python, Pydantic, MongoDB (async via PyMongo), JWT auth (access + refresh), SendGrid (transactional email), APScheduler |
| **Architecture** | Three-layer backend: **API** → **Service** → **DB** (see [backend-main/README.md](backend-main/README.md)) |

---

## Core functionality (high level)

- **Authentication** — Login/logout, JWT in cookies, email verification codes, refresh tokens, change password.
- **Teams** — Create team, join by short code, promote/leave/kick members, delete team; create/delete projects and events at team level.
- **Projects** — Project CRUD; todo board with custom statuses, drag-and-drop reorder, assign/approve (including exec auto-approve); **budget**: increase available budget and record spending with validation so you can’t spend more than available.
- **Events** — Create/update events, list all public events; RSVP flow: send invite emails (SendGrid + HTML templates), reply via links (accept/decline), store and retrieve RSVP state.

Representative backend entrypoints:

- **Budget** — `increase_budget_service` / `spend_budget_service` in `backend-main/app/service/project.py` (and API routes `POST /projects/increase-budget/{id}`, `POST /projects/spend-budget/{id}`). Spending checks `budget_available` and updates both `budget_available` and `budget_spent`.
- **Auth** — Token creation and cookie handling in `backend-main/app/api/auth.py`; password hashing and verification in `backend-main/app/core/security.py`.
- **Events & RSVP** — `send_rsvp_invite_email`, `reply_rsvp`, and event CRUD in `backend-main/app/service/event.py` and `backend-main/app/api/event.py`.

The frontend uses a dedicated **Budget management** UI (e.g. `frontend-main/src/components/team-details/BudgetManagement.tsx`) and calls the project budget APIs via `frontend-main/src/api/projects.ts` (`increaseBudget`, `spendBudget`).

---

## Repository layout

- **`frontend-main/`** — React + Vite app. See **[frontend-main/README.md](frontend-main/README.md)** for setup (Bun, `bun run dev`, local URL).
- **`backend-main/`** — FastAPI app. See **[backend-main/README.md](backend-main/README.md)** for the three-layer design (API / Service / DB) and conventions.

For “what it is” and how to run each part, start with those two READMEs.

---

## Summary

ClubSync is a full-stack club management and budget-tracking app for Monash University clubs, built with React + FastAPI + MongoDB and a clear API/Service/DB split. It is not maintained anymore but is kept as a portfolio piece; for details and run instructions, use the READMEs in `frontend-main` and `backend-main`.
