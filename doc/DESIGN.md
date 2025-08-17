# GrabNavi Agent Design

## Overview
GrabNavi is an AI-driven orchestration system for *ride-hailing and delivery*.  
It uses *LangChain* with *Google Generative AI (Gemini)* plus supporting libraries (e.g., NetworkX, Pandas) to interpret reports, classify issues, and decide resolutions.

## Core Components
- *Input Processing*  
  Ingests natural language from *drivers, **customers, and **merchants*; normalizes and enriches with basic metadata (IDs, timestamps).

- *LLM via LangChain (Gemini)*  
  ChatGoogleGenerativeAI routes prompts to Gemini.  
  ChatPromptTemplate + MessagesPlaceholder enforce role, context, and *strict JSON* outputs.

- *Tool Integration*  
  Custom tools via BaseTool (e.g., reroute simulation, case logging, notification dispatch). Tools return structured results for the agent to compose.

- *Agent Orchestration*  
  create_tool_calling_agent + AgentExecutor enable multi-step reasoning with tool calls and stateful context.

- *Compound Case Detection*  
  Detects and *splits multi-issue reports* (e.g., traffic jam + customer no-show) into separate cases to avoid conflicting actions.

- *Routing Logic*  
  Mix of rules and LLM reasoning to choose next actions:
  - Reroute driver (NetworkX graph simulation)
  - Notify customer/merchant with clear status & ETA
  - Reassign or escalate when thresholds are exceeded

- *Customer Complaint Handling (First-Class)*  
  Prompts are tuned for *empathy + clarity*:
  - Acknowledge & apologize
  - Provide real-time status (driver location/delay, merchant prep time)
  - Offer next steps (ETA, driver contact, reassignment, escalation)

- *Human-in-the-Loop Escalation*  
  If confidence < threshold or actions conflict, *escalate* to an operator with a concise case summary.

## Flow
mermaid
graph TD
    A[User Report: Driver/Customer/Merchant] --> B[Preprocessing]
    B --> C[Gemini via LangChain]
    C -->|Single Issue| D[Action Routing]
    C -->|Compound Issue| E[Compound Resolver]
    E --> D
    D -->|Notify & Execute| F[Customer/Driver/Merchant Updates]
    D -->|Complaint Path| H[Empathy Response + ETA/Next Steps]
    D -->|Low Confidence| G[Human Escalation]

# Goals
- Deliver one coherent update to the customer/driver
- Enforce strict JSON outputs for reliability
- Remain extensible for new case types and tools