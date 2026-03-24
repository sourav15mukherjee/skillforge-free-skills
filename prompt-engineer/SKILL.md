---
name: prompt-engineer
description: >-
  Help users craft optimized AI prompts. Use when the user asks to write a
  prompt, improve a prompt, create a system message, build few-shot examples,
  or optimize LLM instructions for any AI model (OpenAI, Anthropic, open-source).
version: "1.0.0"
tools:
  - Read
  - Write
---

## Prompt Engineer

Transform vague instructions into production-grade AI prompts.

## Workflow

1. **Understand the intent**
   Ask the user (or infer from context):
   - What is the AI supposed to do? (task)
   - Who will use it? (audience)
   - What model will run it? (OpenAI, Claude, Llama, etc.)
   - What format should the output be? (JSON, markdown, free text)
   - Any constraints? (length, tone, safety)

2. **Define the role and context**
   Write a system message that establishes:
   - Who the AI is (role)
   - What it knows (context/expertise)
   - What it should NOT do (constraints)
   - How it should respond (tone, format)

   ```
   You are a senior code reviewer at a fintech company. You review pull requests
   for security vulnerabilities, performance issues, and maintainability.
   You are direct and specific — cite exact line numbers. You never approve
   code with SQL injection or XSS vulnerabilities.
   ```

3. **Create the user message template**
   Design a structured input format:
   ```
   Review this pull request:

   **Title:** {{pr_title}}
   **Description:** {{pr_description}}
   **Diff:**
   ```
   {{diff}}
   ```

   Focus on: {{focus_areas}}
   ```

4. **Add few-shot examples**
   Create 2-3 input/output examples that demonstrate:
   - The expected quality and format
   - Edge cases the model should handle
   - The boundary between "in scope" and "out of scope"

5. **Define output structure**
   Specify the exact format:
   ```json
   {
     "verdict": "approve | request_changes | comment",
     "summary": "One-sentence overall assessment",
     "findings": [
       {
         "severity": "critical | warning | suggestion",
         "file": "path/to/file.ts",
         "line": 42,
         "issue": "Description of the issue",
         "fix": "Suggested fix"
       }
     ]
   }
   ```

6. **Add guardrails**
   - Token budget guidance ("keep responses under 500 tokens")
   - Hallucination prevention ("only reference code in the provided diff")
   - Safety boundaries ("never generate executable code in reviews")
   - Fallback behavior ("if the diff is too large, summarize by file")

7. **Optimize for the target model**
   - **Claude:** Use XML tags for structure, be explicit about constraints
   - **GPT-4:** Use markdown headers, JSON mode if available
   - **Open-source:** Keep prompts simpler, use more examples
   - **All:** Put critical instructions at the start AND end (primacy + recency)

8. **Output the final prompt**
   Present the complete prompt package:
   - System message
   - User message template
   - Few-shot examples
   - Output format specification
   - Usage notes and tips

## Rules

- Always ask about the target model — prompt strategies differ
- Include at least 2 few-shot examples for complex tasks
- Put constraints BEFORE instructions (models follow what they read last)
- Use delimiters (XML tags, markdown headers, triple backticks) to separate sections
- Test prompts mentally with edge cases before delivering
- Never include real API keys or PII in example prompts
- If the user's task is ambiguous, clarify before writing — a good prompt starts with a clear intent
