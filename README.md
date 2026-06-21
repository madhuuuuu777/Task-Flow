Task Manager API
A scalable REST backend for a multi-tenant task management platform.
Built with Express, PostgreSQL, and JWT auth.
Setup
```bash
createdb taskmanager
psql taskmanager < schema.sql
cp .env.example .env   # fill in DATABASE_URL and JWT_SECRET
npm install
npm start
```
Server runs on `http://localhost:3000` by default.
Auth
All routes except `/api/auth/*` and `/health` require a header:
```
Authorization: Bearer <token>
```
Endpoints
Method	Path	Description
POST	`/api/auth/register`	Create a user, returns JWT
POST	`/api/auth/login`	Login, returns JWT
POST	`/api/workspaces`	Create a workspace (caller becomes admin)
GET	`/api/workspaces`	List workspaces the user belongs to
POST	`/api/workspaces/:workspaceId/members`	Add/update a member's role (admin only)
POST	`/api/workspaces/:workspaceId/projects`	Create a project
GET	`/api/workspaces/:workspaceId/projects`	List projects in a workspace
POST	`/api/projects/:projectId/tasks`	Create a task
GET	`/api/projects/:projectId/tasks?status=&assignee_id=`	List/filter tasks
PATCH	`/api/tasks/:taskId`	Update task fields (status, priority, assignee, position, etc.)
DELETE	`/api/tasks/:taskId`	Delete a task
POST	`/api/tasks/:taskId/comments`	Add a comment
GET	`/api/tasks/:taskId/comments`	List comments on a task
GET	`/health`	Health check (for load balancers)
Roles
Workspace members have one of: `admin`, `member`, `viewer`.
Use the `requireRole([...])` middleware pattern in `server.js` to gate any new route.
Scaling this further
Add Redis caching for `GET /projects/:projectId/tasks` (high-read path)
Move comment/notification side effects to a queue (BullMQ/RabbitMQ)
Add a WebSocket layer (Socket.io) for live task updates, using Redis pub/sub to fan out across multiple API instances
Add database read replicas once read traffic outgrows a single Postgres instance
Add pagination to list endpoints before they're used at scale
