---
name: data-schema-validator
description: >-
  Validate JSON/YAML schemas, generate TypeScript interfaces and Zod schemas
  from data samples, and detect schema drift. Use when the user asks to
  generate types from JSON, create a Zod schema, validate API responses,
  check data shapes, or compare schema versions.
version: "1.0.0"
tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## Data Schema Validator

Validate data structures, generate type-safe schemas, and detect drift between expected and actual data shapes.

## Workflow

1. **Collect data samples**
   Gather data from JSON/YAML files, API responses, or inline data.

2. **Analyze the data shape**
   Recursively map every field:
   - Type(s) observed per field
   - Optional vs required (present in all samples?)
   - Nullable fields
   - Array item types (homogeneous vs heterogeneous)
   - Enum-like fields (small set of repeated string values)
   - Nested object structures

3. **Generate TypeScript interfaces**
   ```typescript
   export interface User {
     id: number;
     name: string;
     email: string;
     avatar_url?: string | null;
     role: "admin" | "editor" | "viewer";
     settings: UserSettings;
     created_at: string;
   }
   ```
   - PascalCase names, extract nested objects into separate interfaces
   - Use `?` for optional, `| null` for nullable
   - Prefer string literal unions when values are enumerable

4. **Generate Zod validation schemas**
   ```typescript
   import { z } from "zod";
   export const userSchema = z.object({
     id: z.number().int().positive(),
     name: z.string().min(1),
     email: z.string().email(),
     avatar_url: z.string().url().nullable().optional(),
     role: z.enum(["admin", "editor", "viewer"]),
     settings: userSettingsSchema,
     created_at: z.string().datetime(),
   });
   export type User = z.infer<typeof userSchema>;
   ```

5. **Validate data against existing schemas**
   Compare sample data against existing types:
   - Fields in data but missing from schema → **Schema needs update**
   - Type mismatches → **Type error**
   - Null values for non-nullable fields → **Validation failure**

6. **Detect schema drift across versions**
   Compare two data samples (v1 vs v2) field by field:
   - Added/removed fields, type changes, nullability changes
   - Output migration summary with recommended type changes

## Rules

- Always generate both TypeScript interfaces AND Zod schemas unless user asks for only one
- Use `z.infer<typeof schema>` to derive types — never define the same type twice
- Extract nested objects into named sub-schemas
- Prefer string literal unions over plain `string` for fewer than 10 known values
- When validating, distinguish "field missing" (drift) from "field null" (data issue)
- If multiple samples provided, merge to detect optional vs required fields
