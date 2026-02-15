---
created: 2026-02-14T23:00:31Z
last_updated: 2026-02-14T23:00:31Z
version: 1.0
author: Claude Code PM System
---

# Project Brief

## What is OpenClaw?

**OpenClaw** is a self-hosted personal AI assistant framework that runs locally on your machine and answers on your messaging channels. Unlike cloud-based AI services, OpenClaw prioritizes privacy, extensibility, and local-first compute.

### Elevator Pitch

"Your personal AI assistant that lives in Slack, WhatsApp, or Telegram — and actually does things like scheduling meetings, sending emails, and searching your files. Fully self-hosted, privacy-preserving, and extensible with custom skills."

## Why OpenClaw Exists

### Problem Statement

Modern AI assistants suffer from three critical flaws:

1. **Privacy Invasion:** Commercial services upload your data to their clouds
2. **Limited Actions:** Most AI just talks; it doesn't DO things
3. **Vendor Lock-in:** Proprietary platforms with no extensibility

### OpenClaw's Solution

1. **Privacy-First:** All compute happens locally. Zero telemetry.
2. **Action-Oriented:** 50+ skills execute real tasks (email, calendar, files, APIs)
3. **Fully Extensible:** Write custom skills in TypeScript, publish to ClawHub marketplace

## Core Principles

### 1. Self-Hosted

- Runs on user's own hardware (laptop, homelab, VPS)
- No data leaves your machine unless you explicitly grant permission
- Docker support for easy deployment

### 2. Privacy-Preserving

- No telemetry, analytics, or tracking
- OAuth credentials stored locally in encrypted config
- Session history saved to local JSONL files

### 3. Local-First Compute

- AI models accessed via API (Anthropic, OpenAI) but no user data uploaded
- Skills execute locally with direct filesystem/API access
- Optional sandbox mode for untrusted code

### 4. Multi-Channel

- Single AI agent, many interfaces
- Users interact via their preferred messaging app
- Unified conversation history across channels

### 5. Extensible by Design

- Plugin-based skill system
- ClawHub marketplace for community skills
- Simple TypeScript API for custom development

## Success Criteria

### MVP (Minimum Viable Product)

- ✅ Runs locally on macOS/Linux/Windows
- ✅ Connects to at least 1 messaging channel (Slack, Telegram, or WhatsApp)
- ✅ Executes at least 5 skills successfully
- ✅ Persists session history across restarts
- ✅ OAuth setup for LLM providers (Anthropic/OpenAI)

### V1.0 Goals

- Connects to 3+ messaging channels simultaneously
- 20+ working skills (including full Google Workspace integration)
- Cron-based autonomous tasks (morning briefings, scheduled reports)
- Web-based control panel UI for configuration
- Public ClawHub marketplace with 10+ community skills

### Long-Term Vision

- 100+ skills available in marketplace
- Mobile apps (iOS/Android) for on-the-go access
- Voice interface support (Siri, Alexa shortcuts)
- Multi-user support for family/team deployments
- Integration with local AI models (Ollama, llama.cpp) for fully offline mode

## Technical Scope

### In Scope

- Node.js runtime (>=22.12.0)
- TypeScript codebase (strict mode)
- Lit-based web UI for control panel
- OAuth authentication for LLM providers
- Messaging channel adapters (10+ platforms)
- Skill system with plugin architecture
- Docker deployment support
- JSONL session history format
- Local configuration storage (~/.openclaw/openclaw.json)

### Out of Scope (for now)

- Browser extension
- Desktop app (native macOS/Windows)
- Mobile-first UI
- Multi-user authentication
- Hosted/SaaS offering
- GUI-based skill builder

## Target Deployment Environments

1. **Local Development:** `pnpm build && node openclaw.mjs`
2. **Docker Container:** `docker build -f Dockerfile .`
3. **Sandboxed Execution:** `docker build -f Dockerfile.sandbox .`
4. **Multi-Container:** `docker-compose up` (UI + runtime + message queue)

## Key Differentiators

### vs. Cloud AI Assistants

- **Privacy:** No data leaves your machine
- **Extensibility:** Write custom skills in TypeScript
- **Cost:** Pay only for LLM API calls, not for the assistant itself

### vs. DIY Chatbot Scripts

- **Polish:** Production-ready framework, not a weekend hack
- **Ecosystem:** ClawHub marketplace, community support
- **Maintenance:** Active development, regular updates

### vs. Enterprise AI Platforms

- **Simplicity:** No enterprise sales, no per-seat pricing
- **Control:** You own the code, the data, the deployment
- **Flexibility:** Integrate with any API or local tool

## Risks & Mitigations

| Risk                                      | Impact                | Mitigation                                                         |
| ----------------------------------------- | --------------------- | ------------------------------------------------------------------ |
| LLM API costs too high                    | Users abandon project | Provide cost tracking dashboard, support cheaper models            |
| Setup too complex for non-technical users | Limited adoption      | Improve onboarding wizard, Docker one-click deploy                 |
| Skill quality varies wildly               | Poor user experience  | ClawHub curation process, user ratings                             |
| Breaking changes in LLM APIs              | Runtime failures      | Model Resolver with failover, adapter pattern                      |
| Privacy expectations unmet                | Trust erosion         | Clear documentation on what data leaves machine (only LLM prompts) |

## Open Questions

- Should we support local AI models (Ollama, llama.cpp) for fully offline mode?
- What is the right abstraction for multi-user deployments (family/team)?
- How do we balance ease-of-use (GUI builders) with power-user needs (code)?
