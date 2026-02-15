---
created: 2026-02-14T23:00:31Z
last_updated: 2026-02-14T23:00:31Z
version: 1.0
author: Claude Code PM System
---

# Project Overview

## OpenClaw v2026.2.13

**Tagline:** Personal AI Assistant Framework — Self-Hosted, Privacy-First, Action-Oriented

## Feature Summary

### Multi-Channel Messaging (10+ Platforms)

OpenClaw provides adapters for all major messaging platforms:

1. **WhatsApp** — Personal messaging
2. **Telegram** — Encrypted, bot-friendly
3. **Slack** — Team collaboration
4. **Discord** — Community and gaming
5. **Google Chat** — Enterprise messaging
6. **Signal** — Privacy-focused
7. **iMessage** — Apple ecosystem
8. **Microsoft Teams** — Enterprise collaboration
9. **WebChat** — Browser-based interface
10. **Custom Integrations** — Extensible adapter pattern

### Skill System (50+ Skills)

Skills enable OpenClaw to execute real-world actions:

#### Google Workspace (`gog` skill)

- **Gmail:** Read, send, search, label emails
- **Calendar:** Create events, check availability, send invites
- **Drive:** Search files, upload/download, share links
- **Contacts:** Look up people, add/update contact info
- **Sheets:** Query data, update cells, create reports
- **Docs:** Generate documents, extract text

#### Additional Skills (via ClawHub)

- **Weather:** Real-time forecasts
- **News:** Curated briefings
- **GitHub:** PR stats, issue tracking
- **Jira:** Task management
- **Spotify:** Music control
- **Smart Home:** IoT device control
- **And 40+ more** in marketplace

### Authentication

#### LLM Provider OAuth

- **Anthropic (Claude):** OAuth-based API access
  - User grants permission via OAuth flow
  - No API key management required
  - Scoped tokens for security

- **OpenAI (GPT):** OAuth-based API access
  - Same OAuth pattern as Anthropic
  - Supports GPT-4, GPT-3.5, and future models

#### Custom API Keys

- Fallback for providers without OAuth
- Encrypted storage in `~/.openclaw/openclaw.json`
- Per-skill key management

### Deployment Options

#### 1. Local Execution

```bash
pnpm install
pnpm ui:build
pnpm build
node openclaw.mjs
```

- Runs directly on host machine
- Best for development and personal use
- Full filesystem access

#### 2. Docker Container

```bash
docker build -f Dockerfile -t openclaw:latest .
docker run -v ~/.openclaw:/root/.openclaw openclaw:latest
```

- Isolated environment
- Consistent runtime across platforms
- Volume mount for persistent config

#### 3. Sandboxed Execution

```bash
docker build -f Dockerfile.sandbox -t openclaw:sandbox .
docker run --security-opt seccomp=unconfined openclaw:sandbox
```

- For running untrusted skills
- Limited filesystem access
- Network isolation options

#### 4. Multi-Container Orchestration

```bash
docker-compose up
```

- Separate containers for UI, runtime, message queue
- Scalable architecture
- Shared volumes for session data

### Cron Jobs (Autonomous Tasks)

OpenClaw can execute scheduled tasks without user prompts:

#### Built-in Schedules

- **Morning Briefing:** 7:00 AM (weather, calendar, unread emails)
- **Daily Standup:** 5:00 PM (task summary, tomorrow's schedule)
- **Weekly Reports:** Friday 4:00 PM (time tracking, expense summary)

#### Custom Schedules

Users can define cron expressions for any skill:

```json
{
  "cron": "0 9 * * 1-5",
  "skill": "gog.calendar",
  "action": "listToday"
}
```

### Persistent Memory

#### Session History

- **Format:** JSONL (JSON Lines)
- **Location:** `~/.openclaw/sessions/{channel_id}/{session_id}.jsonl`
- **Contents:**
  - User messages
  - Agent responses
  - Skill executions
  - Timestamps
  - Metadata (channel, user ID)

#### Configuration

- **File:** `~/.openclaw/openclaw.json`
- **Format:** JSON
- **Contents:**
  - API keys (encrypted)
  - Channel credentials
  - Skill settings
  - User preferences
  - Cron schedules

### Sandbox Execution

For untrusted skills (marketplace downloads):

#### Security Features

- **Filesystem Isolation:** Skills can't access parent directories
- **Network Restrictions:** Whitelist allowed domains
- **Resource Limits:** CPU/memory caps via Docker
- **Audit Logging:** All skill actions logged to session history

#### Developer Mode

- Disable sandbox for trusted skills (faster execution)
- Full access to local tools and APIs
- Recommended only for self-authored skills

### Control Panel UI

#### Features

- **Skill Management:** Install, enable/disable, configure skills
- **Channel Configuration:** Add/remove messaging integrations
- **Session Browser:** View conversation history
- **Cost Tracking:** LLM API usage and expenses
- **Cron Editor:** Visual cron schedule builder

#### Technology

- **Framework:** Lit 3.3.2 (Web Components)
- **Build:** Rolldown bundler
- **Hosting:** Embedded server in openclaw.mjs
- **Access:** `http://localhost:8080` (configurable port)

## Monorepo Structure

### Workspace Packages

- **`packages/core`** — Shared utilities, types
- **`packages/skills`** — Skill SDK and base classes
- **`packages/adapters`** — Channel adapter interfaces
- **`packages/ui`** — Lit component library

### Apps

- **`apps/cli`** — Command-line interface
- **`apps/web`** — Web-based control panel
- **`apps/desktop`** — Electron wrapper (future)
- **`apps/mobile`** — React Native app (future)

## Development Workflow

### Adding a New Skill

1. Create skill directory in `skills/my-skill/`
2. Implement skill interface (TypeScript)
3. Register skill in `skills/index.ts`
4. Add tests in `skills/my-skill/my-skill.test.ts`
5. Document in `skills/my-skill/README.md`
6. Publish to ClawHub (optional)

### Adding a New Channel

1. Create adapter in `src/adapters/my-channel.ts`
2. Implement adapter interface (normalize input/output)
3. Add credentials schema to config
4. Register adapter in `src/adapters/index.ts`
5. Test with real messages
6. Document setup in `docs/channels/my-channel.md`

### Running Tests

```bash
pnpm test              # Run all Vitest tests
pnpm test:watch        # Watch mode
pnpm test:coverage     # Generate coverage report
```

### Code Quality

```bash
pnpm lint              # Run oxlint
pnpm format            # Run oxfmt
pnpm typecheck         # TypeScript strict check
```

## Roadmap

### Current Version (v2026.2.13)

- 10+ messaging channels
- 50+ skills (marketplace)
- OAuth for LLM providers
- Docker deployment
- JSONL session history
- Lit-based UI

### Next Release (v2026.3.x)

- Mobile apps (iOS/Android)
- Voice interface support
- Multi-user authentication
- Enhanced sandbox security
- Performance optimizations

### Future (v2026.4.x+)

- Local AI model support (Ollama, llama.cpp)
- Visual skill builder (no-code)
- Team/family deployment mode
- End-to-end encryption for sessions
- Plugin marketplace revenue sharing
