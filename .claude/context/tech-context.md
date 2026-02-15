---
created: 2026-02-14T23:00:31Z
last_updated: 2026-02-14T23:00:31Z
version: 1.0
author: Claude Code PM System
---

# Technical Context

## Runtime Stack

### Core Platform

- **Runtime:** Node.js >=22.12.0 (currently v25.6.0)
- **Language:** TypeScript 5.9.3 (strict mode)
- **Package Manager:** pnpm (workspace mode)
- **License:** MIT

### Dependency Summary

- **Production Dependencies:** 52 packages
- **Development Dependencies:** 21 packages
- **Total Installed:** 73 packages

## Key Dependencies

### Build Tools

- **tsdown** — TypeScript compilation and bundling
- **Rolldown 1.0.0-rc.4** — Fast Rust-based bundler
- **pnpm** — Workspace-aware package manager

### UI Framework

- **Lit 3.3.2** — Lightweight Web Components library
  - Used for control panel dashboard
  - No virtual DOM overhead
  - Native browser standards

### Testing & Quality

- **Vitest 4.0.18** — Fast Vite-powered test framework
  - Unit testing
  - Integration testing
  - Watch mode support

- **oxlint** — Rust-based JavaScript/TypeScript linter
  - Fast static analysis
  - Modern ESLint alternative

- **oxfmt** — Rust-based code formatter
  - Consistent code style
  - High-performance formatting

### LLM Integrations

- **Anthropic SDK** — Claude API access (OAuth-based)
- **OpenAI SDK** — GPT API access (OAuth-based)
- Custom API key support for other providers

### Messaging Channels

Adapters for 10+ platforms:

- WhatsApp
- Telegram
- Slack
- Discord
- Google Chat
- Signal
- iMessage
- Microsoft Teams
- WebChat
- Additional third-party integrations

### Google Workspace Integration

- **`gog` skill** — OAuth-based Google services
  - Gmail API
  - Google Calendar API
  - Google Drive API
  - Google Contacts API
  - Google Sheets API
  - Google Docs API

## Build Pipeline

### Development

```bash
pnpm install          # Install dependencies
pnpm ui:build         # Build Lit UI components
pnpm build            # Compile TypeScript → openclaw.mjs
pnpm test             # Run Vitest suite
```

### Production

```bash
docker build -f Dockerfile .                    # Standard container
docker build -f Dockerfile.sandbox .            # Sandboxed execution
docker-compose up                               # Multi-container deployment
```

## Architecture Patterns

### TypeScript Configuration

- Strict mode enabled
- Path aliases for clean imports
- Monorepo workspace support
- ES module output

### File Naming Conventions

- **kebab-case** for all files
- `.ts` for source code
- `.test.ts` for tests
- `.spec.ts` for integration specs

### Code Quality Standards

- oxlint for static analysis
- oxfmt for automatic formatting
- Vitest for test coverage
- TypeScript strict null checks
