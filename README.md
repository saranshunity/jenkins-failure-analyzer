# Jenkins Failure Analyzer — GenAI Agent

A GenAI-powered CI failure analysis prototype that analyzes build logs, identifies potential root causes, validates structured diagnoses, and presents results through a dashboard.

The project uses **FastAPI, LangGraph, Pydantic, SQLite, and Ollama Cloud** to demonstrate how an LLM-powered diagnostic workflow can be integrated into a developer tool.

## Features

- **Failure ingestion API:** Accepts build metadata and console logs.
- **LLM-based diagnosis:** Analyzes logs to identify likely root causes and recommend fixes.
- **LangGraph workflow:** Orchestrates analysis, validation, and success/failure routing.
- **Structured output validation:** Uses Pydantic to validate severity, root cause, evidence, recommended fix, and confidence.
- **Persistent storage:** Stores build events and analysis results in SQLite.
- **Web dashboard:** Displays recorded failures, analysis status, and diagnostic details.
- **Error handling:** Tracks analysis failures and supports controlled processing retries.

## Architecture

```text
CI Build Failure
       |
       v
FastAPI Webhook
       |
       v
SQLite: Record Event
       |
       v
Background Processing
       |
       v
LangGraph Workflow
       |
       v
Ollama Cloud LLM
       |
       v
Pydantic Validation
       |
       v
Persist Diagnosis
       |
       v
Web Dashboard
```

## Technology Stack

| Technology | Purpose |
|---|---|
| Python | Application logic |
| FastAPI | API endpoints and webhook handling |
| LangGraph | Workflow orchestration |
| Ollama Cloud | Hosted LLM inference |
| OpenAI-compatible Python client | Model API requests |
| Pydantic | Structured response validation |
| SQLite | Persistent event and analysis storage |
| HTML, CSS, JavaScript | Dashboard UI |

## How the Analysis Workflow Works

1. A build failure event is received with build metadata and console logs.
2. The application records the event and updates its processing status.
3. A background worker invokes the LangGraph analysis workflow.
4. The LLM analyzes the logs and returns a proposed diagnosis.
5. Pydantic validates the response against the expected schema.
6. The workflow routes the event to a completed or failed state.
7. The result is persisted and displayed in the dashboard.

The expected analysis fields are:

- `severity`: LOW, MEDIUM, HIGH, CRITICAL, or UNKNOWN
- `root_cause`: The likely reason for the failure
- `evidence`: Relevant log evidence
- `recommended_fix`: A proposed remediation
- `confidence`: LOW, MEDIUM, or HIGH

LLM-generated diagnoses are suggestions and should be verified before applying changes.

## Prerequisites

- Python 3.10 or a compatible version supported by the installed dependencies
- An Ollama Cloud API key
- Internet access for model inference

## Local Setup

### 1. Clone the repository

```bash
git clone https://github.com/saranshunity/jenkins-failure-analyzer.git
cd jenkins-failure-analyzer
```

### 2. Create a virtual environment

Windows PowerShell:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure environment variables

Create a local `.env` file in the project root:

```env
OLLAMA_API_KEY=your_ollama_api_key
OLLAMA_MODEL=gemma4:31b-cloud
```

Replace the model value with a model available to your Ollama Cloud account if necessary.

**Security:** Never commit `.env` or expose API keys in source code, logs, screenshots, or public repositories.

### 5. Start the application

```bash
uvicorn main:app --reload
```

Open the FastAPI interactive documentation at:

http://127.0.0.1:8000/docs

Open the dashboard at the local URL served by the application's static-file configuration.

## API and Dashboard

The application provides endpoints for retrieving recorded analyses and individual analysis details, including:

- `GET /analyses` — list recorded analyses.
- `GET /analyses/{event_key}` — retrieve a specific analysis.

Use the interactive API documentation to inspect the available webhook endpoint and its expected request schema.

## Current Project Status

**Implemented:** FastAPI application, LangGraph-based analysis, Ollama Cloud integration, structured response validation, SQLite persistence, and a dashboard for reviewing analyses.

**Potential next steps:**

- Integrate with a live GitHub Actions or Jenkins pipeline.
- Add automated tests for model errors, invalid output, and retry handling.
- Improve retrieval of relevant log excerpts for large builds.
- Add authenticated webhook requests and stronger operational monitoring.
- Publish validated diagnoses as GitHub pull request comments.

This is an independent prototype intended to demonstrate GenAI application engineering concepts. Live CI integration and production deployment should be considered separate enhancements until implemented and tested.

## Repository

https://github.com/saranshunity/jenkins-failure-analyzer
