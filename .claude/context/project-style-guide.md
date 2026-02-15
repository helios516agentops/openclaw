---
created: 2026-02-14T23:00:31Z
last_updated: 2026-02-14T23:00:31Z
version: 1.0
author: Claude Code PM System
---

# Project Style Guide

## Code Style Standards

### TypeScript Configuration

#### Strict Mode (Always Enabled)

```json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "strictBindCallApply": true,
    "strictPropertyInitialization": true,
    "noImplicitThis": true,
    "alwaysStrict": true
  }
}
```

#### File Naming Conventions

- **kebab-case for all files:** `my-component.ts`, `user-service.ts`
- **Test files:** `my-component.test.ts` (unit tests)
- **Integration tests:** `my-feature.spec.ts`
- **Type definitions:** `my-types.d.ts`
- **Configuration:** `my-config.json`

**Examples:**

```
✅ Good:
  - src/agent-runner.ts
  - src/model-resolver.ts
  - skills/gog-skill/gmail-adapter.ts
  - test/session-history.test.ts

❌ Bad:
  - src/AgentRunner.ts
  - src/model_resolver.ts
  - skills/gog_skill/GmailAdapter.ts
  - test/SessionHistory.test.ts
```

### Code Formatting

#### oxfmt (Rust-based Formatter)

Primary code formatter for all TypeScript/JavaScript files.

**Configuration:**

```json
{
  "printWidth": 100,
  "tabWidth": 2,
  "useTabs": false,
  "semi": true,
  "singleQuote": true,
  "trailingComma": "all",
  "bracketSpacing": true,
  "arrowParens": "always"
}
```

**Run Commands:**

```bash
pnpm format          # Format all files
pnpm format:check    # Check without modifying
```

#### Manual Style Rules

- **Indentation:** 2 spaces (no tabs)
- **Line Length:** 100 characters max
- **Quotes:** Single quotes for strings (`'hello'` not `"hello"`)
- **Semicolons:** Always required (no ASI reliance)
- **Trailing Commas:** Always in multi-line arrays/objects

### Linting

#### oxlint (Rust-based Linter)

Primary static analysis tool for catching errors and enforcing patterns.

**Key Rules Enabled:**

- No unused variables
- No implicit any types
- No console.log in production code (use logger)
- Prefer const over let
- No var declarations (use const/let)

**Run Commands:**

```bash
pnpm lint            # Lint all files
pnpm lint:fix        # Auto-fix issues
```

### Testing

#### Vitest 4.0.18 (Vite-Powered Test Framework)

**Test File Structure:**

```typescript
import { describe, it, expect, beforeEach, afterEach } from "vitest";
import { MyClass } from "./my-class";

describe("MyClass", () => {
  let instance: MyClass;

  beforeEach(() => {
    instance = new MyClass();
  });

  afterEach(() => {
    // Cleanup
  });

  it("should do something", () => {
    expect(instance.method()).toBe(true);
  });

  it("should handle errors", () => {
    expect(() => instance.methodThatThrows()).toThrow();
  });
});
```

**Test Naming:**

- Use `describe()` for grouping related tests
- Use `it()` for individual test cases (not `test()`)
- Start test descriptions with "should" or "when"

**Run Commands:**

```bash
pnpm test              # Run all tests
pnpm test:watch        # Watch mode
pnpm test:coverage     # Generate coverage report
pnpm test:ui           # Interactive UI
```

**Coverage Targets:**

- **Minimum:** 70% overall coverage
- **Goal:** 85%+ for core modules (agent-runner, model-resolver)
- **Stretch:** 95%+ for critical paths (auth, data persistence)

## UI Component Standards

### Lit 3.3.2 (Web Components)

**Component File Structure:**

```typescript
import { LitElement, html, css } from "lit";
import { customElement, property } from "lit/decorators.js";

@customElement("my-component")
export class MyComponent extends LitElement {
  static styles = css`
    :host {
      display: block;
    }
  `;

  @property({ type: String })
  name = "";

  render() {
    return html`<div>Hello, ${this.name}!</div>`;
  }
}
```

**Component Naming:**

- **Tag names:** `my-component` (kebab-case, always with prefix)
- **Class names:** `MyComponent` (PascalCase)
- **File names:** `my-component.ts` (kebab-case, match tag name)

**Best Practices:**

- Use Shadow DOM for style encapsulation
- Prefer `@property()` decorators over manual property definitions
- Always define `static styles` (don't use inline styles)
- Use `html` tagged template literals (never strings)

## Monorepo Patterns

### pnpm Workspace Configuration

**Root `package.json`:**

```json
{
  "name": "openclaw-monorepo",
  "private": true,
  "workspaces": ["packages/*", "apps/*"]
}
```

**Workspace Dependencies:**

- Use workspace protocol: `"@openclaw/core": "workspace:*"`
- Never use relative paths in imports (configure tsconfig paths)

**Example tsconfig.json:**

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@openclaw/core/*": ["packages/core/src/*"],
      "@openclaw/skills/*": ["packages/skills/src/*"]
    }
  }
}
```

## Git Commit Style

### Commit Message Format

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**

- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation only
- `style`: Code style (formatting, no logic change)
- `refactor`: Code restructuring (no behavior change)
- `test`: Add/update tests
- `chore`: Build process, dependencies, tooling

**Examples:**

```
✅ Good:
  feat(skills): add gmail skill for reading emails
  fix(agent-runner): handle empty session history
  docs(readme): update installation instructions

❌ Bad:
  added gmail skill
  fix bug
  updated readme
```

## Documentation Standards

### Inline Comments

- Use JSDoc for all public APIs
- Explain WHY, not WHAT (code should be self-documenting)
- Keep comments up-to-date (delete stale comments)

**Example:**

```typescript
/**
 * Resolves the best LLM provider based on user preferences and availability.
 * Falls back to secondary providers if primary is unavailable.
 *
 * @param preferences - User-defined provider priorities
 * @returns Resolved provider instance
 * @throws {NoProvidersAvailableError} If all providers fail health checks
 */
export function resolveProvider(preferences: ProviderPreferences): Provider {
  // Implementation
}
```

### README Files

Every package/skill/app should have a README with:

1. **Purpose:** What it does in 1-2 sentences
2. **Usage:** Code examples or CLI commands
3. **API:** Public interfaces (if applicable)
4. **Configuration:** Required settings
5. **Testing:** How to run tests

## Performance Guidelines

### Bundle Size

- **Target:** <500KB for UI bundle (minified, gzipped)
- **Lazy Load:** Split large skills into separate chunks
- **Tree Shaking:** Ensure all exports are used (oxlint will warn)

### Runtime Performance

- **Startup Time:** <1 second for agent initialization
- **Response Time:** <500ms for simple skill executions
- **Memory:** <200MB baseline (excluding LLM context)

### Monitoring

- Use `console.time()` / `console.timeEnd()` for profiling
- Add performance metrics to session history
- Set up alerts for >2s response times

## Accessibility (UI)

### WCAG 2.1 Level AA Compliance

- All interactive elements must be keyboard-navigable
- Color contrast ratio ≥4.5:1 for text
- ARIA labels for non-semantic elements
- Focus indicators visible on all controls

### Lit Component Requirements

```typescript
render() {
  return html`
    <button
      aria-label="Send message"
      @click=${this.handleClick}
    >
      Send
    </button>
  `;
}
```

## Security Best Practices

### API Key Handling

- **NEVER** commit API keys to git
- Store in `~/.openclaw/openclaw.json` (encrypted at rest)
- Use environment variables for CI/CD
- Rotate keys every 90 days

### Input Validation

- Validate all user inputs (messages, file uploads)
- Sanitize data before passing to skills
- Reject oversized payloads (>10MB)

### Dependency Auditing

```bash
pnpm audit              # Check for vulnerabilities
pnpm update             # Update to latest patches
```

## Deprecation Policy

### How to Deprecate APIs

1. Add `@deprecated` JSDoc tag with migration path
2. Log warning on first use (not every use)
3. Keep deprecated code for 2 major versions
4. Remove in third major version

**Example:**

```typescript
/**
 * @deprecated Use `newMethod()` instead. Will be removed in v3.0.0.
 */
export function oldMethod() {
  console.warn("oldMethod is deprecated. Use newMethod instead.");
  // Implementation
}
```
