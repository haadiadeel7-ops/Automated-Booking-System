Summarize this entire conversation and save it to the memory system at C:\Users\Adeel#\.claude\projects\C--Users-Adeel-\memory\

Steps:
1. Read MEMORY.md to see what already exists
2. Identify what's new in this conversation — new projects, decisions, technical findings, feedback given, preferences expressed
3. For each meaningful piece of new information, either:
   - Create a new memory file (user_*.md, project_*.md, feedback_*.md, reference_*.md) with proper frontmatter
   - Or update an existing file if the info extends something already saved
4. Update MEMORY.md index to include any new files (one line per entry, under 150 chars)

Memory file format:
---
name: short-kebab-case-slug
description: one-line summary
metadata:
  type: user | feedback | project | reference
---

[content with Why: and How to apply: lines for feedback/project types]

Do not save: code patterns, git history, temp task details, or anything already in CLAUDE.md. Only save what would be useful in a future session to understand who I am, how I work, and what's going on in my projects.
