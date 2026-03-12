---
name: n8n-expert
description: Expert guide for n8n workflow creation, troubleshooting, and optimization. Use when writing expressions, configuring nodes, or designing complex workflows.
---

# n8n Master Skill

This skill empowers you to be an n8n expert, providing syntax rules, common patterns, and validation guidelines for building resilient automation workflows.

## Core Capabilities

1.  **Expression Syntax**: Write correct `{{ $json... }}` expressions.
2.  **Code Nodes**: Write effective JavaScript and Python code for data transformation.
3.  **Workflow Patterns**: Implement resilient patterns for webhooks, APIs, and AI agents.
4.  **Debugging**: Identify and fix common n8n errors (like Webhook data paths).

---

## 1. Expression Syntax

**Rule #1:** All dynamic content in parameters must use double curly braces: `{{ expression }}`.

### Essential Variables

*   `{{ $json.field }}`: Access data from the *current* node's input item.
*   `{{ $node["Node Name"].json.field }}`: Access data from a *previous* node.
    *   **CRITICAL:** Node names with spaces MUST use quotes and bracket notation.
    *   ✅ `{{ $node["HTTP Request"].json.data }}`
    *   ❌ `{{ $node.HTTP Request.json.data }}`
*   `{{ $now }}`: Current timestamp (Luxon object).
*   `{{ $env.VAR_NAME }}`: Environment variables.

### 🚨 The Webhook "Gotcha"

Webhook data is NOT at the root. It is wrapped in `body`, `headers`, `query`.

*   ❌ WRONG: `{{ $json.email }}`
*   ✅ CORRECT: `{{ $json.body.email }}`

**References:**
*   [Common Mistakes](references/COMMON_MISTAKES.md)
*   [Expression Examples](references/EXAMPLES.md)

---

## 2. Code Nodes (JavaScript & Python)

**Rule #2:** DO NOT use `{{ }}` expressions inside Code Nodes. Use native variable access.

### JavaScript (Node.js)

```javascript
// Accessing input data
const email = $input.item.json.email; // From current item
const allData = $input.all(); // All items

// Returning data
return {
  json: {
    processed_email: email.toLowerCase(),
    timestamp: new Date().toISOString()
  }
};
```

**References:**
*   [JS Built-in Functions](references/BUILTIN_FUNCTIONS.md)
*   [JS Patterns](references/JAVASCRIPT_PATTERNS.md)

### Python

```python
# Accessing input data
email = _input.item.json.get('email')

# Returning data
return {
  'json': {
    'processed_email': email.lower(),
    'timestamp': datetime.now().isoformat()
  }
}
```

**References:**
*   [Python Patterns](references/PYTHON_PATTERNS.md)

---

## 3. Workflow Patterns

### Resilient Webhooks
Always add an "Error Trigger" workflow or use the "Continue On Fail" setting for critical nodes to prevent silent failures.

### AI Agent Integration
When building AI agents, ensure your "Tools" return clear, JSON-structured data that the LLM can easily parse.

**References:**
*   [AI Agent Workflows](references/ai_agent_workflow.md)
*   [2026 Agent Patterns](references/2026_AGENT_PATTERNS.md)
*   [Database Operations](references/database_operations.md)
*   [HTTP Integrations](references/http_api_integration.md)

---

## 4. MCP Integration (n8n-mcp)

You have `n8n-mcp` configured! This means you can inspect your local n8n instance directly.

**Useful Commands:**
*   "List my workflows" -> Uses MCP to see what you have running.
*   "Get workflow schema for [ID]" -> Retrives the JSON structure for analysis.
*   "Activate workflow [ID]" -> Turns it on via CLI.

---

## Quick Checklist for "Why isn't this working?"

1.  **Did you use `{{ }}`?** (Unless it's a Code Node).
2.  **Does the node name have spaces?** Use `["Node Name"]`.
3.  **Is it Webhook data?** Check `$json.body`.
4.  **Is the previous node actually executing?** n8n only passes data from *executed* nodes.