# OpenClaw: Adding Subscription-Based LLM Models

## Context

OpenClaw is a multi-channel AI gateway (v2026.2.15) with extensible messaging integrations. It currently uses a **token-based authentication system** for LLM providers where each provider requires an API key and charges per token usage.

## Current Architecture

### Model Provider System

- **Provider Configuration**: `src/agents/models-config.ts` and `src/config/types.models.ts`
- **Authentication Modes**: Currently supports:
  - `api-key` - Standard API key authentication (current default)
  - `aws-sdk` - AWS Bedrock authentication
  - `oauth` - OAuth-based authentication
  - `token` - Token-based authentication

### Current Provider Structure

```typescript
type ModelProviderConfig = {
  baseUrl: string;
  apiKey?: string;
  auth?: ModelProviderAuthMode; // "api-key" | "aws-sdk" | "oauth" | "token"
  api?: ModelApi;
  headers?: Record<string, string>;
  authHeader?: boolean;
  models: ModelDefinitionConfig[];
};
```

### Cost Tracking

- Each model has a `cost` field tracking per-token costs:
  ```typescript
  cost: {
    input: number; // Cost per input token
    output: number; // Cost per output token
    cacheRead: number; // Cost per cached token read
    cacheWrite: number; // Cost per cached token write
  }
  ```

### Current Configuration (openclaw.json)

```json
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "anthropic/claude-sonnet-4-5"
      },
      "models": {
        "anthropic/claude-opus-4-6": {...},
        "anthropic/claude-sonnet-4-5": {...},
        "anthropic/claude-haiku-4-5": {...}
      }
    }
  }
}
```

## Requirements

We want to **add support for subscription-based LLM providers** instead of (or in addition to) token-based billing. Examples include:

1. **OpenAI Plus / ChatGPT Pro** - Monthly subscription with unlimited usage
2. **Claude.ai Pro** - $20/month subscription
3. **Perplexity Pro** - Monthly subscription
4. **GitHub Copilot** - Already has OAuth support, but needs subscription model
5. **Other subscription services** - Generic framework for subscription-based models

### Key Differences: Token-Based vs Subscription-Based

**Token-Based (Current)**:

- Pay per API call based on token usage
- Track costs per request
- Need API keys with billing attached
- Usage directly correlates to cost

**Subscription-Based (Desired)**:

- Fixed monthly fee regardless of usage
- No per-token cost tracking needed
- May have rate limits instead of cost limits
- Authentication might be session-based (cookies, OAuth tokens)
- May require different API endpoints (web vs API)

## Questions for Architect Review

### 1. **Architecture & Design**

- How should we extend the current `ModelProviderConfig` to support subscription-based models?
- Should we create a separate authentication flow for subscription models, or extend the existing auth system?
- How do we handle models that offer BOTH token-based API access AND subscription-based access (e.g., OpenAI has both API and ChatGPT Plus)?

### 2. **Cost Tracking & Analytics**

- How should we represent "cost" for subscription models? Set to zero? Create a new cost model?
- Should we track usage differently for subscription vs token-based models?
- How do we show cost analytics when some models are subscription-based?

### 3. **Authentication**

- For web-based subscriptions (like ChatGPT Plus), how should we handle:
  - Cookie-based authentication?
  - Session management?
  - Token refresh?
- Should we support browser automation for services that don't have official APIs?
- What's the best way to securely store subscription credentials?

### 4. **Rate Limiting**

- Subscription services often have rate limits instead of cost limits
- How should we implement rate limit tracking?
- Should rate limits be configurable per-subscription tier?

### 5. **Model Discovery**

- How do we auto-discover available models for subscription-based providers?
- Some services might not have a `/models` endpoint - how do we handle this?

### 6. **Configuration Schema**

- What should the config schema look like? Example options:

**Option A: Extend existing auth modes**

```typescript
type ModelProviderAuthMode = "api-key" | "aws-sdk" | "oauth" | "token" | "subscription"; // New mode

type ModelProviderConfig = {
  // ... existing fields
  subscription?: {
    tier: string; // "pro" | "plus" | "enterprise"
    renewalDate?: string; // When subscription renews
    rateLimit?: {
      // Rate limits instead of cost
      requestsPerMinute: number;
      requestsPerDay?: number;
    };
  };
};
```

**Option B: Separate subscription provider type**

```typescript
type SubscriptionProviderConfig = {
  baseUrl: string;
  auth: "oauth" | "cookie" | "session-token";
  tier: string;
  rateLimits: RateLimitConfig;
  models: ModelDefinitionConfig[]; // Cost fields would be zero
};
```

### 7. **Hybrid Models**

- Some providers offer both APIs (e.g., OpenAI has both paid API and ChatGPT Plus)
- Should we allow configuring the same provider twice with different auth modes?
- How do we route requests between subscription and API-based access?

### 8. **Migration & Backward Compatibility**

- This is an active project with existing configurations
- How do we ensure backward compatibility?
- Should we provide a migration tool for existing configs?

### 9. **Implementation Strategy**

- What files would need to be modified?
- Should we create new abstraction layers?
- Are there existing patterns in the codebase we should follow?

## Desired Outcome

A comprehensive architectural plan that includes:

1. **Proposed schema changes** for supporting subscription models
2. **Authentication strategy** for subscription-based services
3. **Cost tracking approach** that works for both token and subscription models
4. **Rate limiting design** for subscription tiers
5. **Migration path** from current architecture
6. **Implementation phases** with file-level changes
7. **Trade-offs and risks** of different approaches
8. **Recommended approach** with justification

## Additional Context

- Project uses TypeScript with strict types
- Has extensive test coverage (e2e tests visible in file listings)
- Already supports multiple auth modes (OAuth, AWS SDK, etc.)
- Has provider usage tracking in `src/infra/provider-usage.*` files
- Gateway architecture with protocol schemas
- Supports multiple channels (Telegram, Discord, etc.)

Please provide an **opus-level architect review** that:

- Considers all trade-offs
- Provides concrete implementation guidance
- Addresses backward compatibility
- Considers security implications
- Suggests best practices for this specific codebase
