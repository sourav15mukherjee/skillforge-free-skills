---
name: api-docs-writer
description: >-
  Generate comprehensive API documentation from source code. Use when the user
  asks to document APIs, create API docs, generate endpoint documentation,
  or write API reference. Scans route handlers and produces structured docs
  with endpoints, parameters, and examples.
version: "1.0.0"
tools:
  - Read
  - Grep
  - Glob
  - Write
---

## API Docs Writer

Generate complete API documentation by analyzing route handler source code.

## Workflow

1. **Find all API routes**
   Search for API route files:
   - Next.js: `app/api/**/route.ts`
   - Express: files with `router.get/post/put/delete`
   - FastAPI: files with `@app.get/post/put/delete`

2. **Analyze each route**
   For every endpoint, extract:
   - HTTP method (GET, POST, PUT, DELETE)
   - URL path and path parameters
   - Query parameters
   - Request body schema (from TypeScript types or validation)
   - Response schema (from return statements)
   - Authentication requirements
   - Error responses

3. **Generate documentation** using this format for each endpoint:

```markdown
### [METHOD] /api/path

[One-line description]

**Authentication:** Required / None

**Request**

| Parameter | Type   | In    | Required | Description |
|-----------|--------|-------|----------|-------------|
| id        | string | path  | yes      | Resource ID |

**Request Body**
\`\`\`json
{
  "field": "value"
}
\`\`\`

**Response (200)**
\`\`\`json
{
  "data": { ... }
}
\`\`\`

**Error Responses**
| Status | Description |
|--------|-------------|
| 401    | Unauthorized |
| 404    | Not found   |
```

4. **Write the output**
   Create or update the docs file (e.g., `docs/API.md` or `API_REFERENCE.md`).

## Rules

- Include curl examples for each endpoint
- Show both success and error response examples
- Group endpoints by resource/domain
- Note any rate limits or special headers required
- Include authentication details (Bearer token, API key, cookies)
- Link to related endpoints where relevant
