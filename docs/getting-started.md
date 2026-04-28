# Getting Started

This guide walks you through the fastest path to using Claude on Microsoft Foundry — from zero to a working API call in about 10 minutes.

## Quick Start Steps

### 1. Verify Prerequisites

You need a paid Azure subscription (EA or MCA-E recommended) with Marketplace purchasing enabled. See [Prerequisites](prerequisites.md) for full details.

### 2. Create a Foundry Project

1. Go to [ai.azure.com](https://ai.azure.com/)
2. Create a Foundry resource in **East US 2** or **Sweden Central**
3. Create a project within that resource

### 3. Deploy a Claude Model

1. In your project, go to **Models + endpoints** > **+ Deploy model** > **Deploy base model**
2. Search for **claude-sonnet-4-6** and deploy it
3. Copy your **Target URI** and **API Key** from the deployment details

### 4. Test the API

```bash
curl -X POST "https://<resource-name>.services.ai.azure.com/anthropic/v1/messages" \
  -H "Content-Type: application/json" \
  -H "x-api-key: YOUR_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "claude-sonnet-4-6",
    "max_tokens": 256,
    "messages": [{"role": "user", "content": "Hello, Claude!"}]
  }'
```

### 5. Set Up VS Code (Optional)

For agentic coding with Claude Code:

```bash
# Install Claude Code
irm https://claude.ai/install.ps1 | iex

# Configure Foundry
export CLAUDE_CODE_USE_FOUNDRY=1
export ANTHROPIC_FOUNDRY_RESOURCE="your-resource-name"
export ANTHROPIC_FOUNDRY_API_KEY="your-api-key"
```

Install the Claude Code VS Code extension from the marketplace and verify with `/status`.

## What's Next?

| Guide | Description |
|---|---|
| [Azure Setup](azure-setup.md) | Detailed walkthrough of resource and model deployment |
| [Authentication](authentication.md) | API key vs. Entra ID configuration |
| [VS Code Setup](vscode-setup.md) | Claude Code, Foundry Toolkit, and GitHub Copilot integration |
| [API Access](api-access.md) | SDK examples for Python, TypeScript, C#, and REST |
| [Troubleshooting](troubleshooting.md) | Common issues and fixes |
