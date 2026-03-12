# n8n Agent Patterns (2026 Edition)

**Goal**: Leverage the latest n8n AI capabilities for autonomous and semi-autonomous automation.

---

## 1. Agent Memory Graph
Instead of simple buffer memory, use the **Memory Graph** pattern to allow agents to "remember" entities across different workflows.

**Structure**:
```
AI Agent → Vector Store (Supabase/Pinecone) → Entity Extraction → Memory Graph
```

**Key Tip**: Use the `n8n-nodes-base.memory` node with a `sessionId` linked to the user's unique ID.

---

## 2. Sub-Agent Orchestration
Break complex tasks into specialized sub-agents.

**Pattern**:
*   **Main Agent**: Routes requests.
*   **Data Agent**: Handles database queries.
*   **Action Agent**: Executes external API calls.

**Implementation**: Use the `Execute Workflow` node as a **Tool** for the main agent.

---

## 3. Tool-Call Validation (Human-in-the-loop)
For high-stakes tools (e.g., deleting data, sending mass emails), implement a validation step.

**Pattern**:
1. Agent requests tool execution.
2. Workflow pauses at a **Wait** node.
3. User receives a message (Slack/WhatsApp) with a "Confirm" button.
4. Workflow continues only after approval.

---

## 4. Advanced Expression Syntax (2026)
*   `{{ $json.output.reasoning }}`: Access the reasoning trace of an AI Agent node.
*   `{{ $vars.global_context }}`: Use global variables for system-wide prompts.

---

## 5. Security Best Practices
*   **NEVER** use `{{ $json.body.apiKey }}` in an HTTP node directly.
*   **ALWAYS** use the **Credentials** port and reference the credential name.
*   Use the **Limit** node to prevent runaway recursive tool calls in AI loops.
