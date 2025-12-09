# MCP Multi-Agent Customer Service System

A streamlined multi-agent system built with **Model Context Protocol (MCP)** and **A2A (Agent-to-Agent)** communication.  
The system routes customer queries through a dedicated **Router Agent**, which coordinates three specialists — **Data**, **Support**, and **Payment** — each running independently and communicating via JSON‑RPC.

---

## 📐 System Architecture

```
                    ┌─────────────────────────────┐
                    │     MCP Server (8000)       │
                    │ SQLite DB + Tool Registry   │
                    └─────────────────────────────┘
                                ▲
                                │ HTTP/REST
          ┌─────────────────────┼─────────────────────┐
          │                     │                     │
          ▼                     ▼                     ▼
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│   Data Agent    │ │  Support Agent  │ │  Payment Agent  │
│     (8011)      │ │     (8012)      │ │     (8013)      │
└─────────────────┘ └─────────────────┘ └─────────────────┘
          ▲                     ▲                     ▲
          └─────────────────────┼─────────────────────┘
                                │ A2A RPC
                                ▼
                    ┌─────────────────────┐
                    │    Router Agent     │
                    │       (8010)        │
                    └─────────────────────┘

```

---

## 📁 Project Layout
```
.
├── README.md
├── requirements.txt
├── database_setup.py
├── demo.py
├── mcp_server/
├── agents/
│   ├── router/
│   ├── data/
│   ├── support/
│   └── payments/
├── common/
├── sdk/
└── shared/
```

---

## ▶️ Setup & Run

### 1. Create & Activate Virtual Environment
```bash
python3 -m venv venv
source venv/bin/activate      # macOS/Linux
venv\Scripts\activate       # Windows
```

### 2. Install Dependencies
```bash
pip install -r requirements.txt
```

### 3. Initialize Database
```bash
python database_setup.py
```

### 4. Start All Services (One Terminal per Command)

```
python -m mcp_server.app          # http://localhost:8000
python -m agents.data.main        # http://localhost:8011
python -m agents.support.main     # http://localhost:8012
python -m agents.payments.main    # http://localhost:8013
python -m agents.router.main      # http://localhost:8010
```

---

## 🧪 How to Test
Run the demo script:

```bash
python demo.py
```

Or open the notebook:

```bash
jupyter notebook Assignment5_notebook.ipynb
```

Tests include:
- Basic customer lookup  
- Multi-agent query handling  
- Escalations (refunds / urgency)  
- Combined multi-intent instructions  

---

## 🔧 Configuration

Create a `.env` file:
```
MCP_SERVER_URL=http://localhost:8000
DATA_AGENT_RPC=http://localhost:8011/rpc
SUPPORT_AGENT_RPC=http://localhost:8012/rpc
BILLING_AGENT_RPC=http://localhost:8013/rpc
ROUTER_RPC=http://localhost:8010/rpc
```

Load environment:
```bash
export $(cat .env | xargs)
```

---

## 📡 API Summary

### MCP Server
| Endpoint          | Purpose                           |
|------------------|-----------------------------------|
| `/tools/list`    | List available tools               |
| `/tools/call`    | Execute a tool                     |
| `/events/stream` | Real‑time event stream (SSE)       |
| `/health`        | Service status                     |

### Agent RPC
All agents support:
- `message/send`
- `message/send_stream`
- `task/get`
- `task/cancel`

Metadata available via:
```
/.well-known/agent-card.json
```

---

## 🧠 Example Usage

### 1. Query Information
```python
"Get customer information for ID 5"
```

### 2. Multi‑intent
```
"Update my email and show my ticket history"
```

### 3. Escalation
```
"I was charged twice, refund immediately!"
```

Router decides routing and aggregates the final answer.

---

## 🗄 Database Overview

Tables:
- `customers`  
- `tickets`  
- `interactions`  

Each table includes timestamps and relational links to support history lookup and ticket flow.

---

## 🛠 Troubleshooting

### Port already in use
```bash
lsof -i :8010 | awk '{print $2}' | xargs kill -9
```

### Database locked
```bash
rm support.db database.sqlite
python database_setup.py
```

### Import problems
- Ensure `venv` is activated
- Re‑install dependencies

---

## 🎯 Key Features

- Independent agents with clean separation of responsibility  
- MCP-based tool execution  
- A2A orchestration via JSON‑RPC  
- Router powered by deterministic routing logic  
- Async end‑to‑end architecture  


