# Project Plan

## 1. Project Overview
This project delivers a task-tracking application composed of a backend API, a frontend interface, and a shared PostgreSQL database. It is designed for local development with Docker Compose and for deployment in AWS-based infrastructure using versioned container images.

## 2. Objectives
- Build a working task management API with CRUD support.
- Provide a frontend page that interacts with the backend through `/api` routes.
- Maintain database health and service health checks.
- Ensure the local Docker stack runs reliably.
- Support secure, versioned production deployment and rollback workflows.
- Enforce CI checks and team review requirements before merge.

## 3. Scope
### In Scope
- Backend API implementation in `services/backend`
- Frontend app implementation in `services/frontend`
- PostgreSQL database integration and health checks
- Docker and Compose setup for local development and production deployment
- CI validation for lint, formatting, tests, builds, and image scanning
- Automated deployment and rollback scripts

### Out of Scope
- Unrelated business features beyond the task tracker
- Extra services beyond the backend, frontend, and database model
- Ownership of infrastructure outside the repository-managed app deployment flow

## 4. Architecture
### Components
- Backend: Node.js service running on port `3000`
- Frontend: Node.js service running on port `8080`
- Database: PostgreSQL service used by the backend

### Routing Contract
- `/api/*` routes to the backend service
- All other requests route to the frontend application
- Frontend health endpoint: `GET /health`
- Backend health endpoint: `GET /api/health`
- Database health endpoint: `GET /api/health/db`

### API Contract
- `GET /api/tasks` reads all tasks
- `POST /api/tasks` creates a task
- `PATCH /api/tasks/:id` toggles task completion using `{ "done": true }`
- `DELETE /api/tasks/:id` deletes a task and returns `204`

## 5. Technical Requirements
- Node.js version: `24`
- PostgreSQL version: `17`
- Docker Compose for local stack orchestration
- Multi-stage Dockerfiles for backend and frontend
- Non-root container execution with user `1000`
- `.env` files excluded from source control
- Versioned image tags using `sha-<7>` and `v<VERSION>.<run-number>` patterns
- No `latest` tag pushed to ECR

## 6. Development Workstreams
### Backend CODEX
- Create task endpoints for read, create, update, and delete
- Add database connectivity and startup schema creation
- Implement health-check logic for app status and database status
- Validate environment variables for local and AWS behavior

### Frontend CLAUDE
- Display task data from the API
- Keep the backend address out of client-side code for local deployment pattern
- Route API calls correctly through the same host
- Expose a valid `/health` endpoint

### Database GROK
- Configure PostgreSQL for local Docker Compose usage
- Support AWS-managed secrets and SSL configuration
- Ensure database health checks fail cleanly when Postgres is unavailable

### Deployment and Containerization
- Build backend/frontend Dockerfiles and `.dockerignore` files
- Verify compose files for local and production execution
- Align image tags and deployment scripts with AWS ECR and SSM patterns
- Support rollback and smoke testing operations

### Quality and Validation
- Run linting and format checks
- Execute unit tests with coverage thresholds
- Run Dockerfile linting and vulnerability scanning
- Confirm build images succeed before release

## 7. Milestones
### Milestone 1: Setup
- Confirm repository layout and required environment versions
- Validate Docker and Node.js prerequisites
- Ensure project configuration and environment files are in place

### Milestone 2: Backend and Data
- Implement tasks API
- Add database connectivity and health checks
- Confirm API behavior for success and error cases

### Milestone 3: Frontend Integration
- Implement task UI and API consumption
- Verify frontend routing and health endpoints
- Validate end-to-end user interactions

### Milestone 4: Containerization
- Build Dockerfiles and Compose configuration
- Run the stack locally with Docker
- Verify service startup and health status

### Milestone 5: CI/CD and Deployment
- Validate pipeline commands
- Build and scan container images
- Confirm AWS deployment, version tagging, and rollback flow

## 8. Validation Checklist
The project is complete only when all of the following are satisfied:
- Docker Compose starts the backend, frontend, and database successfully
- Backend health endpoints return the expected status codes and payloads
- Database health returns `503` when Postgres is unavailable
- Task CRUD operations work correctly
- Frontend loads correctly and calls `/api/tasks` without embedding backend URLs
- Lint, format, and tests all pass
- Docker images build successfully
- Image vulnerability scans pass according to policy
- Deployment and rollback scripts are functional

## 9. Risks and Mitigations
### Risk: Database connectivity issues
Mitigation: Validate credentials, host configuration, and DB health checks for both local and AWS environments.

### Risk: Routing mismatches
Mitigation: Keep frontend API calls aligned with `/api` routing and validated against the backend contract.

### Risk: CI failures
Mitigation: Run validation commands before PRs and ensure branch protection and review policies are followed.

### Risk: Deployment drift
Mitigation: Use pinned tags, environment-driven configuration, and smoke tests before and after deployment.

## 10. Branching and Collaboration
- Work happens on short-lived branches from `main`
- Branch naming conventions:
  - `feature/<short-description>`
  - `fix/<short-description>`
  - `docs/<short-description>`
- Pull requests require at least one approving reviewer and passing CI checks
- No direct pushes to `main`
- Commit messages should follow `type(scope): summary`

## 11. Local Run Commands
```bash
docker compose up --build -d

docker compose ps

docker compose logs -f backend

docker compose down

docker compose down -v
```

## 12. Verification Commands
From each service directory:
```bash
npm test
npm run lint
npm run format:check
```

From the repository root:
```bash
./scripts/build-and-test.sh
```

## 13. Definition of Done
The project is considered complete when the application, containerized stack, validation checks, and deployment workflows are all functioning in line with the project contract and repository documentation.
