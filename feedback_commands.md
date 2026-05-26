---
name: feedback-commands
description: Custom slash commands the user has set up and uses regularly in Claude Code
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 4a74d5c1-b448-4bab-be0a-c9dbda42fdb1
---

# Custom Claude Code Commands

These live in `C:\Users\Adeel#\.claude\commands\`

| Command | What it does |
|---|---|
| `/recall` | Reads all memory files and gives a structured summary — use at session start to restore full context |
| `/mem` | Summarizes the current conversation and saves new info to the memory system for future sessions |
| `/details` | Before any task: shows steps, token estimate, time estimate, and files affected — waits for approval before proceeding |

**Why:** User wants persistent context across sessions and cost visibility before work starts.
**How to apply:** Recognize when user types these commands and execute the behavior defined in each command file. [[feedback-behavior]]
