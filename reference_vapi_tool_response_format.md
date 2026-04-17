---
name: Vapi tool call response format
description: Non-obvious format required for n8n webhook responses to Vapi tool calls
type: reference
originSessionId: 19c1cf55-99cf-4301-87e6-b8e946cd9bd7
---
Vapi server-side tools (webhook-based) require this exact response format or the assistant silently fails:

```json
{
  "results": [{ "toolCallId": "<id>", "result": "<string>" }]
}
```

- `toolCallId` must be extracted from `body.message.toolCalls[0].id` in the incoming webhook body
- `result` is a plain string — the assistant reads it and acts on it per the system prompt
- If `toolCallId` is missing or wrong, Vapi hangs or ignores the tool response entirely

**Pattern used in this project:**
- Success: `"result": "Booking confirmed."`
- Unavailable: `"result": "SLOT_UNAVAILABLE"` — system prompt maps this to a specific spoken response
