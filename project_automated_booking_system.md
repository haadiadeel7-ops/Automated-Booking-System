---
name: project-automated-booking-system
description: "Voice-powered appointment booking system (Vapi + n8n + Supabase + Google Calendar) — architecture, status, known bugs, and business model"
metadata: 
  node_type: memory
  type: project
  originSessionId: 4a74d5c1-b448-4bab-be0a-c9dbda42fdb1
---

# Automated Booking System

**Repo:** https://github.com/haadiadeel7-ops/Automated-Booking-System

Initial target: barber shops. Designed as a reusable template for any appointment-based business.

## Stack

- **Vapi** — AI voice layer; handles inbound calls, exposes a `book_appointment` tool
- **n8n** (hosted at `druits.app.n8n.cloud`) — no-code workflow automation for all booking logic
- **Supabase** — PostgreSQL database; enforces date/time uniqueness constraints
- **Google Calendar** — syncs confirmed bookings

## Architecture

```
Inbound call → Vapi → webhook → n8n Switch node
                                    ├── book appointment (validate → check conflicts → write Supabase → sync Calendar)
                                    ├── check availability (return open slots for date)
                                    └── reschedule (validate cancellation window → update record)
```

## Critical: Vapi Webhook Response Format

n8n MUST respond with this exact structure or Vapi silently ignores the response:

```json
{
  "results": [{ "toolCallId": "<body.message.toolCalls[0].id>", "result": "<plain text string>" }]
}
```

## Known Bug: n8n Supabase Node + Empty Results

The n8n Supabase "Get Many" node **halts workflow execution** when a query returns zero rows.

**Workaround:** Use an HTTP Request node calling Supabase's REST API directly — returns an empty array and lets execution continue.

## Current Status (as of 2026-05-26)

- Core booking flow: **operational**
- Rescheduling: built in n8n but **not yet wired into Vapi** (tools not added, system prompt not updated)
- End-to-end rescheduling test: **pending**

## Business Model

- Setup fee: $300–800
- Monthly recurring: $200–500
- Operational costs: ~$35–50/month

**Why:** Sell as a template to appointment-based businesses (barbers, salons, clinics, etc.)
**How to apply:** Keep architecture no-code/low-code (Vapi + n8n) to minimize operational overhead and maximize margin.
