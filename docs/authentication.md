# Authentication

Microsoft Foundry supports two authentication methods for Claude model endpoints: **API keys** and **Microsoft Entra ID** (keyless). Choose based on your security requirements and environment.

## Option 1: API Key Authentication

The simplest approach. Best for local development and quick testing.

### How It Works

Each Foundry resource has API keys that you pass in the request header. Foundry accepts either header name:

- `x-api-key: <your-key>` (Anthropic convention)
- `api-key: <your-key>` (Azure convention)

### Finding Your API Key

1. Open your project in [ai.azure.com](https://ai.azure.com/)
2. Go to the **Home** page and copy the **Project API key**
3. Alternatively, navigate to **Models + endpoints** > select your deployment > **Details** tab

### Using the API Key

Set it as an environment variable:

```bash
export ANTHROPIC_FOUNDRY_API_KEY="your-api-key-here"
```

Or pass it directly in code (not recommended for production):

```python
from anthropic import AnthropicFoundry

client = AnthropicFoundry(
    api_key="your-api-key-here",
    base_url="https://<resource-name>.services.ai.azure.com/anthropic"
)
```

> [!WARNING]
> Never commit API keys to source control. Use environment variables, `.env` files (added to `.gitignore`), or a secret manager like Azure Key Vault.

## Option 2: Microsoft Entra ID (Recommended)

Keyless authentication using Azure identity tokens. Recommended for production workloads and team environments.

### How It Works

Instead of a static API key, you obtain a short-lived OAuth token from Microsoft Entra ID and pass it as a Bearer token. The token is scoped to Azure AI services.

### Prerequisites

1. **Azure CLI** installed and signed in:
   ```powershell
   winget install -e --id Microsoft.AzureCLI
   ```

   ```bash
   az login
   ```

   If your Foundry resource is in a different tenant:

   ```bash
   az login --tenant <tenant-id>
   ```

2. **RBAC role** assigned on the Foundry resource — one of:
   - **Azure AI Developer** (recommended, Foundry-native)
   - **Cognitive Services User** (legacy, also works)

   Assign via the Azure portal: Foundry resource > **Access control (IAM)** > **Add role assignment**.

3. **Azure Identity SDK** installed (for programmatic use):

   ```bash
   # Python
   pip install azure-identity

   # Node.js
   npm install @azure/identity
   ```

### Python Example

```python
from anthropic import AnthropicFoundry
from azure.identity import DefaultAzureCredential, get_bearer_token_provider

token_provider = get_bearer_token_provider(
    DefaultAzureCredential(),
    "https://ai.azure.com/.default"
)

client = AnthropicFoundry(
    azure_ad_token_provider=token_provider,
    base_url="https://<resource-name>.services.ai.azure.com/anthropic"
)

message = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Hello from Entra ID auth!"}]
)
print(message.content[0].text)
```

### REST Example

Obtain a token with the Azure CLI, then pass it as a Bearer token:

```bash
# Get an access token
TOKEN=$(az account get-access-token \
  --resource "https://ai.azure.com" \
  --query accessToken -o tsv)

# Call the API
curl -X POST "https://<resource-name>.services.ai.azure.com/anthropic/v1/messages" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "claude-sonnet-4-6",
    "max_tokens": 256,
    "messages": [{"role": "user", "content": "Hello!"}]
  }'
```

### Claude Code with Entra ID

When using Claude Code in VS Code, Entra ID authentication is automatic if you are signed in with the Azure CLI. Claude Code detects your Azure CLI session — no API key configuration needed. See [VS Code Setup](vscode-setup.md) for details.

## Comparison

| | API Key | Entra ID |
|---|---|---|
| **Setup complexity** | Low | Medium |
| **Security** | Static secret to manage | Short-lived tokens, no secrets |
| **RBAC support** | No | Yes — per-user access control |
| **Audit logging** | Limited | Full Azure AD audit trail |
| **Rotation** | Manual key rotation | Automatic token refresh |
| **Recommended for** | Local dev, quick testing | Production, team environments |

## Next Steps

- [VS Code Setup](vscode-setup.md) — Configure Claude Code with your chosen auth method
- [API Access](api-access.md) — SDK examples for Python, TypeScript, C#, and REST
