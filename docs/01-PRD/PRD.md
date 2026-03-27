# Product Requirements Document (PRD)

## Right Tracker — IT Office Ticket Management System

| Field              | Detail                            |
| ------------------ | --------------------------------- |
| **Project Name**   | Right Tracker                     |
| **Version**        | 0.1                               |
| **Date**           | 2026-03-27                        |
| **Status**         | Draft                             |
| **Author**         | IT Office Team                    |
| **Document Owner** | IT Department                     |

---

## Revision History

| Version | Date       | Author          | Description                                          | Status |
| ------- | ---------- | --------------- | ---------------------------------------------------- | ------ |
| 0.1     | 2026-03-27 | IT Office Team  | Initial draft — structure, scope, Docker/Coolify     | Draft  |

---

## 1. Executive Summary

Right Tracker is a web-based IT Office Ticket Management System designed to centralize, streamline, and bring accountability to internal IT support operations. The system replaces ad-hoc communication channels — such as email, phone calls, and verbal requests — with a structured ticketing workflow that provides real-time visibility for all stakeholders. Built on a modern stack of React.js, Node.js, and PostgreSQL, the platform supports role-based access for administrators, IT staff, and employees, with features including SLA tracking, automated notifications, and management reporting. The application is fully containerized with Docker and deployed via Coolify for production reliability. This document defines the product requirements for Version 1.0 and serves as the foundation for design, development, and acceptance testing.

---

## 2. Problem Statement

The IT Office currently lacks a formal system for tracking support requests, leading to the following pain points:

- **No centralized request log.** IT requests arrive via email, phone, instant messages, and walk-ins, making it impossible to maintain a single source of truth.
- **Lost and forgotten requests.** Without a tracking mechanism, tickets are frequently misplaced, duplicated, or never addressed, resulting in frustrated employees and unresolved issues.
- **No visibility or accountability.** Employees have no way to check the status of their requests, and management cannot measure IT staff workload or response times.
- **Inconsistent prioritization.** Without defined priority levels and SLA targets, critical issues compete with low-priority tasks on an ad-hoc basis.
- **No performance data.** The absence of historical records prevents the IT Office from identifying recurring problems, measuring efficiency, or justifying resource allocation.
- **Manual follow-ups consume time.** Both employees and IT staff spend significant effort on status inquiries that a self-service portal would eliminate.

Right Tracker addresses these issues by providing a transparent, auditable, and SLA-driven ticketing workflow accessible to every stakeholder in the organization.

---

## 3. Stakeholders

| Role              | Name / Group        | Responsibility                                                    | Interest Level |
| ----------------- | ------------------- | ----------------------------------------------------------------- | -------------- |
| IT Manager        | TBD                 | Project sponsor; approves requirements, priorities, and go-live   | High           |
| IT Staff          | IT Office Team      | Primary users; manage, resolve, and report on tickets             | High           |
| Department Heads  | All Departments     | Review departmental ticket volume and SLA compliance              | Medium         |
| Employees         | All Staff           | End users; submit and track IT support requests                   | Medium         |
| Project Developer | Development Team    | Design, build, test, and deploy the system                        | High           |
| QA / Tester       | TBD                 | Validate requirements against acceptance criteria                 | Medium         |

---

## 4. Overview

Right Tracker is an internal IT Office Ticket Management System that allows employees to raise IT support tickets and enables IT staff to manage, assign, track, and resolve those tickets efficiently. The system aims to replace manual tracking (email, phone, verbal requests) with a centralized, transparent, and accountable workflow.

---

## 5. Objectives

- Provide a centralized platform for all IT support requests
- Improve ticket response and resolution time
- Enable transparency — employees can track their ticket status in real time
- Provide management with reports and analytics on IT performance
- Reduce lost or forgotten requests
- Establish accountability through audit trails and assignment tracking

---

## 6. Tech Stack

| Layer        | Technology              |
| ------------ | ----------------------- |
| Frontend     | React.js (Vite)         |
| Backend      | Node.js (Express.js)    |
| Database     | PostgreSQL (Sequelize)   |
| Auth         | JWT + bcrypt            |
| UI Library   | Ant Design / MUI        |
| Real-time    | Socket.io               |
| File Upload  | Multer                  |
| Email        | Nodemailer              |
| Security     | Helmet.js + express-rate-limit |
| Validation   | express-validator              |
| Containers   | Docker + Docker Compose |
| Deployment   | Coolify (self-hosted PaaS) |
| Web Server   | Nginx (production frontend) |

### 6.1 Deployment Architecture

The application is containerized using Docker and deployed via Coolify, a self-hosted Platform-as-a-Service (PaaS).

```
                    ┌─────────────────────────────┐
                    │      Coolify / Traefik       │
                    │   (SSL + Reverse Proxy)      │
                    └──────────┬──────────────────┘
                               │ HTTPS
                    ┌──────────▼──────────────────┐
                    │   Client Container (Nginx)   │
                    │   - Serves React build       │
                    │   - Proxies /api → server    │
                    │   - Port 80                  │
                    └──────────┬──────────────────┘
                               │ /api/*
                    ┌──────────▼──────────────────┐
                    │   Server Container (Node.js) │
                    │   - Express.js API           │
                    │   - Port 5000 (internal)     │
                    └──────────┬──────────────────┘
                               │
                    ┌──────────▼──────────────────┐
                    │   Database (PostgreSQL)       │
                    │   - Port 5432 (internal)     │
                    │   - Named volume for data    │
                    └─────────────────────────────┘
```

- All three services run as Docker containers orchestrated by Docker Compose.
- Coolify manages deployments from the Git repository, handles SSL termination via its built-in Traefik proxy, and provides a UI for environment variable management.
- PostgreSQL data is persisted via a named Docker volume.
- Inter-service communication uses a private Docker bridge network; only the frontend (Nginx) is exposed to external traffic.

---

## 7. User Roles & Permissions

### 7.1 Admin
- Full system access
- Create, edit, delete users
- Assign roles to users
- View all tickets across the organization
- Assign/reassign tickets to any IT staff member
- View reports and analytics
- System configuration
- Close resolved tickets

> **Note:** Ticket categories and priorities are hardcoded in v1.0. Admin-configurable categories/priorities are planned for v2.0.

### 7.2 IT Staff / Technician
- View tickets assigned to them
- View unassigned (open) tickets
- Self-assign unassigned tickets (cannot assign to other staff)
- Update ticket status
- Add internal notes (visible only to staff and admin)
- Reply to employees on tickets
- View own performance metrics

### 7.3 Employee / End User
- Submit new tickets
- View and track their own tickets only
- Add comments / replies on their tickets
- Upload attachments (screenshots, documents)
- Reopen their own resolved tickets (within 48 hours)
- Rate resolved tickets (optional, v2.0)

---

## 8. Core Features

> **Priority Legend:**
> **P0 — Must Have:** Essential for launch; system is non-functional without these.
> **P1 — Should Have:** Important for usability; planned for v1.0 but could be deferred briefly.
> **P2 — Nice to Have:** Adds value; can be delivered in a fast-follow release if timeline is tight.

### 8.1 Authentication & Authorization — P0 (Must Have)
- User registration (admin-only; admin creates accounts for employees and staff)
- Login / Logout
- JWT-based session management
- Role-based access control (RBAC)
- Password reset via email
- Profile management

### 8.2 Ticket Management — P0 (Must Have)
- **Create Ticket**: title, description, category, priority, attachments
- **Ticket Categories**: Hardware, Software, Network, Email, Access, Other
- **Priority Levels**: Low, Medium, High, Critical
- **Status Transitions**:

  | Current Status | Can Move To                    | Who Can Transition                   |
  | -------------- | ------------------------------ | ------------------------------------ |
  | open           | assigned                       | Admin, Staff                         |
  | assigned       | in_progress                    | Admin, Staff                         |
  | in_progress    | on_hold, resolved              | Admin, Staff                         |
  | on_hold        | in_progress                    | Admin, Staff                         |
  | resolved       | closed, open (reopen)          | Admin (close), Employee (reopen)     |
  | closed         | — (terminal state)             | —                                    |

- **Assignment**: Admin can assign to any staff. Staff can self-assign unassigned tickets.
- **SLA Tracking**: Expected resolution time based on priority
  - Critical: 4 hours
  - High: 8 hours
  - Medium: 24 hours
  - Low: 48 hours

### 8.3 Comments & Notes — P0 (Must Have)
- Employee can add comments on their tickets
- IT Staff can add:
  - **Public replies** — visible to employee
  - **Internal notes** — visible only to IT staff and admin
- Timestamp and author on every comment

### 8.4 Attachments — P1 (Should Have)
- Upload files during ticket creation or in comments
- Supported formats: PNG, JPG, PDF, DOCX, XLSX
- Max file size: 5 MB per file
- Max 3 files per upload

### 8.5 Dashboard — P1 (Should Have)
- **Admin Dashboard**:
  - Total tickets by status (open, in progress, resolved, closed)
  - Tickets by priority and category
  - Staff workload (tickets per technician)
  - Overdue tickets (based on SLA deadline)
  - Average resolution time
- **IT Staff Dashboard**:
  - My assigned tickets
  - Unassigned tickets
  - My performance (resolved count, avg time)
- **Employee Dashboard**:
  - My tickets and their current status
  - Quick "New Ticket" button

### 8.6 Notifications — P1 (Should Have)
- **In-App Notifications**: Real-time via Socket.io
- **Email Notifications** (via Nodemailer):
  - Ticket created (to employee + admin)
  - Ticket assigned (to IT staff)
  - Status changed (to employee)
  - New comment added (to relevant parties)
  - Ticket overdue (to assigned staff + admin)

### 8.7 Reports & Analytics — P2 (Nice to Have)
- Tickets by status, category, priority, staff
- Average resolution time
- SLA compliance rate
- Date range filter
- Export to PDF / Excel

### 8.8 Search & Filter — P1 (Should Have)
- Search tickets by ID, title, keyword
- Filter by status, priority, category, assigned staff, date range
- Sort by date, priority, status

### 8.9 Security & Data Protection — P0 (Must Have)

Based on security patterns from WisRight's existing projects (HRMS-POC, Prompt-Master):

**Authentication Security:**
- JWT tokens with configurable expiry (default: 24h)
- Bcrypt password hashing (10 salt rounds)
- Password policy: minimum 8 characters, 1 uppercase, 1 number, 1 special character
- Account lockout after 5 failed login attempts (15-minute cooldown)
- Automatic token invalidation on logout
- Secure token storage with automatic cleanup on 401 responses

**Authorization & Access Control:**
- Role-Based Access Control (RBAC) with permission-level granularity
- Route-level middleware guards (authenticate + authorize)
- Resource-level access checks (employees see only own tickets)
- Admin-only operations protected with additional verification

**API Security:**
- Helmet.js for HTTP security headers (X-Frame-Options, X-Content-Type-Options, X-XSS-Protection, HSTS)
- CORS restricted to allowed origins only (not wildcard in production)
- Rate limiting: 100 requests/15min per IP (general), 5 requests/15min for login
- Request body size limit (10MB)
- Input validation on all endpoints via express-validator
- Parameterized queries via Sequelize ORM (SQL injection prevention)

**Data Protection:**
- All passwords hashed — never stored or transmitted in plain text
- Sensitive fields excluded from API responses (password, tokens)
- Environment variables for all secrets (JWT_SECRET, DB credentials)
- .env files excluded from version control
- HTTPS enforced in production via Coolify/Traefik
- Content Security Policy (CSP) headers

**Error Handling:**
- Generic error messages in production (no stack traces exposed)
- Detailed errors in development only
- No user enumeration on login (same error for wrong email/password)
- Global error handling middleware

**Audit & Logging:**
- All ticket status changes logged in Ticket History table
- Login/logout events tracked
- Failed authentication attempts logged
- Application-level error logging

**File Upload Security (v1.0):**
- MIME type validation (PNG, JPG, PDF, DOCX, XLSX only)
- File size limit: 5MB per file
- Filename sanitization (strip special characters)
- Files stored outside web root

---

## 9. Database Schema (High-Level)

### Users Table
| Column      | Type         | Description           |
| ----------- | ------------ | --------------------- |
| id          | UUID (PK)    | Unique user ID        |
| name        | VARCHAR(100) | Full name             |
| email       | VARCHAR(150) | Unique email          |
| password    | VARCHAR(255) | Hashed password       |
| role        | ENUM         | admin, staff, employee|
| department  | VARCHAR(100) | User's department     |
| is_active   | BOOLEAN      | Account status        |
| created_at  | TIMESTAMP    | Account creation date |
| updated_at  | TIMESTAMP    | Last update           |

### Tickets Table
| Column       | Type         | Description            |
| ------------ | ------------ | ---------------------- |
| id           | UUID (PK)    | Unique ticket ID       |
| ticket_no    | VARCHAR(20)  | Display ID (TKT-0001)  |
| title        | VARCHAR(200) | Short summary          |
| description  | TEXT         | Detailed description   |
| category     | ENUM         | hardware, software, network, email, access, other |
| priority     | ENUM         | low, medium, high, critical |
| status       | ENUM         | open, assigned, in_progress, on_hold, resolved, closed |
| created_by   | UUID (FK)    | Employee who created   |
| assigned_to  | UUID (FK)    | IT staff assigned      |
| sla_deadline | TIMESTAMP    | Expected resolution by |
| resolved_at  | TIMESTAMP    | When resolved          |
| closed_at    | TIMESTAMP    | When closed            |
| deleted_at   | TIMESTAMP    | Soft delete timestamp (null if active) |
| created_at   | TIMESTAMP    | Ticket creation date   |
| updated_at   | TIMESTAMP    | Last update            |

### Comments Table
| Column     | Type         | Description                    |
| ---------- | ------------ | ------------------------------ |
| id         | UUID (PK)    | Unique comment ID              |
| ticket_id  | UUID (FK)    | Related ticket                 |
| user_id    | UUID (FK)    | Who commented                  |
| content    | TEXT         | Comment text                   |
| is_internal| BOOLEAN      | Internal note (staff only)     |
| created_at | TIMESTAMP    | Comment date                   |

### Attachments Table
| Column     | Type         | Description                    |
| ---------- | ------------ | ------------------------------ |
| id         | UUID (PK)    | Unique attachment ID           |
| ticket_id  | UUID (FK)    | Related ticket                 |
| comment_id | UUID (FK)    | Related comment (nullable)     |
| file_name  | VARCHAR(255) | Original file name             |
| file_path  | VARCHAR(500) | Server storage path            |
| file_size  | INTEGER      | Size in bytes                  |
| file_type  | VARCHAR(50)  | MIME type                      |
| uploaded_by| UUID (FK)    | Who uploaded                   |
| created_at | TIMESTAMP    | Upload date                    |

### Notifications Table
| Column     | Type         | Description                    |
| ---------- | ------------ | ------------------------------ |
| id         | UUID (PK)    | Unique notification ID         |
| user_id    | UUID (FK)    | Recipient                      |
| ticket_id  | UUID (FK)    | Related ticket                 |
| message    | VARCHAR(500) | Notification text              |
| is_read    | BOOLEAN      | Read status                    |
| created_at | TIMESTAMP    | Notification date              |

### Ticket History Table (Audit Trail)
| Column     | Type         | Description                    |
| ---------- | ------------ | ------------------------------ |
| id         | UUID (PK)    | Unique history entry ID        |
| ticket_id  | UUID (FK)    | Related ticket                 |
| user_id    | UUID (FK)    | Who made the change            |
| field      | VARCHAR(50)  | Field that changed (e.g., status, assigned_to) |
| old_value  | VARCHAR(255) | Previous value                 |
| new_value  | VARCHAR(255) | New value                      |
| created_at | TIMESTAMP    | When the change occurred       |

> **Note:** The Ticket History table provides the audit trail required by Non-Functional Requirements (Section 10). It is planned for v1.0 full build, not the POC.

---

## 10. Non-Functional Requirements

| Requirement     | Detail                                                    |
| --------------- | --------------------------------------------------------- |
| Performance     | Page load under 2 seconds                                 |
| Security        | Passwords hashed, JWT tokens, HTTPS, input sanitization   |
| Scalability     | Support up to 500 concurrent users                        |
| Availability    | 99.5% uptime                                              |
| Browser Support | Chrome, Firefox, Edge (latest 2 versions)                 |
| Responsive      | Works on desktop, tablet, and mobile browsers             |
| Data Backup     | Daily automated database backups                          |
| Logging         | Application-level logging for errors and key events       |
| Audit Trail     | Track all ticket changes via Ticket History table         |
| Authentication | JWT with 24h expiry, bcrypt hashing, account lockout |
| API Protection | Rate limiting, CORS restriction, Helmet security headers |
| Data Privacy   | No plain-text passwords, sensitive fields excluded from responses |
| Portability     | Fully containerized via Docker; deployable on any Docker-capable host |

---

## 11. Out of Scope (Version 1.0)

- Mobile app (React Native)
- Active Directory / LDAP integration
- AI-based ticket auto-categorization
- Chat/live support
- Multi-language support
- Third-party integrations (Slack, Teams)
- Admin-configurable ticket categories and priorities

---

## 12. Future Enhancements (Version 2.0+)

- Auto-assignment (round-robin / load-based)
- Knowledge base / FAQ
- Customer satisfaction survey / ticket rating
- SLA breach escalation
- Mobile app
- Active Directory integration
- Recurring ticket templates
- Admin-configurable categories and priorities

---

## 13. Success Metrics

| Metric                        | Target              |
| ----------------------------- | ------------------- |
| Average response time         | Under 2 hours       |
| Average resolution time       | Under 24 hours      |
| Ticket SLA compliance         | 90%+                |
| User adoption rate            | 80%+ of employees   |
| Employee satisfaction rating  | 4+ out of 5         |

---

## 14. Timeline (Estimated)

| Phase                  | Duration   |
| ---------------------- | ---------- |
| PRD + User Stories     | Week 1     |
| POC Development        | Week 2     |
| UI/UX Design           | Week 2-3   |
| Backend Development    | Week 3-5   |
| Frontend Development   | Week 4-6   |
| Integration & Testing  | Week 6-7   |
| UAT & Bug Fixes        | Week 7-8   |
| Deployment (Coolify)   | Week 8     |

---

## 15. Assumptions & Constraints

### Assumptions
1. All users have access to a modern web browser (Chrome, Firefox, or Edge) and a stable intranet or internet connection.
2. The organization will provide an SMTP-capable mail server or approve a third-party email service for transactional notifications.
3. A PostgreSQL database server (v14+) will be provisioned before backend development begins.
4. User onboarding and training will be coordinated by the IT Manager outside the scope of this project.
5. Initial user accounts will be created by the Admin via the application; the seed script populates test data for the POC only.
6. The production server has Docker Engine 24+ and Docker Compose v2 installed, or Coolify v4+ is available as the deployment platform.

### Constraints
1. The system must be delivered within an 8-week timeline as outlined in the project schedule.
2. The development team consists of a small in-house team; no external contractors are planned.
3. The application must run on existing infrastructure — no additional hardware procurement is approved.
4. All data must reside on-premises or within the organization's approved cloud environment.
5. The project must use open-source or already-licensed technologies to avoid additional licensing costs.

---

## 16. Dependencies

| Dependency          | Type          | Description                                                        | Owner / Provider | Status    |
| ------------------- | ------------- | ------------------------------------------------------------------ | ---------------- | --------- |
| PostgreSQL 14+      | Database      | Primary relational data store for all application data             | IT Infrastructure| Pending   |
| Node.js 18+ LTS    | Runtime       | Server-side JavaScript runtime for the Express.js backend          | Development Team | Available |
| SMTP Server         | Service       | Required for Nodemailer to send transactional email notifications  | IT Infrastructure| Pending   |
| Hosting / Server    | Infrastructure| Linux server or VM to host the Docker containers                   | IT Infrastructure| Pending   |
| Domain / DNS        | Infrastructure| Internal domain or IP allocation for application access            | IT Infrastructure| Pending   |
| SSL Certificate     | Security      | TLS certificate for HTTPS (auto-managed by Coolify/Let's Encrypt) | IT Infrastructure| Pending   |
| Docker Engine 24+   | Runtime       | Containerizes all services for consistent dev/staging/prod environments | Development Team | Pending   |
| Docker Compose v2   | Orchestration | Defines and runs multi-container setup (client, server, postgres)  | Development Team | Pending   |
| Coolify v4+         | Deployment    | Self-hosted PaaS for automated deployment, SSL, and env management | IT Infrastructure| Pending   |

---

## 17. Risks & Mitigations

| #  | Risk                                           | Likelihood | Impact   | Mitigation Strategy                                                       |
| -- | ---------------------------------------------- | ---------- | -------- | ------------------------------------------------------------------------- |
| R1 | SMTP server not provisioned on time            | Medium     | High     | Identify and pre-configure a fallback cloud SMTP service                  |
| R2 | Low user adoption after launch                 | Medium     | High     | Conduct training sessions; provide quick-start guide; gather early feedback|
| R3 | Scope creep delays delivery                    | High       | Medium   | Enforce P0/P1/P2 prioritization; defer P2 features if timeline is at risk |
| R4 | Single developer bottleneck                    | Medium     | High     | Document code thoroughly; maintain modular architecture for easy handoff  |
| R5 | Database performance degrades under load       | Low        | Medium   | Implement pagination, indexing strategy, and connection pooling from day one|
| R6 | Security vulnerability in authentication flow  | Low        | Critical | Follow OWASP guidelines; use bcrypt for hashing; validate all inputs     |
| R7 | Hosting environment unavailable at deployment  | Low        | High     | Docker ensures portability; confirm Coolify environment 2 weeks before go-live |
| R8 | Docker/Coolify unfamiliarity on ops team       | Low        | Medium   | Document deployment procedures; Coolify provides a GUI that reduces CLI complexity |
| R9 | Data breach or unauthorized access | Low | Critical | JWT auth, bcrypt hashing, RBAC, rate limiting, input validation, Helmet headers, HTTPS enforcement |

---

## 18. Glossary

| Term            | Definition                                                                                     |
| --------------- | ---------------------------------------------------------------------------------------------- |
| SLA             | **Service Level Agreement** — A commitment to resolve tickets within a defined time frame based on priority |
| RBAC            | **Role-Based Access Control** — A security model that restricts system access based on user roles |
| JWT             | **JSON Web Token** — A compact, URL-safe token format used for stateless authentication        |
| CRUD            | **Create, Read, Update, Delete** — The four basic operations performed on database records     |
| UUID            | **Universally Unique Identifier** — A 128-bit identifier used as primary keys                  |
| ORM             | **Object-Relational Mapping** — A technique (via Sequelize) that maps database tables to JavaScript objects |
| SMTP            | **Simple Mail Transfer Protocol** — The standard protocol used to send transactional email notifications |
| API             | **Application Programming Interface** — The backend endpoints that the frontend consumes via HTTP |
| UAT             | **User Acceptance Testing** — The final testing phase where end users validate the system       |
| PRD             | **Product Requirements Document** — This document; defines what the product must do             |
| HTTPS           | **Hypertext Transfer Protocol Secure** — HTTP with TLS encryption to protect data in transit   |
| POC             | **Proof of Concept** — An early implementation to validate technical feasibility                |
| Docker          | A platform for building, shipping, and running applications in isolated containers              |
| Docker Compose  | A tool for defining and running multi-container Docker applications via a YAML file             |
| Coolify         | An open-source, self-hosted PaaS alternative to Heroku/Netlify, used for automated deployments |
| Nginx           | A high-performance web server used to serve the production frontend build and reverse-proxy API requests |
| Traefik         | A reverse proxy and load balancer integrated into Coolify for SSL termination and routing       |
| Helmet.js | A Node.js middleware that sets HTTP security headers to protect against common web vulnerabilities |
| Rate Limiting | Restricting the number of API requests a client can make in a given time window to prevent abuse |
| CSP | **Content Security Policy** — An HTTP header that controls which resources the browser can load, preventing XSS attacks |
| Bcrypt | A password hashing algorithm that uses salting and multiple rounds to make brute-force attacks impractical |

---

## 19. Approval & Sign-off

> **Document Status: DRAFT — Not yet approved. Signatures below are pending.**

| Role              | Name | Signature | Date | Status   |
| ----------------- | ---- | --------- | ---- | -------- |
| IT Manager        |      |           |      | Pending  |
| Project Lead      |      |           |      | Pending  |
| Lead Developer    |      |           |      | Pending  |
| QA Lead           |      |           |      | Pending  |
| Department Head   |      |           |      | Pending  |

---

*This document is currently in **Draft** status. It is subject to review and revision. No development work should begin until this PRD has been formally approved by all listed stakeholders.*

---

*End of PRD — Right Tracker v0.1 (Draft)*
