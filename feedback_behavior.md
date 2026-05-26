---
name: feedback-behavior
description: "User's behavioral rules for how Claude should work — speed, honesty, token use, planning, and platform"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 4a74d5c1-b448-4bab-be0a-c9dbda42fdb1
---

# Behavioral Rules

**PowerShell only** — Never chain commands with `&&`. Use `;` or give commands individually.
**Why:** User is on Windows 11, PowerShell-only environment.
**How to apply:** Every shell command must be PowerShell-compatible.

---

**Shortest path always** — No extras, no polish, no unnecessary steps.
**Why:** User values speed and simplicity over thoroughness.
**How to apply:** Do the minimum required. Don't refactor, clean up, or add features beyond what's asked.

---

**Explain before acting** — State what you're about to do before doing it.
**Why:** User wants to stay in control and not be surprised.
**How to apply:** One sentence before the first tool call describing the action.

---

**Plan mode** — Always show time + token estimates. Never run anything without approval.
**Why:** User wants full visibility before committing to work.
**How to apply:** Use /details or state estimates upfront for any non-trivial task.

---

**Admit uncertainty** — Say "I don't know" rather than guessing.
**Why:** User prefers honesty over confident but wrong answers.
**How to apply:** If unsure, say so explicitly before attempting.

---

**Token conservation** — Never launch subagents without permission. Answer from knowledge first.
**Why:** User is cost-conscious and dislikes unnecessary token spend.
**How to apply:** Try to answer from existing context before searching or spawning agents.

---

**Scraping rules** — Always test on 50 entries before scaling. Check for duplicate URLs. Blacklist banner images.
**Why:** Past incident where a full-scale scrape ran with bad logic, wasting time and requests.
**How to apply:** Any scraping task must start with a 50-entry test run and validation step.
