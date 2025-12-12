# Absher Smart Agent — MVP
This repository contains:
- HTML UI interface for the Absher Smart Agent demo
- n8n workflows (Router Agent, Residency Agent, Renew Iqama workflow)
- Assets and documentation for the MVP used in Absher Hackathon
  
## Structure
- absher-agent.html → main UI
- Absher-workflow-agent.json/ → contains all agent workflows in JSON

# **Absher AI Agent — Smart Government Assistant (MVP)**
A multi-assistant, AI-driven workflow system that redefines how citizens interact with government services.

## **🚀 Overview**
Absher AI Agent is an intelligent, multi-agent system built to simplify and automate government-style digital services.
The MVP understands natural language queries, identifies the user’s intent, collects missing information when needed, and triggers structured actions through n8n workflows — all while supporting human-in-the-loop review for sensitive operations.

This prototype was developed as part of the **Absher Hackathon**.

## **✨ Key Features**
### **1) Multi-Assistant Experience (Our Breakthrough Innovation)**
A unique, inclusive design offering **three AI assistants in one platform**, making government services accessible for **all** users:

#### **🟢 Text Assistant (Chat AI)**
* Understands free-text queries
* Extracts intent and required parameters
* Requests missing info intelligently
* Triggers the correct workflow (e.g., Renew Iqama)

#### **🎤 Voice Assistant**
* Converts **speech → text** seamlessly
* Ideal for elderly users or anyone who prefers speaking
* Fully integrated with the same multi-agent workflow

#### **🤟 Video Assistant (Sign-Language Interpreter)**
* Converts **sign language in video → text**
* Enables the Deaf & Hard-of-Hearing community to use services independently
* A major accessibility innovation rarely seen in government platforms

Together, these assistants transform Absher from a “self-service portal” into an **auto-service intelligent experience**.

## **🧠 Multi-Agent Architecture**
Our system uses a layered AI agent model:

### **1) Router Agent**
* Interprets user input
* Determines the service domain (Residency, Traffic, Civil Affairs…)
* Routes the request to the correct specialist agent
* Ensures safety and avoids domain mixing

### **2) Domain-Specific Agents**
Example: **Residency Agent** for iqama services
* Validates required parameters
* Identifies missing fields
* Outputs structured JSON for workflow execution
* Operates within strict guardrails

### **3) Guardrails System**
* Ensures agent outputs follow safety rules
* Validates JSON structure
* Prevents unauthorized data access or incorrect actions

## **⚙️ Workflow Automation (n8n Orchestration)**

The MVP integrates with **n8n** to execute government-style workflows:
* Structured JSON from AI → parsed inside n8n
* Missing data → returned to user
* All required data present → AI triggers workflow node
* Human-in-the-loop review optional
* Final action executed (e.g., simulate Renew Iqama)

## **📄 Documentation Included**
* System architecture diagrams
* Multi-assistant design overview
* Multi-agent logic (Router + Domain Agents)
* Guardrails validation design
* Example input → output flows
* n8n workflow explanation
* MVP demo steps

## **Guardrails validation design**
```
const item = { ...$json };

const allowedActions = ['renew_iqama', 'renew_id', 'book_license'];
const allowedDomains = ['residency', 'identity', 'traffic'];

item.action = item.action || 'none';
item.domain = item.domain || 'unknown';
item.risk_level = item.risk_level || 'medium';

item.user_id = item.parameters?.user_id ?? item.user_id ?? 1;

item.sessionId = item.session_id || `user-${item.user_id}`;
item.requestId = item.requestId || `${item.sessionId}-${Date.now()}`;

if (!allowedActions.includes(item.action) || !allowedDomains.includes(item.domain)) {
  item.guardrailStatus = 'blocked';
  item.guardrailReason = 'خدمة أو مجال غير مدعومين في هذا الإصدار من المساعد.';
  item.action = 'none';
} else {
  item.guardrailStatus = 'allowed';
  item.guardrailReason = null;
}

const missing = item.parameters?.missing_fields || [];
if (missing.length > 0) {
  item.guardrailStatus = 'blocked';
  item.guardrailReason = 'هناك حقول ناقصة يجب استكمالها قبل تنفيذ الخدمة.';
  item.action = 'none';
}

item.needsUserApproval = item.risk_level === 'high';

return {
  json: item,
};
```

#*** Multi-agent logic (Router + Domain Agents) Prompt in workflow**
```
You are the Residency Agent. Your scope includes residency services only. 
Your job is to validate and authorize the execution of the "renew_iqama" service.

You ALWAYS return pure JSON ONLY without any explanation or text outside the JSON object.

INPUT FORMAT (example):
{
  "action": "renew_iqama",
  "domain": "residency",
  "parameters": {
    "nationalId": "...",
    "iqamaNumber": "...",
    "missing_fields": []
  },
  "sessionId": "...",
  "requestId": "..."
}

YOUR DECISION RULES:

1) Only proceed if:
   - action = "renew_iqama"
   - domain = "residency"
   Otherwise, return:
   {
     "can_execute": false,
     "service": "renew_iqama",
     "reason": "هذه الخدمة ليست ضمن نطاق وكيل الإقامة.",
     "sessionId": "<sessionId>",
     "requestId": "<requestId>"
   }

2) Required fields are ONLY:
   - nationalId
   - iqamaNumber

   Treat missing_fields = [] as valid. Do NOT consider it missing data.

   A field is missing ONLY if it is:
   - not present, OR
   - empty string ""

   If data is incomplete, return:
   {
     "can_execute": false,
     "service": "renew_iqama",
     "reason": "البيانات غير مكتملة، نحتاج رقم الهوية ورقم الإقامة قبل تنفيذ التجديد.",
     "sessionId": "<sessionId>",
     "requestId": "<requestId>"
   }

3) If data is complete and valid, return:
   {
     "can_execute": true,
     "service": "renew_iqama",
     "payload": {
       "nationalId": "<from input>",
       "iqamaNumber": "<from input>"
     },
     "sessionId": "<sessionId>",
     "requestId": "<requestId>"
   }

RULES:
- Output MUST be valid JSON only.
- Never include commentary, explanation, markdown, or text outside the JSON.
- Follow the structure exactly as defined above.
```


 

## **🎯 Current Scope (MVP)**
* 30% of the full user journey implemented
* Renew Iqama scenario fully operational
* Natural-language understanding
* Parameter extraction + missing-field detection
* Agent-validated JSON output
* n8n automated execution
* UI prototype for user testing


## **🌱 Future Enhancements**
* Expand domain agents (Traffic, Civil Affairs, Passports)
* Real sign-language recognition using CV models
* Integration with secure government APIs
* Personalization based on citizen profile
* Fully voice-based conversational flow


## **🏆 Hackathon Vision**
A government platform that doesn’t wait for users to search for services…
but **understands them, guides them, and serves them instantly** — no complexity, no barriers.
