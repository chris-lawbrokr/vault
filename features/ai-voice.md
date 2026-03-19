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




---

# Custom Voice Agent

Twilio
OpenAI’s API for conversational logic and natural responses.

## 1. Define the Use Case

- **Goal**: The agent will conduct client intake and appointment booking.
    
- **Task**: The agent will call clients after they fill out a lead form and ask six specific intake questions.
    
- **Scope**: The agent must stay on script, only asking those questions and recording answers.
    

## 2. Core Components

- **Natural Language Understanding & Generation**: Use OpenAI’s API for conversational logic and natural responses.
    
- **Telephony**: Use a service like Twilio (or a similar provider) to handle phone calls, connect with clients, and convert text into speech.
    

## 3. Workflow Design

- **Trigger**: When a lead form is filled, your system triggers the call.
    
- **Script**: Prepare a strict flow of six questions. The AI will ask each question and await an answer.
    
- **Non-Engagement Logic**: If the client goes off track, the agent politely redirects or repeats the question without engaging outside the script.
    

## 4. Steps to Build

1. **Conversation Design**:
    
    - Map out the exact six questions.
        
    - Define polite ways to redirect if the client deviates.
        
2. **OpenAI Integration**:
    
    - Use OpenAI’s API to generate the question prompts and parse responses.
        
    - Ensure each interaction is structured: question, wait for input, confirm, move on.
        
3. **Voice & Telephony**:
    
    - Use Twilio (or equivalent) for making the outbound call.
        
    - Feed OpenAI’s generated text into Twilio’s text-to-speech for a natural voice.
        
    - Collect and transcribe responses.
        
4. **Backend Integration**:
    
    - Store answers in your CRM or booking system.
        
    - Trigger follow-ups like appointment confirmation emails.
        

## 5. Testing & Optimization

- Test with internal staff first, ensuring natural pacing and clarity.
    
- Collect feedback and refine the agent’s prompts.
    
- Monitor real calls and fine-tune based on real interactions.
    

## 6. Deployment

- Once satisfied with tests, deploy live.
    
- Monitor performance regularly and update the flow as needed.
    

---

That should give you a clear roadmap! Let me know if you’d like to dive deeper into any piece.