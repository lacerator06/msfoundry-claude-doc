# Azure Setup

This guide walks through creating a Microsoft Foundry resource, project, and deploying your first Claude model.

## Step 1: Create a Foundry Resource

1. Go to [ai.azure.com](https://ai.azure.com/)
2. Sign in with your Azure account
3. Select **+ Create resource** (or use an existing Foundry resource)
4. Fill in the required fields:
   - **Subscription**: Select your paid Azure subscription
   - **Resource group**: Select an existing group or create a new one
   - **Region**: Choose **East US 2** or **Sweden Central**
   - **Name**: Enter a unique resource name (this becomes part of your API endpoint URL)
5. Select **Review + create**, then **Create**

> [!TIP]
> Note your **resource name** — you will need it later for API endpoints and VS Code configuration. Your API base URL will be `https://<resource-name>.services.ai.azure.com/anthropic`.

## Step 2: Create a Foundry Project

1. In the Foundry portal, select your resource
2. Select **+ New project**
3. Enter a project name and confirm the region matches your resource (East US 2 or Sweden Central)
4. Select **Create**

The project provides an isolated workspace for managing model deployments, endpoints, and access keys.

## Step 3: Deploy a Claude Model

1. In your Foundry project, navigate to **Models + endpoints** in the left sidebar
2. Select **+ Deploy model** > **Deploy base model**
3. In the model catalog, search for **Claude** or browse the Anthropic section
4. Select the model you want to deploy (e.g., `claude-opus-4-6`)
5. Configure the deployment:
   - **Deployment name**: Defaults to the model ID; (claude-opus-4-6) you can customize this, but it **cannot be changed after creation**
   - **Deployment type**: Global Standard (the only option for Claude models)
6. Review the Anthropic terms of service and select **Deploy**
7. Wait for provisioning to complete (typically under a minute)

### Recommended Deployments

For a typical development setup, deploy these three models:

| Role | Model | Purpose |
|---|---|---|
| Primary | `claude-sonnet-4-6` | General coding and conversation |
| Fast | `claude-haiku-4-5` | Quick operations, file reads, lightweight tasks |
| Extended thinking | `claude-opus-4-7` | Complex reasoning, architecture decisions |

## Step 4: Get Your Endpoint and Key

After deployment completes:

1. Go to **Foundry Website -> Models + endpoints** and select your deployment
2. On the **Details** tab, find:
   - **Target URI**: Your API endpoint (e.g., `https://<resource-name>.services.ai.azure.com/anthropic/v1/messages`)
   - **Key**: Your API key for authentication
3. Alternatively, find the **Project API key** on the project **Home** page
4. Take note of the deployment name and the resource name. We will be needing this to the Visual Studio Code setup.
> [!IMPORTANT]
> Keep your API key secure. Do not commit it to source control. Use environment variables or Azure Key Vault for production workloads.

### Verify Your Deployment

Test your deployment with a quick cURL call:

```bash
curl -X POST "https://<resource-name>.services.ai.azure.com/anthropic/v1/messages" \
  -H "Content-Type: application/json" \
  -H "x-api-key: YOUR_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "claude-sonnet-4-6",
    "max_tokens": 256,
    "messages": [
      {"role": "user", "content": "Hello, Claude! Confirm you are running on Azure."}
    ]
  }'
```

If you receive a JSON response with a `content` array, your deployment is working. Proceed to [Authentication](authentication.md) to choose your auth strategy, or jump to [VS Code Setup](vscode-setup.md) to start coding.

## Next Steps

- [Authentication](authentication.md) — Configure API key or Entra ID authentication
- [VS Code Setup](vscode-setup.md) — Connect Claude Code to your Foundry resource
- [API Access](api-access.md) — Call Claude from your applications using SDKs
