# GrabNavi Prompt Engineering

This document explains the *prompt design strategies* used in GrabNavi.  
Prompts are crafted to ensure structured outputs, reliable classification of issues, and empathetic handling of customer interactions.

---

## 1. Role-Based Instructions
The assistant is always framed as *GrabNavi*, an AI logistics agent.  
It is guided with clear system-level instructions to:
- Identify the nature of reported issues (traffic, driver, merchant, package damage, etc.).
- Classify them into structured categories.
- Suggest appropriate actions aligned with operational workflows.

---

## 2. Structured JSON Enforcement
Every prompt enforces responses in a *strict JSON schema* with the following fields:
- case_type – the identified problem category.  
- confidence – a probability score for classification reliability.  
- actions – recommended steps for resolution.  

This ensures *machine-readable consistency* and seamless integration with orchestration modules.

---

## 3. Handling Compound Cases
Prompts are designed to handle *multi-issue inputs* in a single customer or driver report.  
For example, a case where a merchant delay and a driver waiting are reported together will be *split into separate cases* in the output JSON.  
This prevents conflicting resolutions and reduces unnecessary communication loops.

---

## 4. Confidence-Based Escalation
The prompt template explicitly instructs the agent to:
- *Resolve automatically* if classification confidence ≥ 0.8.  
- *Escalate to a human agent* if confidence < 0.8.  

This introduces a *human-in-the-loop safeguard* for ambiguous cases.

---

## 5. Customer Complaint Handling
Prompts emphasize *empathy* and customer-first communication.  
When handling complaints (e.g., late delivery, driver not at pickup point), the assistant:
- Acknowledges and apologizes politely.  
- Provides clear next steps (ETA updates, driver contact, reassignment).  
- Ensures customers feel supported, even during disruptions.  

---

## 6. Role of LLM and Gemini
GrabNavi leverages *Google Gemini (via LangChain integration)* as the underlying *Large Language Model (LLM)*.  
- Gemini provides *natural language understanding* for complex, unstructured customer and driver reports.  
- Prompt templates guide Gemini to produce *deterministic, structured JSON outputs* instead of free-form text.  
- The LLM is tuned to follow *schema constraints, **multi-case reasoning, and maintain **politeness in communication*.  

This combination of *LLM flexibility* with *strict prompt controls* ensures that outputs are both *intelligent and operationally reliable*.

---

## 7. Prompt Engineering Highlights
- *Role-based instructions* ensure GrabNavi behaves consistently as a logistics assistant.  
- *Strict schema adherence* enables automation of downstream tasks.  
- *Compound case detection* prevents conflicting actions.  
- *Confidence fallback* ensures safety with human escalation.  
- *Empathy layer* maintains customer trust and satisfaction.  
- *LLM + Gemini integration* powers contextual reasoning while remaining aligned with business rules.  

---

## 8. Final Highlights
- Prompts are *modular* and adaptable for drivers, merchants, and customers.  
- Designed to balance *automation with human oversight*.  
- Built with a focus on *clarity, reliability, and empathy*.  
- Ensure that every response is both *actionable* and *trust-building*.