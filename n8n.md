---
description: Interact with the n8n REST API (list workflows, check executions, trigger workflows, etc.)
argument-hint: [action] [args...]
allowed-tools: Bash
---

# n8n REST API Interaction

You are interacting with a local n8n instance via its REST API.

## Connection Details

- **Base URL**: `http://localhost:5678/api/v1`
- **Auth**: Read the API key from the environment variable `N8N_API_KEY`

## How to Make Requests

Always use this pattern:

```bash
curl -s -H "X-N8N-API-KEY: $N8N_API_KEY" "http://localhost:5678/api/v1/{endpoint}"
```

Before making any request, verify the key is set:

```bash
if [ -z "$N8N_API_KEY" ]; then echo "ERROR: N8N_API_KEY environment variable is not set. Ask the user to set it."; exit 1; fi
```

## Available Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/workflows` | List all workflows |
| GET | `/workflows/{id}` | Get workflow details |
| POST | `/workflows` | Create a workflow |
| PUT | `/workflows/{id}` | Update a workflow |
| DELETE | `/workflows/{id}` | Delete a workflow |
| POST | `/workflows/{id}/activate` | Activate a workflow |
| POST | `/workflows/{id}/deactivate` | Deactivate a workflow |
| GET | `/executions` | List executions |
| GET | `/executions/{id}` | Get execution details |
| DELETE | `/executions/{id}` | Delete an execution |

## Interpreting the User's Request

Based on $ARGUMENTS, determine what the user wants:

- **"list workflows"** or **"workflows"** → GET /workflows, show a clean table of id, name, active status
- **"details {id}"** or **"show {name}"** → GET /workflows/{id}, summarize the nodes and connections
- **"executions"** or **"errors"** → GET /executions, focus on failed ones and show error messages
- **"execution {id}"** → GET /executions/{id}, show full details including error info
- **"activate {id}"** → POST /workflows/{id}/activate
- **"deactivate {id}"** → POST /workflows/{id}/deactivate
- **"trigger {id}"** or **"run {id}"** → POST /workflows/{id}/execute (if supported) or explain how to trigger
- **"create ..."** → Help build workflow JSON and POST /workflows
- Any other request → Use best judgment to map to the appropriate API call

Always format output cleanly for the user. For large JSON responses, parse and summarize the key information rather than dumping raw JSON.

## Safety

- **Never hardcode the API key** — always read from `$N8N_API_KEY`
- **Confirm before destructive actions** (DELETE, deactivate) with the user
- **For POST/PUT requests**, show the user the payload before sending
