# Proof of Concept (POC)

## Right Tracker — IT Office Ticket Management System

| Field            | Detail                          |
| ---------------- | ------------------------------- |
| **Project Name** | Right Tracker                   |
| **Version**      | 0.1                             |
| **Date**         | 2026-03-27                      |
| **Status**       | Draft                           |
| **POC Duration** | 1 Week                          |

---

## 1. Objective

Validate that the core ticket management workflow can be built using the chosen tech stack (React + Node.js + PostgreSQL). The POC will prove that the system's foundation is technically sound before committing to full development.

---

## 2. What the POC Will Prove

| # | Question to Answer                                       | How We Prove It                          |
|---|----------------------------------------------------------|------------------------------------------|
| 1 | Can users register and log in with role-based access?    | JWT auth with 3 roles working            |
| 2 | Can employees create and track tickets?                  | Ticket CRUD with status flow             |
| 3 | Can IT staff be assigned tickets and update status?      | Assignment + status transitions working  |
| 4 | Can the system store and retrieve data from PostgreSQL?  | All CRUD operations hit the database     |
| 5 | Does the React frontend communicate with the backend?    | API integration end-to-end               |
| 6 | Does role-based access control work correctly?           | Unauthorized actions are blocked         |

---

## 3. POC Scope

### 3.1 In Scope (Build These)

#### Backend (Node.js + Express + PostgreSQL)
- [ ] Project setup with Express.js
- [ ] PostgreSQL connection with Sequelize ORM
- [ ] User model (name, email, password, role, department, isActive)
- [ ] Ticket model (ticketNo, title, description, category, priority, status, createdBy, assignedTo)
- [ ] Auth APIs: Register, Login (JWT), Get Current User
- [ ] Auth middleware (JWT verification, role-based authorization)
- [ ] Ticket APIs: Create, Read (list + detail), Update, Assign, Update Status
- [ ] Dashboard API: Stats endpoint (counts by status, priority, category)
- [ ] Request validation (express-validator)
- [ ] Global error handling middleware
- [ ] Status transition validation
- [ ] Helmet.js security headers
- [ ] CORS configuration (restrict to CLIENT_URL)
- [ ] Rate limiting (100 req/15min general, 5 req/15min login)
- [ ] Input sanitization on all endpoints
- [ ] Password hashing with bcrypt (10 salt rounds)
- [ ] Generic error messages (no user enumeration)
- [ ] Environment-based error handling (no stack traces in production)

#### Frontend (React + Vite)
- [ ] Project setup with Vite
- [ ] Login page with error handling
- [ ] Dashboard page (different view per role)
- [ ] Create Ticket form (title, description, category, priority)
- [ ] Ticket List page (with status and priority badges)
- [ ] Ticket Detail page (view + update status + assign)
- [ ] Protected routes (redirect to login if not authenticated)
- [ ] Role-based navigation (sidebar)
- [ ] Axios API integration with JWT interceptor
- [ ] Auth context (state management)

#### Database (PostgreSQL)
- [ ] Users table with indexes
- [ ] Tickets table with foreign keys and indexes
- [ ] Seed data: 1 admin, 2 IT staff, 3 employees, 5 sample tickets

### 3.2 Out of Scope (NOT in POC)
- Comments / internal notes
- File attachments
- Email notifications
- Real-time notifications (Socket.io)
- Reports and analytics
- Search and advanced filtering
- Password reset
- Profile management
- UI polish / responsive design
- UI component library (Ant Design / MUI)
- SLA tracking / sla_deadline column

- Account lockout after failed attempts (full version)
- Content Security Policy headers (full version)
- Security event logging/alerting (full version)
- Two-factor authentication (future)

> **Note:** SLA tracking (including the `sla_deadline` column) is deferred to the full build.

---

## 4. Tech Setup

### 4.1 Backend Structure
```
server/
├── config/
│   └── db.js                    # Sequelize instance + PostgreSQL connection
├── controllers/
│   ├── authController.js        # register, login, getMe
│   ├── ticketController.js      # CRUD, assign, updateStatus
│   └── dashboardController.js   # stats endpoint
├── middleware/
│   ├── auth.js                  # JWT verification (protect)
│   ├── role.js                  # Role authorization (authorize)
│   ├── errorHandler.js          # Global error handler
│   └── validate.js              # express-validator runner
├── models/
│   ├── index.js                 # Model registry + associations
│   ├── User.js                  # User model definition
│   └── Ticket.js                # Ticket model definition
├── routes/
│   ├── authRoutes.js            # /api/auth/*
│   ├── ticketRoutes.js          # /api/tickets/*
│   └── dashboardRoutes.js       # /api/dashboard/*
├── validators/
│   ├── authValidator.js         # register + login validation rules
│   └── ticketValidator.js       # ticket CRUD validation rules
├── utils/
│   ├── AppError.js              # Custom error class
│   └── generateTicketNo.js      # TKT-XXXX auto-generator
├── seeders/
│   └── seed.js                  # Sample data script
├── .env                         # Environment variables
├── .env.example                 # Template for .env
├── .gitignore                   # node_modules, .env
├── server.js                    # Entry point
└── package.json
```

### 4.2 Frontend Structure
```
client/
├── src/
│   ├── components/
│   │   ├── Layout.jsx           # Header + Sidebar + Outlet
│   │   ├── Layout.css
│   │   ├── Sidebar.jsx          # Role-based navigation
│   │   ├── Sidebar.css
│   │   ├── ProtectedRoute.jsx   # Auth guard + role check
│   │   ├── StatusBadge.jsx      # Color-coded status display
│   │   └── PriorityBadge.jsx    # Color-coded priority display
│   ├── pages/
│   │   ├── Login.jsx            # Login form + seed credentials hint
│   │   ├── Login.css
│   │   ├── Dashboard.jsx        # Role-specific dashboard
│   │   ├── Dashboard.css
│   │   ├── TicketList.jsx       # Ticket table with badges
│   │   ├── TicketList.css
│   │   ├── CreateTicket.jsx     # New ticket form
│   │   ├── CreateTicket.css
│   │   ├── TicketDetail.jsx     # Ticket info + actions (assign/status)
│   │   └── TicketDetail.css
│   ├── context/
│   │   └── AuthContext.jsx      # Auth state (user, token, login, logout)
│   ├── services/
│   │   └── api.js               # Axios instance + all API functions
│   ├── utils/
│   │   └── constants.js         # Status/priority colors, categories, role labels
│   ├── App.jsx                  # Route definitions
│   ├── index.css                # Global styles
│   └── main.jsx                 # Entry point
├── .env                         # VITE_API_URL
├── index.html
├── vite.config.js
└── package.json
```

### 4.3 Environment Variables

#### Server (.env)
```
PORT=5000
DB_HOST=localhost
DB_PORT=5432
DB_NAME=right_tracker
DB_USER=postgres
DB_PASSWORD=yourpassword
JWT_SECRET=right-tracker-jwt-secret-key-2026
JWT_EXPIRES_IN=7d
CLIENT_URL=http://localhost:5173
RATE_LIMIT_WINDOW_MS=900000
RATE_LIMIT_MAX=100
LOGIN_RATE_LIMIT_MAX=5
```

#### Client (.env)
```
VITE_API_URL=http://localhost:5000/api
```

### 4.4 Docker File Structure

```
Right Tracker/
├── client/
│   ├── Dockerfile          # Multi-stage: Vite build → Nginx
│   ├── .dockerignore
│   └── nginx.conf          # SPA routing + API proxy
├── server/
│   ├── Dockerfile          # Multi-stage: Node.js Alpine
│   └── .dockerignore
├── docker-compose.yml      # 3 services: postgres, server, client
├── docker-compose.prod.yml # Production overrides
└── .env.docker.example     # Docker environment template
```

### 4.5 Backend Dockerfile

Multi-stage build for the Node.js API server:

```dockerfile
# Stage 1: Install dependencies
FROM node:18-alpine AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

# Stage 2: Production image
FROM node:18-alpine
WORKDIR /app
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN chown -R appuser:appgroup /app
USER appuser
EXPOSE 5000
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD wget --no-verbose --tries=1 --spider http://localhost:5000/api/health || exit 1
CMD ["node", "server.js"]
```

### 4.6 Frontend Dockerfile

Multi-stage build: Vite builds the static assets, Nginx serves them:

```dockerfile
# Stage 1: Build
FROM node:18-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
ARG VITE_API_URL=/api
ENV VITE_API_URL=$VITE_API_URL
RUN npm run build

# Stage 2: Serve with Nginx
FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

**nginx.conf** — SPA routing with API proxy:

```nginx
server {
    listen 80;
    root /usr/share/nginx/html;
    index index.html;

    location /api {
        proxy_pass http://server:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

### 4.7 Docker Compose

Three services on a shared `rt-network` bridge network:

```yaml
version: "3.8"

services:
  postgres:
    image: postgres:14-alpine
    restart: unless-stopped
    environment:
      POSTGRES_DB: ${DB_NAME}
      POSTGRES_USER: ${DB_USER}
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    volumes:
      - pgdata:/var/lib/postgresql/data
    networks:
      - rt-network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${DB_USER} -d ${DB_NAME}"]
      interval: 10s
      timeout: 5s
      retries: 5

  server:
    build: ./server
    restart: unless-stopped
    env_file:
      - .env.docker
    depends_on:
      postgres:
        condition: service_healthy
    ports:
      - "5000:5000"
    networks:
      - rt-network

  client:
    build:
      context: ./client
      args:
        VITE_API_URL: /api
    restart: unless-stopped
    depends_on:
      server:
        condition: service_healthy
    ports:
      - "80:80"
    networks:
      - rt-network

volumes:
  pgdata:

networks:
  rt-network:
    driver: bridge
```

### 4.8 Docker Environment File

A single `.env.docker` file provides environment variables to all services:

```
# PostgreSQL
DB_NAME=right_tracker
DB_USER=postgres
DB_PASSWORD=yourpassword

# Server
PORT=5000
DB_HOST=postgres
DB_PORT=5432
JWT_SECRET=right-tracker-jwt-secret-key-2026
JWT_EXPIRES_IN=7d
CLIENT_URL=http://localhost
```

> **Note:** `DB_HOST` is set to `postgres` (the Docker Compose service name), not `localhost`.

### 4.9 .dockerignore

Both `client/.dockerignore` and `server/.dockerignore` should contain:

```
node_modules
.env
.git
README.md
Dockerfile
```

### 4.10 Security Configuration

The POC implements the following security measures based on patterns from existing WisRight projects:

**Backend Security Middleware Stack (applied in order):**
1. `helmet()` — Sets secure HTTP headers
2. `cors({ origin: process.env.CLIENT_URL })` — Restricts cross-origin requests
3. `express.json({ limit: '10mb' })` — Limits request body size
4. `rateLimit({ windowMs: 15*60*1000, max: 100 })` — General rate limiting
5. Auth routes get stricter limit: `rateLimit({ windowMs: 15*60*1000, max: 5 })` for login/register

**Password Security:**
- Bcrypt with 10 salt rounds
- Minimum 8 characters enforced via express-validator
- Passwords never returned in API responses (toJSON override on User model)

**JWT Configuration:**
- Secret from environment variable (never hardcoded)
- 24-hour expiry (configurable via JWT_EXPIRES_IN)
- Token contains: user ID and role only (minimal payload)
- Bearer token extracted from Authorization header

**Error Handling:**
- Production: `{ success: false, message: "Error description" }` (no stack trace)
- Development: includes stack trace for debugging
- Login errors: "Invalid credentials" (same message for wrong email or wrong password)

**Dependencies to install:**
```
npm install helmet express-rate-limit
```

---

## 5. API Endpoints

| Method | Endpoint                  | Auth | Roles              | Description                              |
| ------ | ------------------------- | ---- | ------------------ | ---------------------------------------- |
| POST   | /api/auth/register        | Yes  | Admin              | Register a new user (admin only)         |
| POST   | /api/auth/login           | No   | Public             | Login, receive JWT token                 |
| GET    | /api/auth/me              | Yes  | All                | Get logged-in user info                  |
| GET    | /api/tickets              | Yes  | All (filtered)     | List tickets (role-based)                |
| POST   | /api/tickets              | Yes  | Employee           | Create a new ticket                      |
| GET    | /api/tickets/:id          | Yes  | All (access check) | Get ticket details                       |
| PUT    | /api/tickets/:id          | Yes  | Conditional        | Update ticket fields                     |
| PUT    | /api/tickets/:id/assign   | Yes  | Admin, Staff       | Assign ticket to staff                   |
| PATCH  | /api/tickets/:id/status   | Yes  | Conditional        | Update ticket status                     |
| GET    | /api/dashboard/stats      | Yes  | All (filtered)     | Dashboard statistics                     |
| GET    | /api/users/staff          | Yes  | Admin, Staff       | List staff users (for assign)            |
| GET    | /api/health               | No   | Public             | Health check for Docker/load balancer    |

> **Note:** Registration (`POST /api/auth/register`) requires admin authentication. The POC seed script creates the initial set of users (1 admin, 2 staff, 3 employees) for testing purposes.

> **Security Notes:**
> - All authenticated endpoints require a valid JWT in the `Authorization: Bearer <token>` header
> - Login and register endpoints are rate-limited to 5 requests per 15 minutes per IP
> - All other endpoints are rate-limited to 100 requests per 15 minutes per IP
> - Invalid tokens return 401; insufficient role returns 403

### Role-Based Ticket Filtering
- **Employee:** Sees only their own tickets (`WHERE created_by = user.id`)
- **Staff:** Sees their assigned tickets + open/unassigned tickets (`WHERE assigned_to = user.id OR status = 'open'`)
- **Admin:** Sees all tickets

---

## 6. Status Transition Map

```
┌──────────┐     ┌──────────┐     ┌─────────────┐
│   Open   │────>│ Assigned │────>│ In Progress │
└──────────┘     └──────────┘     └─────────────┘
                                    │         │
                                    v         v
                                ┌─────────┐ ┌──────────┐
                                │ On Hold │ │ Resolved │
                                └─────────┘ └──────────┘
                                    │         │       │
                                    v         v       v
                                ┌─────────────┐ ┌────────┐
                                │ In Progress │ │ Closed │
                                └─────────────┘ └────────┘
```

### Valid Transitions

| Current Status | Can Move To                          | Who Can Transition         |
| -------------- | ------------------------------------ | -------------------------- |
| open           | assigned                             | Admin, Staff               |
| assigned       | in_progress                          | Admin, Staff               |
| in_progress    | on_hold, resolved                    | Admin, Staff               |
| on_hold        | in_progress                          | Admin, Staff               |
| resolved       | closed, open (reopen)                | Admin (close), Employee (reopen) |
| closed         | — (terminal state)                   | —                          |

---

## 7. API Response Format

All API responses follow a consistent JSON structure:

### Success Responses
```json
// Single resource
{
  "success": true,
  "user": { "id": "...", "name": "...", "email": "...", "role": "..." }
}

// List of resources
{
  "success": true,
  "count": 5,
  "tickets": [...]
}

// With token (auth)
{
  "success": true,
  "token": "eyJhbGci...",
  "user": { ... }
}
```

### Error Responses
```json
// General error
{
  "success": false,
  "message": "Error description"
}

// Validation error
{
  "success": false,
  "errors": [
    { "field": "email", "message": "Valid email is required" },
    { "field": "password", "message": "Password must be at least 8 characters" }
  ]
}
```

---

## 8. Database Schema (POC)

### Users
```sql
CREATE TABLE users (
    id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name        VARCHAR(100) NOT NULL,
    email       VARCHAR(150) UNIQUE NOT NULL,
    password    VARCHAR(255) NOT NULL,
    role        VARCHAR(20) NOT NULL DEFAULT 'employee',
    department  VARCHAR(100),
    is_active   BOOLEAN DEFAULT true,
    created_at  TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at  TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Indexes
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_role ON users(role);
```

### Tickets
```sql
CREATE TABLE tickets (
    id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    ticket_no   VARCHAR(20) UNIQUE NOT NULL,
    title       VARCHAR(200) NOT NULL,
    description TEXT,
    category    VARCHAR(50) NOT NULL,
    priority    VARCHAR(20) NOT NULL DEFAULT 'medium',
    status      VARCHAR(20) NOT NULL DEFAULT 'open',
    created_by  UUID REFERENCES users(id),
    assigned_to UUID REFERENCES users(id),
    created_at  TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at  TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Indexes
CREATE INDEX idx_tickets_ticket_no ON tickets(ticket_no);
CREATE INDEX idx_tickets_status ON tickets(status);
CREATE INDEX idx_tickets_priority ON tickets(priority);
CREATE INDEX idx_tickets_created_by ON tickets(created_by);
CREATE INDEX idx_tickets_assigned_to ON tickets(assigned_to);
```

> **Note:** The POC schema is simplified. The following PRD columns are deferred to the full build: `sla_deadline`, `resolved_at`, `closed_at`, `deleted_at`.

> **Note:** The POC uses VARCHAR for `role`, `category`, `priority`, and `status` fields for simplicity. The full build will use ENUM types or CHECK constraints for data integrity.

---

## 9. Seed Data

### Users
| Name             | Email                     | Role     | Password  | Department |
| ---------------- | ------------------------- | -------- | --------- | ---------- |
| Admin User       | admin@righttracker.com    | admin    | Admin@123 | IT         |
| John Tech        | john@righttracker.com     | staff    | Staff@123 | IT         |
| Jane Tech        | jane@righttracker.com     | staff    | Staff@123 | IT         |
| Alice Employee   | alice@righttracker.com    | employee | User@123  | Marketing  |
| Bob Employee     | bob@righttracker.com      | employee | User@123  | Finance    |
| Charlie Employee | charlie@righttracker.com  | employee | User@123  | HR         |

### Sample Tickets
| Ticket No | Title                      | Category | Priority | Status      | Created By | Assigned To |
| --------- | -------------------------- | -------- | -------- | ----------- | ---------- | ----------- |
| TKT-0001  | Laptop not turning on      | hardware | high     | open        | Alice      | —           |
| TKT-0002  | Cannot access shared drive | network  | medium   | assigned    | Bob        | John        |
| TKT-0003  | Install Adobe Photoshop    | software | low      | in_progress | Charlie    | Jane        |
| TKT-0004  | Email not syncing on phone | email    | medium   | open        | Alice      | —           |
| TKT-0005  | Need VPN access            | access   | high     | resolved    | Bob        | John        |

---

## 10. Setup Instructions

### Prerequisites
1. **Node.js** v18 or higher installed
2. **PostgreSQL** v14 or higher installed and running
3. **Git** installed

### Step-by-Step Setup

#### 1. Create Database
```sql
CREATE DATABASE right_tracker;
```

#### 2. Backend Setup
```bash
cd server
npm install
# Copy .env.example to .env and update database credentials
cp .env.example .env
# Run seed script to create tables and sample data
npm run seed
# Start the development server
npm run dev
```
Server will start on `http://localhost:5000`

#### 3. Frontend Setup
```bash
cd client
npm install
# Start the development server
npm run dev
```
Frontend will start on `http://localhost:5173`

#### 4. Test Login
Open `http://localhost:5173` in your browser and log in with:
- **Admin:** admin@righttracker.com / Admin@123
- **Staff:** john@righttracker.com / Staff@123
- **Employee:** alice@righttracker.com / User@123

### Alternative: Docker Setup

#### Prerequisites
- **Docker Engine** 24+ installed
- **Docker Compose** v2 installed

#### Steps

```bash
# 1. Copy the Docker environment template and update values
cp .env.docker.example .env.docker

# 2. Build and start all services (postgres, server, client)
docker compose up --build -d

# 3. Seed the database with initial users and sample tickets
docker compose exec server npm run seed

# 4. Open the application
# http://localhost
```

#### Common Docker Commands

```bash
# View logs for all services
docker compose logs -f

# View logs for a specific service
docker compose logs -f server

# Stop all services
docker compose down

# Stop and remove volumes (reset database)
docker compose down -v

# Rebuild a specific service after code changes
docker compose up --build -d server

# Access the PostgreSQL database directly
docker compose exec postgres psql -U postgres -d right_tracker
```

---

## 11. POC Success Criteria

| #  | Criteria                                                      | Test Steps                                                          | Pass/Fail |
| -- | ------------------------------------------------------------- | ------------------------------------------------------------------- | --------- |
| 1  | User can register with valid data                             | POST /api/auth/register with name, email, password                  | ⬜        |
| 2  | User can log in and receive JWT token                         | POST /api/auth/login, verify token in response                      | ⬜        |
| 3  | Invalid credentials are rejected                              | Login with wrong password, verify 401 response                      | ⬜        |
| 4  | Employee can create a ticket                                  | Login as employee, POST /api/tickets, verify 201                    | ⬜        |
| 5  | Employee sees only their own tickets                          | Login as Alice, verify only Alice's tickets appear                  | ⬜        |
| 6  | Staff sees assigned + open tickets                            | Login as John, verify assigned + unassigned tickets appear          | ⬜        |
| 7  | Admin sees all tickets                                        | Login as Admin, verify all tickets visible                          | ⬜        |
| 8  | Admin/Staff can assign a ticket                               | PUT /api/tickets/:id/assign with staff user ID                      | ⬜        |
| 9  | Staff can update ticket status                                | PATCH /api/tickets/:id/status, verify transition                    | ⬜        |
| 10 | Invalid status transitions are blocked                        | Try open → resolved directly, verify 400 error                     | ⬜        |
| 11 | Employee cannot assign or update status                       | Login as employee, try assign/status, verify 403                    | ⬜        |
| 12 | Dashboard shows correct stats per role                        | Login as each role, verify stats match visible tickets              | ⬜        |
| 13 | Data persists across server restarts                          | Restart server, verify tickets still exist                          | ⬜        |
| 14 | React frontend communicates with backend                      | Complete full flow in browser: login → create → list → detail       | ⬜        |
| 15 | Protected routes redirect to login when not authenticated     | Access /dashboard without token, verify redirect to /login          | ⬜        |
| 16 | Helmet security headers are present in responses | Check response headers for X-Frame-Options, X-Content-Type-Options | Pass/Fail |
| 17 | Rate limiting blocks excessive login attempts | Send 6 login requests rapidly, verify 429 on 6th | Pass/Fail |
| 18 | Passwords are never exposed in API responses | GET /api/auth/me, verify no password field in response | Pass/Fail |
| 19 | CORS blocks requests from unauthorized origins | Send request from different origin, verify blocked | Pass/Fail |

---

## 12. POC Timeline

| Day   | Task                                                      |
| ----- | --------------------------------------------------------- |
| Day 1 | Backend setup, DB connection, User + Ticket models        |
| Day 2 | Auth APIs, middleware (JWT, roles), validation             |
| Day 3 | Ticket CRUD APIs, status transitions, dashboard stats     |
| Day 4 | Frontend setup, Login page, Auth context, API service      |
| Day 5 | Dashboard, Ticket List, Create Ticket pages               |
| Day 6 | Ticket Detail page, assign + status update, integration   |
| Day 7 | Seed data, end-to-end testing, bug fixes, documentation   |

---

## 13. After POC — Next Steps

Once the POC is validated and all success criteria pass:

1. **Review & Approve** — Demo to stakeholders, get sign-off to proceed
2. **Phase 2 Features** — Add the following, prioritized by importance:
   - Comments (P0)
   - Attachments (P1)
   - Notifications (P1)
   - Search & Filter (P1)
   - Password Reset (P1)
   - Profile Management (P1)
   - Docker/Coolify Production Deployment (P1)
   - Reports & Analytics (P2)
3. **UI Polish** — Integrate Ant Design or MUI component library
4. **Testing** — Unit tests, integration tests, end-to-end tests
5. **Security Hardening** — HTTPS, rate limiting, input sanitization audit
6. **Deployment** — Staging environment for UAT, then production

---

## 14. Coolify Deployment

### Prerequisites

- A Linux server (VPS or dedicated) with Coolify v4 installed
- A domain name pointed to the server (e.g., `tracker.yourdomain.com`)
- The Right Tracker Git repository accessible by Coolify (GitHub, GitLab, or self-hosted)

### How Coolify Deploys Docker Compose Projects

Coolify natively supports Docker Compose projects. When you add a Docker Compose resource, Coolify will:

1. Clone your Git repository
2. Detect the `docker-compose.yml` file
3. Build all services defined in the compose file
4. Manage networking, SSL certificates, and reverse proxy automatically
5. Provide a web UI for logs, environment variables, and redeployments

### Setup Steps

#### Step 1: Add a New Project in Coolify

- Log in to your Coolify dashboard
- Click **"New Project"** and give it a name (e.g., "Right Tracker")
- Select the environment (e.g., "Production")

#### Step 2: Add a Docker Compose Resource

- Inside the project, click **"Add New Resource"**
- Select **"Docker Compose"** as the resource type
- Connect your Git repository (GitHub/GitLab) and select the branch (e.g., `main`)
- Coolify will detect the `docker-compose.yml` in the repository root

#### Step 3: Configure Environment Variables

- In the resource settings, go to **"Environment Variables"**
- Add all variables from `.env.docker`:

```
DB_NAME=right_tracker
DB_USER=postgres
DB_PASSWORD=<strong-production-password>
PORT=5000
DB_HOST=postgres
DB_PORT=5432
JWT_SECRET=<strong-random-secret>
JWT_EXPIRES_IN=7d
CLIENT_URL=https://tracker.yourdomain.com
```

> **Important:** Use strong, unique passwords and secrets for production. Do not reuse the development defaults.

#### Step 4: Configure Domain and SSL

- In the resource settings, go to **"Domains"**
- Add your domain: `tracker.yourdomain.com`
- Map the domain to the `client` service on port 80
- Enable **"Auto SSL"** — Coolify will provision a Let's Encrypt certificate automatically

#### Step 5: Configure Persistent Storage

- In the resource settings, go to **"Storages"**
- Confirm the `pgdata` named volume is configured for the `postgres` service
- This ensures database data survives redeployments

#### Step 6: Deploy

- Click **"Deploy"**
- Monitor the build logs in the Coolify UI
- Coolify will build all three services (postgres, server, client) and start them

#### Step 7: Seed the Database

After the first successful deployment, seed the database with initial data:

- In Coolify, open the **"Terminal"** for the `server` service
- Run: `npm run seed`
- Alternatively, use Coolify's execute command feature:
  ```
  docker compose exec server npm run seed
  ```

### Ongoing Operations

| Task              | How                                                                      |
| ----------------- | ------------------------------------------------------------------------ |
| **Redeploy**      | Push to the configured branch, or click "Deploy" in Coolify              |
| **View Logs**     | Click "Logs" on any service in the Coolify dashboard                     |
| **Restart**       | Click "Restart" on the resource in Coolify                               |
| **Update Env**    | Edit environment variables in Coolify settings, then redeploy            |
| **Backup DB**     | Use Coolify's built-in backup feature for the postgres volume            |
| **SSL Renewal**   | Automatic — Coolify renews Let's Encrypt certificates before expiry      |
| **Rollback**      | Use Coolify's deployment history to roll back to a previous build        |

---

*This document is currently in **Draft** status. Subject to review and revision.*

---

*End of POC — Right Tracker v0.1 (Draft)*
