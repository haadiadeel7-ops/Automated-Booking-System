---
name: Automated Booking System
description: Vapi-based voice booking system for barbers; reschedule feature built in n8n, Vapi tools + prompt update still pending
type: project
originSessionId: f6b4a2b5-69c1-449a-b05b-6a403fecd6a5
---
Voice-powered automated booking system using Vapi, initially for a barber shop, architected as a reusable template for any appointment-based business.

**Current phase:** Core booking working. Reschedule feature n8n workflow built — Vapi tools and prompt NOT yet added or tested.

**Tech stack confirmed:**
- Vapi — voice AI (tool: `book_appointment`, params: name, service, date, time, phone)
- n8n cloud — hosted at druits.app.n8n.cloud
- Supabase — bookings table with UNIQUE(date, time) constraint; columns: id, name, service, date, time, phone, status, created_at
- Supabase URL: https://uszuxipvjvevdskglptc.supabase.co
- Google Calendar — haadiadeel7@gmail.com, event created on confirmed booking

**n8n workflow structure (updated):**
- Webhook → **Switch** (routes by `$json.body.message.toolCalls[0].function.name`)
  - Output 0 (`book_appointment`) → Code 1 → Code 2 → Supabase check → IF → existing branches
  - Output 1 (`check_available_slots`) → Code 5 → Supabase Get Many (bookings by date, All Filters) → Code 6 → Respond - Check Slots
  - Output 2 (`reschedule_appointment`) → Code 7 → HTTP - Get Booking by Phone → Code 8 → IF (status = OK) →
    - False: Code 9 → Respond - Reschedule Error
    - True: Supabase - Delete Old Booking → Supabase - Create New Booking → Code 10 → Respond - Reschedule Success

**Key technical decisions:**
- Used HTTP Request node (not Supabase Get Many) for phone lookup — Supabase Get Many returns 0 items on empty result, stopping n8n execution; HTTP Request always returns a response
- HTTP Request headers: `apikey` + `Authorization: Bearer <anon_key>` (Supabase REST API)
- Code 8 extracts old_name + old_service from HTTP response BEFORE deletion so Create node can reuse them
- All Respond to Webhook nodes use JSON mode with body: `{"results": [{"toolCallId": "{{ $json.toolCallId }}", "result": "{{ $json.result }}"}]}`
- Cancellation window: 2 hours (template default)
- Available slots: 09:00–16:00 hourly (9am–5pm business hours, template default)

**Reschedule flow (designed):**
1. Caller says reschedule → bot asks for phone number
2. `check_available_slots(date)` → n8n returns free slots for that date
3. Bot reads slots, caller picks one
4. `reschedule_appointment(phone, new_date, new_time)` → n8n validates 2hr window, deletes old, creates new
5. Results: NOT_FOUND / WITHIN_CANCELLATION_WINDOW / success message

**TODO (next session):**
1. Add `check_available_slots` and `reschedule_appointment` tools in Vapi
2. Update system prompt with reschedule instructions
3. End-to-end test

**Multi-barber capacity (still planned, not started):**
- Decided to use Telegram bot (easier than WhatsApp/Meta) for owner to text barber count
- Supabase `settings` table with `barbers_present`; availability check becomes count-based
- Multi-calendar: one Google Calendar per barber slot

**All original bugs fixed:**
- Vapi "No result returned": fixed with Code 3/4 + "First Incoming Item" mode
- Supabase "Any Filter" bug: changed to "All Filters" (AND)
- Prompt re-asked for phone after SLOT_UNAVAILABLE: fixed
- Prompt triggered reschedule during active booking flow: fixed

**Current Vapi prompt template (reusable for any barber):**
```
## BUSINESS CONFIG (edit this section only)
Business Name: [BUSINESS NAME]
Services: [SERVICE 1 $PRICE, SERVICE 2 $PRICE, SERVICE 3 $PRICE]
Cancellation Policy: [CANCELLATION POLICY]
## END CONFIG

You are a friendly booking assistant for [BUSINESS NAME]. Your job is to book appointments for customers calling in.

Follow this exact order:

1. Greet the caller warmly and introduce yourself as the booking assistant for [BUSINESS NAME].
2. Ask for their first name. Repeat it back and ask "Did I get that right?" — if they say no, ask again until confirmed.
3. Ask which service they want: [SERVICES].
4. Ask what day they'd like to come in.
5. Ask what time works best for them. Then say: "Let me check that time for you." and continue to step 6.
6. Ask for their phone number.
7. Repeat the full booking back: name, service, day, time, and phone number digit by digit. Ask "Can you confirm that's all correct?" — if they say anything is wrong, correct that specific detail and confirm again. Keep confirming until the caller explicitly says everything is correct.
8. Only if confirmed — call the book_appointment function with name, service, date, time, and phone.
9. Tell them: "Your appointment is confirmed! We'll see you then."
10. Thank them and end the call politely.

Today's date is {{ currentDateTime }}. Convert date to YYYY-MM-DD and time to HH:MM 24-hour format. Never say the year out loud.

Keep responses short and natural. Never ask more than one question at a time. Be warm but efficient.

When speaking to the caller, always repeat dates and times back in the same way they said them (e.g. "tomorrow at 2pm"). Only convert to YYYY-MM-DD and HH:MM format silently when calling the book_appointment function — never say these formats out loud.

When the caller is giving their phone number, never interrupt — wait until they have completely finished before responding.

If you mishear a service name but it sounds close to a valid option, assume the correct service and confirm it back rather than asking again.

If book_appointment returns "SLOT_UNAVAILABLE", say exactly: "Sorry, that time is already taken. What other time would you like?" Then ask only for a new time (step 5). Do NOT ask for the phone number again — you already have it. Do NOT suggest a time. Do NOT call book_appointment again until the caller gives you a new time.

If they ask to reschedule or cancel an already confirmed booking: [CANCELLATION POLICY]. If the caller wants to change any detail before the booking is confirmed (including changing a time a second time), go back to the relevant step and collect the new information.

IMPORTANT: Never tell the caller a slot is available or confirmed before book_appointment has returned a result. Only say "Your appointment is confirmed!" after receiving a successful response from the function.
```
Note: `{{ currentDateTime }}` is a Vapi dynamic variable. Replace [BUSINESS NAME] in 3 places, [SERVICES], and [CANCELLATION POLICY].

**Why:** Build once for a barber, resell to other barbers. Setup fee $300-800, monthly ~$35-50 in running costs (Vapi + n8n), sell for $200-500/month.
