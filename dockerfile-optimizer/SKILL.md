---
name: dockerfile-optimizer
description: >-
  Analyze and optimize Dockerfiles for image size, layer caching, security,
  and build speed. Use when the user asks to optimize a Dockerfile, reduce
  image size, harden a container, improve build performance, or review
  Docker configuration. Produces scored analysis with before/after diffs.
version: "1.0.0"
tools:
  - Read
  - Write
  - Bash
  - Grep
  - Glob
---

## Dockerfile Optimizer

Analyze Dockerfiles across four dimensions and produce prioritized, actionable optimizations with estimated impact.

## Workflow

1. **Locate and read the Dockerfile(s)**
   Search for Dockerfile, Dockerfile.*, or *.dockerfile in the project. Read all found files. Also check for .dockerignore and docker-compose.yml for context.

2. **Pass 1 — Image Size Analysis**
   Check for:
   - Using slim/alpine/distroless base images where appropriate
   - Combining RUN commands to reduce layers
   - Cleaning package manager caches (apt clean, apk --no-cache, pip --no-cache-dir)
   - Multi-stage build usage to exclude build dependencies
   - Unnecessary files copied into the image (check .dockerignore)
   - Using COPY instead of ADD (ADD has implicit tar extraction)
   - Installing only production dependencies (npm ci --omit=dev, pip install --no-dev)
   Score: 1-10. Estimate total size reduction in MB/GB.

3. **Pass 2 — Layer Caching Analysis**
   Check for:
   - Ordering instructions from least to most frequently changing
   - Separate dependency install step before COPY . .
   - Using BuildKit cache mounts for package managers
   - Avoiding COPY . . before dependency installation
   - Using .dockerignore to prevent cache invalidation
   Score: 1-10. Estimate rebuild time improvement.

4. **Pass 3 — Security Hardening**
   Check for:
   - Running as non-root user (USER directive)
   - Using specific image tags, not :latest
   - No secrets in ENV or ARG that leak into final image
   - No unnecessary ports exposed
   - Using read-only filesystem where possible
   - Scanning for known CVEs in base image
   - Dropping capabilities (CAP_DROP ALL)
   - Using HEALTHCHECK
   Score: 1-10. List specific vulnerabilities found.

5. **Pass 4 — Build Speed**
   Check for:
   - BuildKit syntax enabled (# syntax=docker/dockerfile:1)
   - Parallel build stages where possible
   - Using --mount=type=cache for apt/pip/npm caches
   - Avoiding unnecessary .dockerignore exclusions that force full rebuilds
   - Using ADD for remote URLs instead of RUN curl
   Score: 1-10. Estimate build time savings.

6. **Generate the report**
   Format as a structured report:

   ## Optimization Report

   | Dimension       | Score | Impact    |
   |-----------------|-------|-----------|
   | Image Size      | 7/10  | -340MB    |
   | Layer Caching   | 5/10  | -45s      |
   | Security        | 6/10  | 3 issues  |
   | Build Speed     | 8/10  | -12s      |

   Then list each optimization as:
   - **Priority**: High/Medium/Low
   - **Issue**: What's wrong
   - **Fix**: What to change (with code diff)
   - **Impact**: Estimated benefit

7. **Write the optimized Dockerfile**
   If requested, write the optimized version with comments explaining each change.

## Rules

- Always explain WHY each change matters, not just what to change
- Show the diff (before → after) for every optimization
- Estimate size/time savings conservatively
- Don't change the application's runtime behavior — only optimize infrastructure
- If the Dockerfile is already well-optimized, say so honestly
- Always check if the project uses docker-compose before changing port mappings
- Preserve the original EXPOSE ports and CMD/ENTRYPOINT
- If multi-stage is recommended, ensure the final stage is clean (no build tools)

## Example Optimized Multi-Stage Pattern

```dockerfile
# syntax=docker/dockerfile:1
FROM node:20-alpine AS deps
WORKDIR /app
COPY package.json package-lock.json ./
RUN --mount=type=cache,target=/root/.npm \\
    npm ci --omit=dev

FROM node:20-alpine AS builder
WORKDIR /app
COPY package.json package-lock.json ./
RUN --mount=type=cache,target=/root/.npm \\
    npm ci
COPY . .
RUN npm run build

FROM node:20-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production
RUN addgroup --system --gid 1001 nodejs && \\
    adduser --system --uid 1001 appuser
COPY --from=deps /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/package.json ./
USER appuser
EXPOSE 3000
HEALTHCHECK --interval=30s --timeout=3s \\
  CMD wget -q --spider http://localhost:3000/health || exit 1
CMD ["node", "dist/server.js"]
```
