# GrabNavi

**Prototype / Hackathon build** — A conversational, vision-enabled support & navigation prototype for delivery operations (Grab Delivery style). This repository contains the Jupyter notebook `GrabNavi_finall.ipynb` which wires together:

* Google Generative AI (Gemini) for vision + text reasoning
* LangChain helpers for tool/agent patterns
* A small Flask API (agent backend) for light orchestration
* Gradio driver & customer UIs for interactive flows
* AWS DynamoDB for lightweight event/log storage
* Route calculation & Folium-based map plotting


---

## Table of contents

1. Overview
2. Features
3. Architecture (high level)
4. Prerequisites
5. Quick start (run locally)
6. Configuration (API keys & env)
7. DynamoDB: table names & suggested schema
8. Notebook walkthrough (cell-by-cell mapping)
9. Usage / demo flows
10. Notes, limitations & cost warnings
11. Contributing

---

## 1. Overview

**GrabNavi** is a proof-of-concept prototype that demonstrates how a delivery support system can combine vision, LLM reasoning, routing and simple backend storage to: detect incidents from photos (e.g. damaged packaging), decide next steps, notify stakeholders, and help drivers navigate or escalate with support.

The implementation in `GrabNavi_finall.ipynb` is intentionally pragmatic: it prioritizes clarity and hackathon speed over production hardening.

## 2. Key features

* Image captioning and incident detection using Google Generative AI (Gemini).
* LLM-driven decision logic and incident classification.
* Tools for calculating routes and plotting them with Folium.
* Simple event/log capture using AWS DynamoDB.
* A small Flask endpoint that acts as the agent backend.
* Two Gradio front-ends: one for drivers and one for customers/support.
* Utilities for sending delay/notification emails (example templates included).

## 3. Architecture (high level)

```
[Driver Gradio UI] <--> [Flask agent & LangChain tools] <--> [VisionService (Gemini)]
                                           |                        |
                                           v                        v
                                     [DynamoDB]                [Routing/Map service]
                                           |
                                           v
                                   [Customer Gradio UI / Notifications]
```

* The notebook contains sections to run everything from a single environment. For a real deployment you'd split services (Flask + Gradio UIs + DB) across containers/servers.

## 4. Prerequisites

* Python 3.10+ recommended.
* An account & API access to Google Generative AI (Gemini). Obtain an API key and put it in your environment as `GOOGLE_API_KEY` (see Configuration).
* AWS account for DynamoDB access (or mock the table locally). Configure AWS credentials in your environment (AWS\_ACCESS\_KEY\_ID, AWS\_SECRET\_ACCESS\_KEY, AWS\_REGION) or via `aws configure`.

Suggested Python packages (the notebook installs some of these):

```
pip install -r requirements.txt
# Example packages used in the notebook (create requirements.txt with these):
# google-generativeai, langchain, langchain-google-genai, gradio, flask,
# boto3, pandas, folium, networkx, pillow, rich, matplotlib, requests
```


## 5. Quick start (run locally)

1. Clone the repository and open the notebook: `GrabNavi_finall.ipynb`.
2. Create & activate a Python virtual environment.
3. Install dependencies (see `requirements.txt` example above).
4. Configure environment variables (see Section 6).
5. Open and run the notebook cells **in order**. The notebook is organized into clear sections — run cells top-to-bottom.

There are three runtime modes inside the notebook:

* notebook-only: run cells and interact with inline outputs (maps, small demos)
* local Flask agent: start the Flask server cell if you want the HTTP agent API
* Gradio UIs: run the Gradio driver and customer cells to get interactive web UIs

## 6. Configuration (API keys & env vars)

Create a `.env` file or export the following environment variables before running the notebook.

```
# Google Generative AI (Gemini)
export GOOGLE_API_KEY="YOUR_GOOGLE_API_KEY"

# AWS (for DynamoDB)
export AWS_ACCESS_KEY_ID="YOUR_AWS_KEY"
export AWS_SECRET_ACCESS_KEY="YOUR_AWS_SECRET"
export AWS_REGION="YOUR_AWS_REGION"   # e.g. ap-southeast-1 or us-east-1

# Optional: tweak DynamoDB table name(s) (defaults used in notebook):
# DDB_TABLE_NAME=GrabUpdates
```

Inside the notebook the notebook sets `os.environ['GOOGLE_API_KEY']` as an example — replace that placeholder with your real key or set the variable externally.

## 7. DynamoDB: suggested table names & schema

The notebook references several DynamoDB table names. If you're using real AWS DynamoDB create these tables (or mock equivalents) before running the cells that write/read them.

* `GrabUpdates` (example): lightweight log store for order/incident traces.

  * Partition key: `order_id` (String)
  * Sort key (optional): `timestamp` (String / ISO datetime)
  * Attributes: `events` (list / JSON), `status`, `customer_email`, `driver_id`

* Other tables referenced in helper code (optional): `claims`, `notifications`, `customers`, `Orders`.

  * Keep `order_id` or `pk` as primary identifier depending on the notebook cell expectations.

If you don't want to use AWS, you can temporarily stub out DynamoDB calls or use a local JSON file to hold logs while testing.

## 8. Notebook walkthrough (cell-by-cell mapping)

The notebook is already separated into clear sections. Run the cells in-order. Here's a short map so you know what each section does:

1. **Install & Imports** — installs required packages (if in a fresh environment) and imports modules.
2. **API Key + Base Graph Setup** — sets `GOOGLE_API_KEY` and other initial variables.
3. **VisionService (Gemini Captioning + Decision)** — wraps Gemini image captioning + decide logic to classify image incidents.
4. **Tool Implementations** — LangChain-style tools that the agent uses (e.g., `get_nearby_drivers`, `send_driver_delay`).
5. **System Prompt, Text LLM, Incident Detector** — LLM prompt templates and decision flows.
6. **Route Calculation & Map Plotting** — helpers to compute routes and render Folium maps for visual debugging.
7. **DynamoDB** — helpers to push/read logs and traces from DynamoDB.
8. **Flask** — small Flask app that exposes an `/agent` endpoint to call the agent over HTTP (useful to connect Gradio UIs).
9. **Driver Gradio** — driver-facing UI: input status, photo upload, route plotting, trace fetch.
10. **Customer Gradio** — customer / support UI: fetch logs, view notifications, basic chat interface.

## 9. Usage / demo flows

### Driver flow (example)

1. Driver opens the Gradio driver UI.
2. Uploads a photo when an incident occurs (e.g., damaged package) and types a status.
3. VisionService captions and incident detection runs internally.
4. Agent decides whether to escalate, replace, or proceed.
5. The action and a timestamped trace are recorded to DynamoDB and optionally an email is sent to the customer/support.
6. Route plotting can help the driver re-route or view location overlays.

### Customer/support flow (example)

1. Open customer Gradio UI.
2. Enter an `Order ID` and fetch the full trace to see what has happened.
3. Trigger canned notifications or re-send emails (the samples include HTML templates).

## 10. Notes, limitations & cost warnings

* **Costs & quota**: Geminis & other generative APIs may incur charges. Test with care and watch your API usage.
* **Not production hardened**: This notebook uses in-notebook state and simple patterns appropriate for hackathons. Do NOT use it as-is for sensitive production workloads.
* **Credentials**: Never commit real API keys to source control. Use environment variables or a secrets manager.
* **Privacy**: If you upload real customer photos, ensure you have consent and that you handle PII appropriately.

## 11. Contributing

If you'd like to extend the project, here are suggested improvements:

* Extract the Flask agent into a standalone service and containerize it.
* Replace DynamoDB with a relational DB or a more structured event store.
* Add tests and CI for the core agent decision logic.
* Add authentication for the Gradio UIs and protect production APIs.

Create issues, open PRs, and add a short CONTRIBUTING.md to guide collaborators.

---
