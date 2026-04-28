# Introduction

Microsoft Foundry (formerly Azure AI Foundry) hosts Anthropic's Claude models as serverless API deployments. This gives you access to Claude's full model family — Opus, Sonnet, and Haiku — through your existing Azure subscription, with enterprise-grade authentication, billing, and governance.

## Why Use Claude Through Microsoft Foundry?

**Unified billing.** Claude usage is billed through your Azure subscription. No separate Anthropic account or billing relationship required.

**Enterprise authentication.** Use Microsoft Entra ID (Azure AD) for keyless authentication with full RBAC support, or use API keys for simpler setups.

**Compliance and governance.** Manage Claude model access through Azure's existing policy, networking, and monitoring infrastructure.

**Agentic coding.** Connect Claude Code in VS Code directly to your Foundry resource for AI-assisted development that routes through your Azure environment.

## Available Claude Models

| Model | Model ID | Context Window | Max Output | Best For |
|---|---|---|---|---|
| Claude Opus 4.7 | `claude-opus-4-7` | 1M tokens | 128K tokens | Most capable — complex reasoning, long-running tasks |
| Claude Opus 4.6 | `claude-opus-4-6` | 1M tokens | 128K tokens | Advanced coding, agentic workflows |
| Claude Opus 4.5 | `claude-opus-4-5` | 200K tokens | 64K tokens | Coding and agent tasks |
| Claude Opus 4.1 | `claude-opus-4-1` | 200K tokens | — | Complex reasoning |
| Claude Sonnet 4.6 | `claude-sonnet-4-6` | 1M tokens | 128K tokens | Frontier intelligence at scale |
| Claude Sonnet 4.5 | `claude-sonnet-4-5` | 200K tokens | — | Balanced performance and cost |
| Claude Haiku 4.5 | `claude-haiku-4-5` | 200K tokens | — | Fastest and cheapest — high-volume workloads |

All models support adaptive/extended thinking, vision (image + text), code generation, PDF processing, prompt caching, citations, and tool use.

## Architecture Overview

Claude models on Microsoft Foundry are deployed as **serverless API endpoints** (Global Standard deployment type). When you deploy a Claude model:

1. Azure provisions a serverless endpoint in your Foundry resource
2. Requests are routed through Azure's infrastructure to Anthropic's model hosting
3. Authentication is handled by either API keys or Microsoft Entra ID tokens
4. Billing flows through Azure Marketplace to your subscription

> [!NOTE]
> Claude models currently run on Anthropic-hosted infrastructure, not Azure regional infrastructure. EU data residency support is planned for 2026.
