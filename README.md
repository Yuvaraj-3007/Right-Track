# Right Tracker

**Ticket Management System** | By Wisright Team

A centralized web-based platform for managing support tickets — employees raise issues, support staff resolves them, admins oversee everything with full transparency and accountability.

[![Status](https://img.shields.io/badge/Status-Planning-orange)]()
[![Version](https://img.shields.io/badge/Version-0.1_(Draft)-blue)]()
[![Stories](https://img.shields.io/badge/User_Stories-31-green)]()
[![Points](https://img.shields.io/badge/Story_Points-109-purple)]()

---

## Table of Contents

- [Overview](#overview)
- [Problem Statement](#problem-statement)
- [Key Features](#key-features)
- [Tech Stack](#tech-stack)
- [User Roles](#user-roles)
- [Ticket Workflow](#ticket-workflow)
- [SLA Targets](#sla-targets)
- [Security](#security)
- [Database Schema](#database-schema)
- [API Endpoints](#api-endpoints)
- [Project Structure](#project-structure)
- [Documentation](#documentation)
- [Presentations](#presentations)
- [Deployment Architecture](#deployment-architecture)
- [Getting Started](#getting-started)
- [Project Timeline](#project-timeline)
- [Success Metrics](#success-metrics)
- [Current Status](#current-status)
- [Contributing](#contributing)

---

## Overview

Right Tracker is a Ticket Management System designed to replace manual support request tracking (email, phone calls, walk-ins) with a structured, transparent, and accountable ticketing workflow. The system provides:

- **For Employees**: Submit tickets, track status in real-time, get notified on updates
- **For Support Staff**: Manage assigned tickets, update status, add notes, track performance
- **For Admins**: Oversee all tickets, assign staff, view reports, manage users

---

## Problem Statement

| Current Problem | Impact |
|---|---|
| Requests via email, phone, walk-ins get lost | Employees frustrated, issues unresolved |
| No way to track request status | Constant follow-up calls waste everyone's time |
| No accountability or audit trail | No record of who handled what or how long it took |
| No performance data | Management cannot measure team efficiency |
| Duplicate requests | Multiple people report same issue, wasting resources |
| No prioritization system | Critical issues compete with low-priority tasks |

**Right Tracker solves all of these** by providing a single source of truth for every support request.

---

## Key Features

### P0 — Must Have (Essential for Launch)

| Feature | Description |
|---|---|
| **Authentication & Authorization** | JWT tokens (24h expiry), bcrypt password hashing, role-based access control |
| **Ticket Management** | Create, view, edit, assign, update status, auto-generated ticket numbers (TKT-0001) |
| **Comments & Notes** | Public comments (visible to employee) + internal notes (staff/admin only) |
| **SLA Tracking** | Auto-calculated deadlines based on priority (Critical: 4h, High: 8h, Medium: 24h, Low: 48h) |
| **Security & Data Protection** | Helmet.js, rate limiting, CORS, input validation, SQL injection prevention, audit trail |

### P1 — Should Have (Important for Usability)

| Feature | Description |
|---|---|
| **Dashboard** | Role-specific views with ticket counts, priority breakdown, staff workload |
| **Attachments** | Upload screenshots/documents (PNG, JPG, PDF, DOCX, XLSX — max 5MB) |
| **Notifications** | Real-time in-app (Socket.io) + email notifications (Nodemailer) |
| **Search & Filter** | Search by ticket ID/title/keyword, filter by status/priority/category/date |
| **Docker + Coolify** | Containerized deployment with automated CI/CD via Coolify |

### P2 — Nice to Have (Can Be Deferred)

| Feature | Description |
|---|---|
| **Reports & Analytics** | Ticket reports by status/category/priority/staff, SLA compliance, export to PDF/Excel |

---

## Tech Stack

| Layer | Technology | Purpose |
|---|---|---|
| Frontend | **React.js** (Vite) | Single Page Application UI |
| Backend | **Node.js** (Express.js) | REST API server |
| Database | **PostgreSQL** (Prisma ORM) | Relational data storage with type-safe queries |
| Authentication | **JWT + bcrypt** | Stateless auth with secure password hashing |
| UI Library | Ant Design / MUI | Pre-built UI components |
| Real-time | Socket.io | Live notifications |
| File Upload | Multer | Attachment handling |
| Email | Nodemailer | Transactional email notifications |
| Security | **Helmet.js + express-rate-limit** | HTTP security headers + brute force prevention |
| Validation | **express-validator** | Input validation and sanitization |
| Containers | **Docker + Docker Compose** | Multi-container orchestration |
| Deployment | **Coolify** (self-hosted PaaS) | Automated deployment with SSL |
| Web Server | **Nginx** | Serves frontend build, reverse-proxies API |

---

## User Roles

### Admin
- Full system access
- Create, edit, deactivate user accounts
- Assign/reassign tickets to any support staff
- View all tickets across the organization
- View reports and analytics
- Close resolved tickets
- System configuration

### Support Staff / Technician
- View assigned + unassigned (open) tickets
- Self-assign unassigned tickets
- Update ticket status through the workflow
- Add internal notes (not visible to employees)
- Reply to employees on tickets
- View own performance metrics

### Employee / End User
- Submit new support tickets
- View and track their own tickets only
- Add comments/replies on their tickets
- Upload attachments (screenshots, documents)
- Reopen their own resolved tickets (within 48 hours)

---

## Ticket Workflow

Every ticket follows a defined status flow:

```
                    ┌──────────────────────────────────────────┐
                    │                                          │
    ┌──────┐    ┌──────────┐    ┌─────────────┐    ┌──────────┐    ┌────────┐
    │ Open │───>│ Assigned │───>│ In Progress │───>│ Resolved │───>│ Closed │
    └──────┘    └──────────┘    └─────────────┘    └──────────┘    └────────┘
                                    │       ▲          │
                                    ▼       │          ▼
                                ┌─────────┐ │      ┌────────┐
                                │ On Hold │─┘      │ Reopen │
                                └─────────┘        └────────┘
```

### Transition Rules

| Current Status | Can Move To | Who Can Transition |
|---|---|---|
| Open | Assigned | Admin, Support Staff |
| Assigned | In Progress | Admin, Support Staff |
| In Progress | On Hold, Resolved | Admin, Support Staff |
| On Hold | In Progress | Admin, Support Staff |
| Resolved | Closed, Open (reopen) | Admin (close), Employee (reopen) |
| Closed | — (terminal) | — |

**Ticket Categories:** Hardware, Software, Network, Email, Access, Other

**Priority Levels:** Low, Medium, High, Critical

---

## SLA Targets

Service Level Agreement — maximum resolution time by ticket priority:

| Priority | Resolution Target | Use Case |
|---|---|---|
| **Critical** | 4 hours | System down, security breach, all users affected |
| **High** | 8 hours | Major feature broken, multiple users impacted |
| **Medium** | 24 hours | Single user issue, workaround available |
| **Low** | 48 hours | Feature request, nice-to-have, cosmetic issue |

**Target SLA Compliance:** 90%+

---

## Security

Security is a P0 (Must Have) feature. Based on proven patterns from WisRight's existing projects (HRMS-POC, Prompt-Master):

### Authentication
- JWT tokens with 24-hour expiry (configurable)
- Bcrypt password hashing (10 salt rounds)
- Password policy: min 8 chars, 1 uppercase, 1 number, 1 special character
- Account lockout after 5 failed login attempts (15-min cooldown)
- No user enumeration (generic "Invalid credentials" error)

### API Protection
- **Helmet.js** — X-Frame-Options, X-Content-Type-Options, X-XSS-Protection, HSTS
- **CORS** — Restricted to allowed origins only (not wildcard in production)
- **Rate Limiting** — 100 req/15min general, 5 req/15min for login
- **Request Size** — 10MB body limit
- **Input Validation** — All endpoints validated via express-validator

### Data Protection
- Passwords never stored or returned in plain text
- SQL injection prevention via Prisma ORM (parameterized queries)
- Environment variables for all secrets (.env excluded from git)
- HTTPS enforced in production via Coolify/Traefik
- Content Security Policy (CSP) headers
- Sensitive fields excluded from API responses

### Audit Trail
- All ticket status changes logged in Ticket History table
- Login/logout events tracked
- Failed authentication attempts logged

---

## Database Schema

6 tables defined in `prisma/schema.prisma`:

### Users
| Column | Type | Description |
|---|---|---|
| id | UUID (PK) | Unique user ID |
| name | VARCHAR(100) | Full name |
| email | VARCHAR(150) | Unique email |
| password | VARCHAR(255) | Bcrypt-hashed password |
| role | ENUM | admin, staff, employee |
| department | VARCHAR(100) | User's department |
| is_active | BOOLEAN | Account status |

### Tickets
| Column | Type | Description |
|---|---|---|
| id | UUID (PK) | Unique ticket ID |
| ticket_no | VARCHAR(20) | Display ID (TKT-0001) |
| title | VARCHAR(200) | Short summary |
| description | TEXT | Detailed description |
| category | ENUM | hardware, software, network, email, access, other |
| priority | ENUM | low, medium, high, critical |
| status | ENUM | open, assigned, in_progress, on_hold, resolved, closed |
| created_by | UUID (FK) | Employee who created |
| assigned_to | UUID (FK) | Support staff assigned |
| sla_deadline | TIMESTAMP | Expected resolution by |
| resolved_at | TIMESTAMP | When resolved |
| closed_at | TIMESTAMP | When closed |
| deleted_at | TIMESTAMP | Soft delete (null if active) |

### Other Tables
- **Comments** — ticket_id, user_id, content, is_internal (boolean)
- **Attachments** — ticket_id, file_name, file_path, file_size, file_type
- **Notifications** — user_id, ticket_id, message, is_read
- **Ticket History** — ticket_id, user_id, field, old_value, new_value (audit trail)

---

## API Endpoints

### Authentication
| Method | Endpoint | Auth | Description |
|---|---|---|---|
| POST | `/api/auth/register` | Admin | Register a new user |
| POST | `/api/auth/login` | Public | Login, returns JWT |
| GET | `/api/auth/me` | All | Get current user profile |

### Tickets
| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/api/tickets` | All (filtered) | List tickets (role-based filtering) |
| POST | `/api/tickets` | Employee | Create a new ticket |
| GET | `/api/tickets/:id` | All (access check) | Get ticket details |
| PUT | `/api/tickets/:id` | Conditional | Update ticket fields |
| PUT | `/api/tickets/:id/assign` | Admin, Staff | Assign ticket to staff |
| PATCH | `/api/tickets/:id/status` | Conditional | Update ticket status |

### Dashboard & Users
| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/api/dashboard/stats` | All (filtered) | Dashboard statistics |
| GET | `/api/users/staff` | Admin, Staff | List staff (for assignment) |
| GET | `/api/health` | Public | Health check for Docker |

### Role-Based Ticket Filtering
- **Employee:** sees only their own tickets
- **Support Staff:** sees assigned + open/unassigned tickets
- **Admin:** sees all tickets

### Response Format
```json
// Success
{ "success": true, "data": { ... } }

// Error
{ "success": false, "message": "Error description" }

// Validation Error
{ "success": false, "errors": [{ "field": "email", "message": "Valid email required" }] }
```

---

## Project Structure

```
Right Tracker/
├── docs/
│   ├── 01-PRD/
│   │   └── PRD.md                              # Product Requirements Document
│   ├── 02-POC/
│   │   └── POC.md                              # Proof of Concept Plan
│   ├── 03-User-Stories/
│   │   └── UserStories.md                      # All user stories (31 stories)
│   ├── Right-Tracker-Plan.html                 # Technical presentation
│   ├── Right-Tracker-Business-Presentation.html # Business heads presentation
│   └── wisright-logo.png                       # WisRight brand logo
│
├── client/                                      # React Frontend (Vite)
│   ├── src/
│   │   ├── components/                          # Layout, Sidebar, ProtectedRoute, Badges
│   │   ├── pages/                               # Login, Dashboard, TicketList, CreateTicket, TicketDetail
│   │   ├── context/                             # AuthContext (JWT state management)
│   │   ├── services/                            # Axios API client
│   │   └── utils/                               # Constants (colors, categories, roles)
│   ├── Dockerfile                               # Multi-stage: Vite build → Nginx
│   ├── nginx.conf                               # SPA routing + API proxy
│   └── .dockerignore
│
├── server/                                      # Node.js Backend (Express)
│   ├── prisma/
│   │   ├── schema.prisma                        # Database schema definition
│   │   ├── migrations/                          # Version-controlled migrations
│   │   └── seed.ts                              # Seed data script
│   ├── controllers/                             # Auth, Ticket, Dashboard controllers
│   ├── middleware/                               # auth, role, errorHandler, validate
│   ├── routes/                                  # Auth, Ticket, Dashboard routes
│   ├── validators/                              # express-validator rules
│   ├── utils/                                   # AppError, generateTicketNo
│   ├── server.js                                # Express entry point
│   ├── Dockerfile                               # Multi-stage: Node.js Alpine
│   └── .dockerignore
│
├── docker-compose.yml                           # 3 services: postgres, server, client
├── docker-compose.prod.yml                      # Production overrides
├── .env.docker.example                          # Docker environment template
├── .gitignore
└── README.md
```

---

## Documentation

| Document | Description | Link |
|---|---|---|
| **PRD** | Product Requirements Document — 19 sections covering all aspects of the system | [View PRD](docs/01-PRD/PRD.md) |
| **POC** | Proof of Concept — scope, API endpoints, Docker setup, Coolify deployment, 19 success criteria | [View POC](docs/02-POC/POC.md) |
| **User Stories** | 31 stories across 9 epics — acceptance criteria, story points, POC coverage | [View Stories](docs/03-User-Stories/UserStories.md) |

### User Stories Summary

| Epic | Priority | Stories | Points |
|---|---|---|---|
| 1. Authentication & Users | P0 | 6 | 16 |
| 2. Ticket Management | P0 | 9 | 30 |
| 3. Comments & Notes | P0 | 2 | 5 |
| 4. Attachments | P1 | 2 | 5 |
| 5. Dashboard | P1 | 3 | 16 |
| 6. Notifications | P1 | 2 | 10 |
| 7. Reports & Analytics | P2 | 2 | 8 |
| 8. Deployment & Infrastructure | P1 | 2 | 8 |
| 9. Security & Data Protection | P0 | 3 | 11 |
| **Total** | | **31** | **109** |

### Priority Breakdown

| Priority | Stories | Points | % |
|---|---|---|---|
| P0 — Must Have | 15 | 47 | 43% |
| P1 — Should Have | 13 | 52 | 48% |
| P2 — Nice to Have | 3 | 10 | 9% |

---

## Presentations

Two HTML presentations are available in `docs/`:

| Presentation | Audience | Contents |
|---|---|---|
| [Technical Plan](docs/Right-Tracker-Plan.html) | Development Team | Tech stack, features, charts, UI mockups, architecture, timeline |
| [Business Presentation](docs/Right-Tracker-Business-Presentation.html) | Business Heads | Problem/solution, expected impact, ROI, outcomes, security, timeline |

> Open the HTML files in a browser to view interactive charts and mockups.

---

## Deployment Architecture

```
┌─────────────────────────────────────┐
│      Coolify / Traefik              │
│   (SSL + Reverse Proxy + Auto-Deploy) │
└──────────────┬──────────────────────┘
               │ HTTPS
┌──────────────▼──────────────────────┐
│   Client Container (Nginx)          │
│   - Serves React build             │
│   - Proxies /api → server          │
│   - Port 80                        │
└──────────────┬──────────────────────┘
               │ /api/*
┌──────────────▼──────────────────────┐
│   Server Container (Node.js)        │
│   - Express.js REST API            │
│   - Port 5000 (internal)           │
└──────────────┬──────────────────────┘
               │
┌──────────────▼──────────────────────┐
│   Database (PostgreSQL 14)          │
│   - Port 5432 (internal only)      │
│   - Named volume for persistence   │
└─────────────────────────────────────┘
```

- All services run as **Docker containers** on a shared bridge network
- **Coolify** manages deployments from Git, handles SSL via Let's Encrypt
- **PostgreSQL** data persists via named Docker volume
- Only the **Nginx frontend** is exposed externally (port 80/443)

---

## Getting Started

### Prerequisites
- Node.js v18+
- PostgreSQL v14+
- Docker Engine v24+ (optional, for containerized setup)

### Option 1: Manual Setup

```bash
# 1. Clone the repository
git clone https://github.com/Yuvaraj-3007/Right-Track.git
cd Right-Track

# 2. Create database
psql -U postgres -c "CREATE DATABASE right_tracker;"

# 3. Backend setup
cd server
npm install
cp .env.example .env          # Edit with your DB credentials
npx prisma generate           # Generate Prisma client
npx prisma migrate dev        # Run migrations
npx prisma db seed            # Seed sample data
npm run dev                   # Start on http://localhost:5000

# 4. Frontend setup
cd ../client
npm install
cp .env.example .env          # Set VITE_API_URL
npm run dev                   # Start on http://localhost:5173
```

### Option 2: Docker Setup (Recommended)

```bash
# 1. Clone the repository
git clone https://github.com/Yuvaraj-3007/Right-Track.git
cd Right-Track

# 2. Create environment file
cp .env.docker.example .env.docker    # Edit with your passwords

# 3. Build and start all services
docker compose up --build -d

# 4. Seed the database
docker compose exec server npx prisma db seed

# 5. Open in browser
# http://localhost
```

### Test Accounts (POC Seed Data)

| Role | Email | Password |
|---|---|---|
| Admin | admin@righttracker.com | Admin@123 |
| Support Staff | john@righttracker.com | Staff@123 |
| Support Staff | jane@righttracker.com | Staff@123 |
| Employee | alice@righttracker.com | User@123 |
| Employee | bob@righttracker.com | User@123 |
| Employee | charlie@righttracker.com | User@123 |

---

## Project Timeline

| Phase | Duration | Status |
|---|---|---|
| PRD + User Stories | Week 1 | Done (Draft v0.1) |
| POC Development | Week 2 | Pending |
| UI/UX Design | Week 2-3 | Pending |
| Backend Development | Week 3-5 | Pending |
| Frontend Development | Week 4-6 | Pending |
| Integration & Testing | Week 6-7 | Pending |
| UAT & Bug Fixes | Week 7-8 | Pending |
| Deployment (Coolify) | Week 8 | Pending |

**Total Duration:** 8 weeks from planning to production

---

## Success Metrics

| Metric | Target |
|---|---|
| Average response time | Under 2 hours |
| Average resolution time | Under 24 hours |
| Ticket SLA compliance | 90%+ |
| User adoption rate | 80%+ of employees |
| Employee satisfaction rating | 4+ out of 5 |

---

## Current Status

> All documents are in **Draft** status (v0.1) — pending review and approval.

- [x] PRD — Draft v0.1 (19 sections, security, Docker/Coolify)
- [x] User Stories — Draft v0.1 (31 stories, 109 points, 9 epics)
- [x] POC Plan — Draft v0.1 (19 success criteria, Docker setup, Coolify deployment)
- [x] Technical Presentation HTML
- [x] Business Presentation HTML
- [ ] Document Review & Approval — **Pending**
- [ ] POC Development — Pending
- [ ] Full Development — Pending
- [ ] Production Deployment — Pending

---

## Contributing

This is an internal project by the **Wisright Team**. For questions or contributions:

1. Check the [PRD](docs/01-PRD/PRD.md) for requirements
2. Check the [User Stories](docs/03-User-Stories/UserStories.md) for acceptance criteria
3. Follow the project's coding standards (to be defined during POC)
4. All PRs require review before merge

---

## License

Internal use only. Proprietary to Wisright.

---

*Right Tracker v0.1 (Draft) | Wisright Team | March 2026*
