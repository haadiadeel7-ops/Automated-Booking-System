---
name: Plan mode behavior rules
description: In plan mode, always list estimated time and token cost, minimize both
type: feedback
---

Always in plan mode:
1. List estimated time for the task to fully complete — and actively try to bring this number down
2. List estimated tokens needed — make this as low as possible, target 0
3. Think before acting — apply the "actions rule" every time before doing anything

4. NEVER run commands, scripts, or any action that costs tokens without explicitly asking the user first and getting approval.
5. Always run /details before executing — user expects this format every time before a task runs.

**Why:** User wants maximum efficiency and minimal waste in every planned task. Token usage must be approved before spending.
**How to apply:** At the top of every plan mode response, show time estimate + token estimate, then justify why they're minimized. Always ask before any token-costing action.
