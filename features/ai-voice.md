# AI Voice — Auto-Call Incoming Leads

## Concept
New tab on Lawbrokr.com called "AI Voice" — automatically call incoming leads using AI voice agents.

## Trigger
When a response email is sent to a lead → auto call or text the lead (opt-in) using info from lead capture.

## Parameters to Configure
- **Who to call** — which leads trigger auto-call
- **What to collect** — details to gather during the call
- **When to auto-book** — criteria for automatically booking a lead

## Architecture

```
Rails API → FastAPI → Calling Service → Calling Service (post-call)
```

1. **Rails API**: Trigger event to reach out to lead. Pull lead details and send to API.
2. **FastAPI**: Create chatbot specific to the client's lead details.
3. **Calling Service**: Execute the AI voice call.
4. **Post-Call**: Retrieve and organize details from the call. Send updated leads to the DB.

## Competitor Research
- [Lexidesk](https://lexidesk.ai/us) — legal AI assistant
- [Retell AI](https://www.retellai.com/) — AI voice calling platform

## Notes
- Auto email already exists in the system
- AI voice parameters would be a new tab in lawbrokr.com
