# Memory Index

- [Automated Booking System](project_automated_booking.md) — Vapi+n8n+Supabase+GCal; core booking working; reschedule n8n built, Vapi tools + prompt update pending
- [Vapi tool response format](reference_vapi_tool_response_format.md) — Must return `{ results: [{ toolCallId, result }] }` or Vapi silently fails
- [Fragrance Website MCP Server](project_fragrance_website.md) — **Discontinued**; CLAUDE.md has full research; kept as learning reference only

- [Always minimize steps](feedback_speed.md) — fewest files/steps/tokens always, no extras
- [Plan mode rules](feedback_planmode.md) — always list time + token estimates in plan mode, minimize both, think before acting
- [Think before acting](feedback_think_before_acting.md)
- [Admit uncertainty](feedback_admit_uncertainty.md) — if unsure, say so; don't guess — check ESM/CJS/Node compatibility before installing packages; no trial-and-error cycles
- [Token conservation](feedback_token_conservation.md) — NEVER use agents without permission; treat tokens as scarce; answer from knowledge first
- [Scraping lessons](feedback_scraping_lessons.md) — always test on 50 entries first, check for duplicate URLs, blacklist banner images before scaling
- [n8n Supabase empty result](feedback_n8n_supabase_empty.md) — Supabase Get Many with 0 results stops execution; use HTTP Request node instead for lookups where no-result is valid
