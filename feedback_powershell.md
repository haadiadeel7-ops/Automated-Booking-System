---
name: User uses PowerShell
description: User's terminal is PowerShell — && is not valid, commands must be run separately
type: feedback
originSessionId: 607557f0-f7e3-435e-a511-a686579ff52c
---
User's terminal is PowerShell, not CMD or bash.

**Why:** `&&` is not a valid statement separator in PowerShell — caused a failed git push when two commands were chained.

**How to apply:** When giving terminal commands, either give them one at a time, or use `;` instead of `&&`. Never chain with `&&` in instructions to the user.
