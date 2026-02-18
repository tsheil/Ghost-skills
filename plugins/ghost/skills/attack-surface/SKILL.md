---
name: "ghost-attack-surface"
description: "Ghost Security - Attack surface enumeration. Maps all API endpoints, authentication flows, input entry points, and high-value targets in a codebase. Use when starting a security review, preparing for bug bounty hunting, or needing to understand the full attack surface of an application before running targeted scans."
allowed-tools: Read, Write, Edit, Glob, Grep, Bash
license: apache-2.0
metadata:
  version: 1.1.0
---

# Attack Surface Enumeration

You enumerate the full attack surface of a repository by mapping endpoints, authentication flows, input entry points, and high-value targets. Do all work yourself — do not spawn subagents or delegate.

## Inputs

Parse these from `$ARGUMENTS` (key=value pairs):
- **repo_path**: path to the repository root (defaults to current working directory)
- **cache_dir**: path to the cache directory (defaults to `~/.ghost/repos/<repo_id>/cache`)

$ARGUMENTS

If `cache_dir` is not provided, compute it:
```bash
repo_name=$(basename "$(pwd)") && remote_url=$(git remote get-url origin 2>/dev/null || pwd) && short_hash=$(printf '%s' "$remote_url" | git hash-object --stdin | cut -c1-8) && repo_id="${repo_name}-${short_hash}" && cache_dir="$HOME/.ghost/repos/${repo_id}/cache" && echo "cache_dir=$cache_dir"
```

## Tool Restrictions

Do NOT use WebFetch or WebSearch. All work must use only local files in the repository.

---

## Check Cache First

Check if `<cache_dir>/attack-surface.md` already exists. If it does, skip everything and return:

```
Attack surface map is at: <cache_dir>/attack-surface.md
```

If it does not exist, run `mkdir -p <cache_dir>` and continue.

---

## Workflow

### Step 1: Load Repository Context

Read `<cache_dir>/repo.md` if it exists. Extract project types, base paths, frameworks, and languages. If it does not exist, run the repo-context skill first and then continue.

### Step 2: Enumerate API Endpoints

For each project detected, find all route definitions and API endpoints:

**Backend patterns to search for:**
- Route registrations: `router.Get`, `app.post`, `@RequestMapping`, `@app.route`, `@router.`, `HandleFunc`, `r.HandleFunc`
- REST framework routes: `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`, `@PatchMapping`
- Express/Koa/Fastify: `app.get`, `app.post`, `router.get`, `fastify.get`
- Django: `path(`, `url(`, `urlpatterns`
- Flask: `@app.route`, `@blueprint.route`
- Rails: `resources :`, `get '`, `post '`, `match '`
- GraphQL: `type Query`, `type Mutation`, `@Resolver`, `resolvers`
- gRPC: `service `, `rpc `, `.proto` files
- WebSocket: `ws.on`, `socket.on`, `@OnMessage`, `@SubscribeMessage`

For each endpoint, record: HTTP method, path/pattern, handler function, file location, and whether authentication middleware is applied.

### Step 3: Map Authentication and Authorization

Identify:
- Authentication middleware and where it is applied (which routes are protected vs unprotected)
- Authentication mechanisms (JWT, session, API key, OAuth, basic auth)
- Authorization/RBAC middleware and role definitions
- Routes that are explicitly public or unauthenticated
- Admin or privileged endpoint groups
- OAuth/SSO integration points (authorize URLs, callback handlers, token endpoints)
- Password reset and account recovery flows

### Step 4: Identify Input Entry Points

Catalog all sources of user-controlled input:
- Request body parsing (JSON, XML, form data, multipart)
- Query parameters and path parameters
- HTTP headers used in application logic (not just standard ones)
- File upload endpoints
- WebSocket message handlers
- Webhook receivers
- Cron jobs or queue workers that process external data
- Email/SMS parsing endpoints
- GraphQL variables and arguments

### Step 5: Flag High-Value Targets

Identify the highest-value targets for bounty hunting:

**Critical priority:**
- Endpoints handling financial transactions (payments, transfers, balance operations)
- Authentication endpoints (login, registration, password reset, MFA enrollment)
- Authorization boundaries (admin panels, role changes, permission updates)
- File upload/download endpoints
- Endpoints that make outbound HTTP requests (SSRF surface)
- Endpoints that execute system commands or interact with the OS
- Endpoints that construct database queries from user input
- OAuth/SSO flows (redirect_uri handling, state validation, token exchange)

**High priority:**
- User profile/account management (update, delete, export)
- API key/token generation and management
- Search and filtering endpoints (potential for injection)
- Data export/import functionality
- Invitation or sharing flows
- Rate-limited or metered operations
- WebSocket real-time endpoints
- GraphQL endpoints (introspection, nested queries)

**Medium priority:**
- Notification/email sending endpoints
- Logging or audit endpoints
- Configuration endpoints
- Health/status/metrics endpoints accessible without auth

### Step 6: Analyze Trust Boundaries

Map where trust transitions occur:
- Public internet to authenticated zone
- User role to admin role
- Frontend to backend API
- Backend to internal microservices
- Application to database layer
- Application to external APIs or third-party services
- Multi-tenant boundaries (data isolation between tenants)

### Step 7: Write Attack Surface Map

Write `<cache_dir>/attack-surface.md` with this structure:

```markdown
# Attack Surface Map

## Overview
- Total endpoints: <count>
- Authenticated endpoints: <count>
- Unauthenticated endpoints: <count>
- File upload endpoints: <count>
- Outbound HTTP request points: <count>
- GraphQL endpoints: <count>
- WebSocket endpoints: <count>

## High-Value Targets

### Critical Priority
<Table of critical-priority endpoints with: Endpoint, Method, Auth Required, Handler Location, Why Critical>

### High Priority
<Table of high-priority endpoints>

## All Endpoints

### <Project Name> (<type>)

| Method | Path | Auth | Handler | File:Line | Notes |
|--------|------|------|---------|-----------|-------|
| <method> | <path> | <yes/no/role> | <function> | <file:line> | <notes> |

## Authentication Flows
<Description of auth mechanisms, where they are applied, and gaps>

## Input Entry Points
<Catalog of all input sources beyond standard request params>

## Trust Boundaries
<Map of trust transitions>

## Recommended Scan Targets
<Prioritized list of endpoints/files to scan first, with reasoning>
```

### Step 8: Validate and Output

Read `<cache_dir>/attack-surface.md` back and verify it contains the expected sections. If the file is missing or malformed, retry the write once before reporting an error.

Return: `Attack surface map is at: <cache_dir>/attack-surface.md`
