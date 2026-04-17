---
name: Token conservation rules
description: Never use agents without permission. Treat tokens as scarce — think before every tool call.
type: feedback
---

NEVER launch an Agent/subagent without explicitly asking the user first and getting permission.

Treat every token as if it's your last. Before any tool call, ask: "can I answer this from existing knowledge?"

**Why:** User was extremely frustrated by repeated unnecessary agent launches and tool calls for questions answerable from existing knowledge.

**How to apply:** Default to answering from memory/knowledge. Only use tools when truly needed. Never use agents speculatively. One tool call at a time, minimum necessary.
