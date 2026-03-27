# User Stories — Right Tracker

## Ticket Management System

| Field            | Detail                          |
| ---------------- | ------------------------------- |
| **Project Name** | Right Tracker                   |
| **Version**      | 0.1                             |
| **Date**         | 2026-03-27                      |
| **Status**       | Draft                           |

---

## Story Point Scale

| Points | Effort       |
| ------ | ------------ |
| 1      | Very small   |
| 2      | Small        |
| 3      | Medium       |
| 5      | Large        |
| 8      | Very large   |

---

## Priority Legend

> Aligned with PRD Section 8 — Core Features.
>
> **P0 — Must Have:** Essential for launch; system is non-functional without these.
> **P1 — Should Have:** Important for usability; planned for v1.0 but could be deferred briefly.
> **P2 — Nice to Have:** Adds value; can be delivered in a fast-follow release if timeline is tight.

---

## Epic 1: Authentication & User Management — P0 (Must Have)

### US-101: User Registration
**As an** admin,
**I want to** register new users into the system,
**So that** employees and support staff can access the platform.

**Acceptance Criteria:**
- Admin can create a user with name, email, department, and role
- System sends a welcome email with temporary password
- Duplicate email is rejected with an error message
- Password must meet minimum security requirements (8+ chars, 1 uppercase, 1 number)
- New user is created with `is_active = true` by default

**Priority:** P0 | **Points:** 3

---

### US-102: User Login
**As a** user,
**I want to** log in with my email and password,
**So that** I can access the system based on my role.

**Acceptance Criteria:**
- User can log in with valid email and password
- Invalid credentials show a generic error message (no hint about which field is wrong)
- Deactivated accounts are rejected with "Account is deactivated" message
- JWT token is issued on successful login
- User is redirected to their role-specific dashboard

**Priority:** P0 | **Points:** 2

---

### US-103: User Logout
**As a** user,
**I want to** log out of the system,
**So that** my session is securely ended.

**Acceptance Criteria:**
- Clicking logout clears the JWT token from local storage
- User is redirected to the login page
- Protected routes are no longer accessible after logout
- Back button does not restore the session

**Priority:** P0 | **Points:** 1

---

### US-104: Password Reset
**As a** user,
**I want to** reset my password via email,
**So that** I can regain access if I forget my password.

**Acceptance Criteria:**
- User enters email and receives a reset link
- Reset link expires after 1 hour
- User can set a new password via the link
- Old password is no longer valid after reset
- Invalid or expired links show an appropriate error

**Priority:** P1 | **Points:** 3

---

### US-105: Profile Management
**As a** user,
**I want to** view and update my profile,
**So that** my information stays current.

**Acceptance Criteria:**
- User can view their name, email, department, and role
- User can update their name and department
- User can change their password (requires current password)
- Email and role cannot be changed by the user
- Changes are saved and reflected immediately

**Priority:** P1 | **Points:** 2

---

### US-106: User Management (Admin)
**As an** admin,
**I want to** view, edit, activate, and deactivate user accounts,
**So that** I can manage system access.

**Acceptance Criteria:**
- Admin can see a list of all users with search and filter
- Admin can edit user name, department, and role
- Admin can deactivate a user (blocks login, `is_active = false`)
- Admin can reactivate a deactivated user
- Admin cannot delete or deactivate their own account
- User list shows role, department, and active status

**Priority:** P0 | **Points:** 5

---

## Epic 2: Ticket Management — P0 (Must Have)

### US-201: Create Ticket
**As an** employee,
**I want to** create a support ticket,
**So that** the support team can be aware of and resolve my issue.

**Acceptance Criteria:**
- Employee fills in title, description, category, and priority
- Employee can attach up to 3 files (max 5 MB each) — P1 feature, not in POC
- System generates a unique ticket number (TKT-0001, TKT-0002, etc.)
- Ticket status is set to "open" by default
- SLA deadline is auto-calculated based on priority
- Confirmation message is shown after creation
- Employee is redirected to the ticket list
- Only employees and admin can create tickets. Support Staff cannot create tickets.

**Priority:** P0 | **Points:** 5

---

### US-202: View Ticket List
**As a** user,
**I want to** see a list of tickets relevant to me,
**So that** I can track and manage them.

**Acceptance Criteria:**
- Employee sees only their own tickets
- Support Staff sees their assigned tickets + open/unassigned tickets
- Admin sees all tickets
- List shows: ticket no, title, status, priority, category, date, assigned to
- Tickets are sorted by created date (newest first)
- Pagination (20 tickets per page)
- If no tickets exist, a "No tickets found" message is displayed with guidance.

**Priority:** P0 | **Points:** 3

---

### US-203: View Ticket Details
**As a** user,
**I want to** view full details of a ticket,
**So that** I can understand the issue and its progress.

**Acceptance Criteria:**
- Shows all ticket fields: title, description, category, priority, status
- Shows created by (name), assigned to (name or "Unassigned"), created date
- Shows SLA deadline (full version)
- Shows full comment/note history (full version, not in POC)
- Shows attachments with download links (full version, not in POC)
- Employee can only view their own tickets

**Priority:** P0 | **Points:** 3

---

### US-204: Assign Ticket
**As an** admin or support staff,
**I want to** assign a ticket to a technician,
**So that** the right person handles the issue.

**Acceptance Criteria:**
- Admin can assign any ticket to any support staff member
- Support Staff can self-assign unassigned tickets only (cannot assign to other staff members). Admin can assign any ticket to any staff member.
- Ticket status automatically changes to "assigned" when assigned from "open"
- Assigned staff member receives a notification (full version)
- Reassignment is allowed (with notification to both old and new assignee in full version)
- Only users with role "staff" appear in the assignment dropdown

**Priority:** P0 | **Points:** 3

---

### US-205: Update Ticket Status
**As an** support staff member,
**I want to** update the status of a ticket,
**So that** the employee knows the progress.

**Acceptance Criteria:**
- Status transitions follow the defined flow:
  - open → assigned
  - assigned → in_progress
  - in_progress → on_hold, resolved
  - on_hold → in_progress
  - resolved → closed (admin only), open (employee reopen)
  - closed → (terminal, no further changes)
- Invalid transitions are blocked with an error message
- Only admin can set status to "closed"
- Employee can reopen their own "resolved" ticket (sets status back to "open")
- Employee is notified on every status change (full version)

**Priority:** P0 | **Points:** 3

---

### US-206: Edit Ticket
**As an** employee,
**I want to** edit my ticket details,
**So that** I can add missing information.

**Acceptance Criteria:**
- Employee can edit title, description, category, priority
- Editing is allowed only when status is "open"
- Support Staff and Admin can edit any ticket at any status
- Changes are saved and the updated ticket is displayed

**Priority:** P1 | **Points:** 3

---

### US-207: Search & Filter Tickets
**As a** user,
**I want to** search and filter tickets,
**So that** I can quickly find what I need.

**Acceptance Criteria:**
- Search by ticket number, title, or keyword in description
- Filter by: status, priority, category, assigned staff, date range
- Sort by: created date, priority, status, last updated
- Filters can be combined
- Clear all filters option

**Priority:** P1 | **Points:** 5

---

### US-208: Delete Ticket
**As an** admin,
**I want to** delete a ticket,
**So that** duplicate or spam tickets can be removed.

**Acceptance Criteria:**
- Only admin can delete tickets
- Soft delete (marked as deleted, not removed from DB)
- Deleted tickets are not visible in normal ticket lists
- Confirmation dialog before deletion

**Priority:** P2 | **Points:** 2

---

### US-209: SLA Tracking
**As an** admin,
**I want** tickets to have SLA deadlines auto-calculated based on priority,
**So that** I can track overdue tickets and ensure timely resolution.

**Acceptance Criteria:**
- SLA deadline is auto-calculated on ticket creation based on priority:
  - Critical = 4 hours
  - High = 8 hours
  - Medium = 24 hours
  - Low = 48 hours
- Overdue tickets are visually highlighted in ticket lists
- Admin dashboard shows overdue ticket count
- SLA deadline is visible on the ticket detail page

**Priority:** P0 | **Points:** 3

---

## Epic 3: Comments & Notes — P0 (Must Have)

### US-301: Add Public Comment
**As a** user,
**I want to** add a comment on a ticket,
**So that** I can communicate about the issue.

**Acceptance Criteria:**
- Employee can comment on their own tickets
- Support Staff can comment on their assigned tickets
- Admin can comment on any ticket
- Comments show author name, role badge, and timestamp
- Relevant parties are notified of new comments (full version)
- Comments are ordered chronologically (oldest first)

**Priority:** P0 | **Points:** 3

---

### US-302: Add Internal Note
**As an** support staff member,
**I want to** add an internal note on a ticket,
**So that** I can share technical details with other staff without the employee seeing it.

**Acceptance Criteria:**
- Internal notes are visible only to Support Staff and Admin
- Notes are visually distinct from public comments (different color/badge)
- Employee cannot see internal notes in any view
- Notes show author name and timestamp
- Notes are ordered chronologically with public comments

**Priority:** P0 | **Points:** 2

---

## Epic 4: Attachments — P1 (Should Have)

### US-401: Upload Attachment
**As an** employee,
**I want to** attach files to my ticket,
**So that** support staff can see screenshots or documents related to my issue.

**Acceptance Criteria:**
- Allowed during ticket creation and in comments
- Supported formats: PNG, JPG, PDF, DOCX, XLSX
- Max file size: 5 MB per file
- Max 3 files per upload
- Invalid file type or size shows a clear error message
- Upload progress indicator

**Priority:** P1 | **Points:** 3

---

### US-402: Download Attachment
**As a** user,
**I want to** download attachments from a ticket,
**So that** I can view the uploaded files.

**Acceptance Criteria:**
- Click to download any attachment on the ticket
- Only users with access to the ticket can download
- File preview for images (inline display)
- File name and size shown next to download link

**Priority:** P1 | **Points:** 2

---

## Epic 5: Dashboard — P1 (Should Have)

### US-501: Admin Dashboard
**As an** admin,
**I want to** see an overview of all tickets and staff performance,
**So that** I can monitor operations.

**Acceptance Criteria:**
- Total tickets by status (cards with count)
- Tickets by priority (visual breakdown)
- Tickets by category (visual breakdown)
- Staff workload (tickets per person)
- Overdue tickets list
- Average resolution time
- Data refreshes on page load

**Priority:** P1 | **Points:** 8

---

### US-502: Support Staff Dashboard
**As an** support staff member,
**I want to** see my assigned tickets and performance,
**So that** I can prioritize my work.

**Acceptance Criteria:**
- My assigned tickets count grouped by status
- Unassigned tickets count I can pick up
- My resolved ticket count (this week/month)
- My average resolution time
- Overdue tickets highlighted

**Priority:** P1 | **Points:** 5

---

### US-503: Employee Dashboard
**As an** employee,
**I want to** see my tickets and their status,
**So that** I know the progress of my requests.

**Acceptance Criteria:**
- List of my tickets with current status
- Quick "Create New Ticket" button
- Ticket status summary (open, in progress, resolved counts)
- Most recent ticket at the top

**Priority:** P1 | **Points:** 3

---

## Epic 6: Notifications — P1 (Should Have)

### US-601: In-App Notifications
**As a** user,
**I want to** receive real-time in-app notifications,
**So that** I'm aware of updates without checking manually.

**Acceptance Criteria:**
- Bell icon with unread count in the header
- Notification dropdown showing recent notifications
- Click notification to go to the related ticket
- Mark as read (individual and mark all)
- Real-time via Socket.io (no page refresh needed)

**Priority:** P1 | **Points:** 5

---

### US-602: Email Notifications
**As a** user,
**I want to** receive email notifications for important ticket events,
**So that** I don't miss updates when I'm not in the system.

**Acceptance Criteria:**
- Email on: ticket created, assigned, status changed, new comment, overdue
- Email contains ticket number, title, and a link to the ticket
- Emails are sent asynchronously (don't block the main action)

**Priority:** P1 | **Points:** 5

---

## Epic 7: Reports & Analytics — P2 (Nice to Have)

### US-701: Generate Reports
**As an** admin,
**I want to** generate reports on ticket data,
**So that** I can analyze team performance and present to management.

**Acceptance Criteria:**
- Report types: by status, category, priority, staff member
- Date range filter (from date — to date)
- Visual charts (bar, pie, line)
- Average resolution time report
- SLA compliance report

**Priority:** P2 | **Points:** 5

---

### US-702: Export Reports
**As an** admin,
**I want to** export reports to PDF or Excel,
**So that** I can share them offline.

**Acceptance Criteria:**
- Export current report view as PDF
- Export ticket data as Excel (CSV/XLSX)
- Exported file includes filters applied and date generated

**Priority:** P2 | **Points:** 3

---

## Epic 8: Deployment & Infrastructure — P1 (Should Have)

### US-801: Docker Containerization
**As a** developer,
**I want to** run the entire application stack with a single `docker compose up` command,
**So that** I can develop and test without installing Node.js, PostgreSQL, or any other dependencies locally.

**Acceptance Criteria:**
- `docker compose up --build` starts all three services (postgres, server, client)
- Backend waits for PostgreSQL health check before starting
- Frontend accessible at http://localhost (port 80)
- Backend API reachable via Nginx proxy at http://localhost/api
- Database data persists across container restarts (named volume)
- `docker compose down` cleanly stops all services
- `.env.docker.example` template committed; actual `.env.docker` is gitignored

**Priority:** P1 | **Points:** 5

---

### US-802: Production Deployment via Coolify
**As an** admin / ops team,
**I want to** deploy Right Tracker to a production server via Coolify,
**So that** the application is accessible on a custom domain with SSL, without manual server configuration.

**Acceptance Criteria:**
- Application deploys from Git repository via Coolify Docker Compose resource type
- Accessible at configured domain with HTTPS
- Environment variables managed through Coolify UI (not committed to Git)
- Database data persists across redeployments
- Push to main branch triggers automatic redeployment
- Rollback to a previous commit possible via Coolify UI

**Priority:** P1 | **Points:** 3

---

## Epic 9: Security & Data Protection — P0 (Must Have)

### US-901: Secure Authentication
**As a** user,
**I want** my login credentials to be securely handled,
**So that** my account cannot be compromised.

**Acceptance Criteria:**
- Passwords are hashed with bcrypt (10 salt rounds) before storage
- JWT tokens expire after 24 hours
- Failed login returns generic "Invalid credentials" message (no user enumeration)
- Deactivated accounts cannot log in
- Token is invalidated on logout (cleared from client)
- Password policy enforced: minimum 8 characters, 1 uppercase, 1 number

**Priority:** P0 | **Points:** 3

---

### US-902: API Security & Rate Limiting
**As an** admin,
**I want** the API to be protected against common attacks,
**So that** the system remains available and data stays safe.

**Acceptance Criteria:**
- Helmet.js sets security headers on all responses (X-Frame-Options, X-Content-Type-Options, HSTS)
- CORS restricts requests to the configured frontend URL only
- Rate limiting: 100 requests per 15 minutes per IP (general)
- Login/register endpoints limited to 5 requests per 15 minutes per IP
- Rate limit exceeded returns HTTP 429 with clear message
- Request body size limited to 10MB
- All user inputs validated and sanitized before processing

**Priority:** P0 | **Points:** 5

---

### US-903: Data Protection & Privacy
**As an** admin,
**I want** sensitive data to be protected at all times,
**So that** no data breach can expose user information.

**Acceptance Criteria:**
- Passwords are never returned in any API response
- JWT secret is stored in environment variables (never hardcoded)
- Database credentials are in environment variables
- .env files are excluded from version control (.gitignore)
- Production errors do not expose stack traces or internal details
- All database queries use parameterized queries (Prisma ORM) to prevent SQL injection
- File uploads validate MIME type and enforce size limits

**Priority:** P0 | **Points:** 3

---

## Summary

| Epic                        | Priority | Stories | Total Points |
| --------------------------- | -------- | ------- | ------------ |
| 1. Authentication & Users   | P0       | 6       | 16           |
| 2. Ticket Management        | P0       | 9       | 30           |
| 3. Comments & Notes         | P0       | 2       | 5            |
| 4. Attachments              | P1       | 2       | 5            |
| 5. Dashboard                | P1       | 3       | 16           |
| 6. Notifications            | P1       | 2       | 10           |
| 7. Reports & Analytics      | P2       | 2       | 8            |
| 8. Deployment & Infra       | P1       | 2       | 8            |
| 9. Security & Data Protection | P0       | 3       | 11           |
| **Total**                   |          | **31**  | **109**      |

### Points by Priority

| Priority              | Stories | Points | % of Total |
| --------------------- | ------- | ------ | ---------- |
| P0 — Must Have        | 15      | 47     | 43%        |
| P1 — Should Have      | 13      | 52     | 48%        |
| P2 — Nice to Have     | 3       | 10     | 9%         |

---

### POC Coverage

The POC will cover the following user stories (partially or fully):

| Story  | Title                  | POC Scope                                    |
| ------ | ---------------------- | -------------------------------------------- |
| US-101 | User Registration      | Full (without welcome email)                 |
| US-102 | User Login             | Full                                         |
| US-103 | User Logout            | Full                                         |
| US-106 | User Management        | Partial (GET /users/staff endpoint only)     |
| US-201 | Create Ticket          | Full (without attachments)                   |
| US-202 | View Ticket List       | Full (without pagination)                    |
| US-203 | View Ticket Details    | Partial (without comments/attachments)       |
| US-204 | Assign Ticket          | Full (without notifications)                 |
| US-205 | Update Ticket Status   | Full (with transition validation)            |
| US-206 | Edit Ticket            | Full (PUT /tickets/:id for field updates)    |
| US-501 | Admin Dashboard        | Partial (stats cards only, no charts)        |
| US-502 | Support Staff Dashboard     | Partial (assigned + unassigned counts)       |
| US-503 | Employee Dashboard     | Partial (ticket list + create button)        |
| US-901 | Secure Authentication  | Full (bcrypt + JWT + generic errors)     |
| US-902 | API Security           | Full (Helmet + CORS + rate limiting)     |
| US-903 | Data Protection        | Full (env vars, no password exposure)    |

> **Note:** US-801 (Docker) is relevant to POC as an alternative dev environment. US-802 (Coolify) applies to production deployment (Week 8).

---

*This document is currently in **Draft** status. Subject to review and revision.*

---

*End of User Stories — Right Tracker v0.1 (Draft)*
