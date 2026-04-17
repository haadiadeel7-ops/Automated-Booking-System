---
name: n8n Supabase Get Many empty result stops execution
description: Supabase Get Many node returns 0 items on no results — downstream nodes won't execute; use HTTP Request instead
type: feedback
originSessionId: 4129ce41-3f5a-4ed1-a375-6911fb6db996
---
When using the Supabase node (Get Many) in n8n and the query returns 0 rows, n8n outputs 0 items and stops execution for that branch. Downstream Code nodes never run.

**Why:** Discovered when building reschedule lookup by phone — needed to handle "no booking found" case but Code 8 would never fire if Supabase returned nothing.

**How to apply:** For any Supabase lookup where 0 results is a valid outcome that needs handling, use an **HTTP Request** node with Supabase REST API instead. It always returns a response (empty array `[]`), so downstream nodes always execute. Add headers: `apikey: <anon_key>` and `Authorization: Bearer <anon_key>`.
