---
created: 2026-02-14T23:00:31Z
last_updated: 2026-02-14T23:00:31Z
version: 1.0
author: Claude Code PM System
---

# System Patterns

## Core Architecture: Gateway Pattern

OpenClaw uses a **Gateway Architecture** to route messages from multiple channels through a unified AI agent runtime.

```
[Messaging Channels] → [Gateway] → [Agent Runner] → [LLM Providers]
         ↓                             ↓
    [Adapters]                   [Skills System]
```

## Key Components

### 1. Agent Runner

- **Purpose:** Orchestrates AI agent execution
- **Responsibilities:**
  - Load system prompts
  - Manage conversation context
  - Execute skill calls
  - Return responses to channels
- **Location:** `src/` (agent runner implementation)

### 2. Model Resolver

- **Purpose:** Intelligent LLM provider selection with failover
- **Features:**
  - Primary/fallback model chains
  - Automatic retry on provider failure
  - Cost-aware routing
  - Provider-specific quirks handling
- **Supported Providers:**
  - Anthropic (Claude)
  - OpenAI (GPT-4, GPT-3.5)
  - Custom API endpoints

### 3. System Prompt Builder

- **Purpose:** Construct context-aware prompts for AI agents
- **Inputs:**
  - User message
  - Session history
  - Active skills
  - Channel metadata
- **Output:** Structured prompt for LLM

### 4. Session History Loader

- **Format:** JSONL (JSON Lines)
  - One JSON object per line
  - Append-only for performance
  - Easy streaming and truncation
- **Contents:**
  - User messages
  - Agent responses
  - Skill execution logs
  - Timestamps and metadata
- **Location:** `~/.openclaw/sessions/`

## Skill System

### Plugin Architecture

- **Discovery:** Automatic skill loading from `skills/` directory
- **Interface:** Standardized skill API contract
- **Isolation:** Sandboxed execution (optional via Dockerfile.sandbox)
- **Marketplace:** ClawHub integration for third-party skills

### Skill Lifecycle

1. **Registration:** Skill declares capabilities and triggers
2. **Activation:** Agent detects trigger keywords in user message
3. **Execution:** Skill runs with access to context and tools
4. **Response:** Skill returns structured data to Agent Runner
5. **Logging:** Execution recorded in session history

### Built-in Skills

- **gog:** Google Workspace integration (Gmail, Calendar, Drive, etc.)
- **Additional 50+ skills** in marketplace

## Multi-Channel Messaging

### Adapter Pattern

Each messaging platform has a dedicated adapter:

- **Input:** Platform-specific message format
- **Output:** Normalized internal message object
- **Bidirectional:** Handles incoming messages and outgoing responses

### Supported Channels

1. WhatsApp
2. Telegram
3. Slack
4. Discord
5. Google Chat
6. Signal
7. iMessage
8. Microsoft Teams
9. WebChat
10. Custom integrations

### Channel Features

- **Authentication:** OAuth or API key per channel
- **Rate Limiting:** Per-channel throttling
- **Media Support:** Images, files, voice messages
- **Rich Formatting:** Markdown, buttons, cards (where supported)

## Authentication & Security

### LLM Provider Auth

- **OAuth-based:** Anthropic, OpenAI (user delegates access)
- **API Keys:** Custom providers and legacy integrations
- **Storage:** Encrypted in `~/.openclaw/openclaw.json`

### Skill Permissions

- **Explicit Grants:** Users approve skill access to data sources
- **Scoped Tokens:** OAuth tokens with minimal required scopes
- **Audit Logging:** All skill executions logged in session history

## Persistence

### Configuration

- **File:** `~/.openclaw/openclaw.json`
- **Format:** JSON
- **Contents:** API keys, channel credentials, skill settings

### Session Data

- **Format:** JSONL (append-only)
- **Storage:** `~/.openclaw/sessions/{channel_id}/{session_id}.jsonl`
- **Retention:** Configurable (default: 30 days)

## Deployment Patterns

### Local Execution

- Node.js process on user's machine
- Direct filesystem access
- Low latency for skill execution

### Docker Container

- Isolated environment
- Consistent runtime across platforms
- Optional sandbox mode for untrusted skills

### Multi-Container Orchestration

- `docker-compose.yml` for complex setups
- Separate containers for UI, agent runtime, message queue
- Shared volumes for session persistence
