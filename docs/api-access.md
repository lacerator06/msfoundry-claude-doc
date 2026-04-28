# API Access

Call Claude models on Microsoft Foundry from your applications using Anthropic's SDKs or direct REST calls.

## Endpoint URL Format

All Claude API requests go through your Foundry resource endpoint:

```
Base URL:     https://<resource-name>.services.ai.azure.com/anthropic
Messages API: https://<resource-name>.services.ai.azure.com/anthropic/v1/messages
```

Replace `<resource-name>` with your Foundry resource name. Find it in the Foundry portal: project name (top-left) > **Project details** > **Parent resource**.

## SDK Installation

| Language | Package | Install Command |
|---|---|---|
| Python | `anthropic` | `pip install -U anthropic` |
| TypeScript | `@anthropic-ai/foundry-sdk` | `npm install @anthropic-ai/foundry-sdk` |
| C# | `Anthropic.Foundry` | `dotnet add package Anthropic.Foundry` |
| Java | `anthropic-java-foundry` | Add `com.anthropic:anthropic-java-foundry` to your build |

All SDKs provide an `AnthropicFoundry` client class that handles Foundry-specific authentication and endpoint routing.

## Python

### API Key Authentication

```python
from anthropic import AnthropicFoundry

client = AnthropicFoundry(
    api_key="your-api-key",
    base_url="https://<resource-name>.services.ai.azure.com/anthropic"
)

message = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    messages=[
        {"role": "user", "content": "Explain Azure resource groups in two sentences."}
    ]
)

print(message.content[0].text)
```

### Entra ID Authentication

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
    messages=[
        {"role": "user", "content": "Hello from Entra ID!"}
    ]
)

print(message.content[0].text)
```

### Using Resource Name Instead of Base URL

The `AnthropicFoundry` client accepts either `base_url` or `resource` (just the resource name). These are **mutually exclusive**:

```python
# Using resource name (constructs the URL automatically)
client = AnthropicFoundry(
    api_key="your-api-key",
    resource="your-resource-name"
)

# Using full base URL
client = AnthropicFoundry(
    api_key="your-api-key",
    base_url="https://your-resource-name.services.ai.azure.com/anthropic"
)
```

## TypeScript

```typescript
import { AnthropicFoundry } from "@anthropic-ai/foundry-sdk";

const client = new AnthropicFoundry({
  apiKey: process.env.ANTHROPIC_FOUNDRY_API_KEY,
  baseURL: `https://${process.env.ANTHROPIC_FOUNDRY_RESOURCE}.services.ai.azure.com/anthropic`,
});

const message = await client.messages.create({
  model: "claude-sonnet-4-6",
  max_tokens: 1024,
  messages: [
    { role: "user", content: "Hello from TypeScript!" },
  ],
});

console.log(message.content[0].text);
```

## C#

```csharp
using Anthropic.Foundry;

var client = new AnthropicFoundryClient(
    apiKey: Environment.GetEnvironmentVariable("ANTHROPIC_FOUNDRY_API_KEY"),
    baseUrl: $"https://{Environment.GetEnvironmentVariable("ANTHROPIC_FOUNDRY_RESOURCE")}.services.ai.azure.com/anthropic"
);

var message = await client.Messages.CreateAsync(new()
{
    Model = "claude-sonnet-4-6",
    MaxTokens = 1024,
    Messages = [
        new() { Role = "user", Content = "Hello from C#!" }
    ]
});

Console.WriteLine(message.Content[0].Text);
```

## REST (cURL)

### With API Key

```bash
curl -X POST "https://<resource-name>.services.ai.azure.com/anthropic/v1/messages" \
  -H "Content-Type: application/json" \
  -H "x-api-key: $ANTHROPIC_FOUNDRY_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "claude-sonnet-4-6",
    "max_tokens": 1024,
    "messages": [
      {"role": "user", "content": "Hello!"}
    ]
  }'
```

### With Entra ID Token

```bash
TOKEN=$(az account get-access-token \
  --resource "https://ai.azure.com" \
  --query accessToken -o tsv)

curl -X POST "https://<resource-name>.services.ai.azure.com/anthropic/v1/messages" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "claude-sonnet-4-6",
    "max_tokens": 1024,
    "messages": [
      {"role": "user", "content": "Hello!"}
    ]
  }'
```

## Advanced Features

### Adaptive Thinking

Enable Claude's extended thinking for complex tasks:

```python
message = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=8192,
    messages=[
        {"role": "user", "content": "Design a microservices architecture for an e-commerce platform."}
    ],
    thinking={"type": "adaptive"},
    output_config={"effort": "max"}
)
```

### Streaming

```python
with client.messages.stream(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    messages=[
        {"role": "user", "content": "Write a Python function to parse CSV files."}
    ]
) as stream:
    for text in stream.text_stream:
        print(text, end="", flush=True)
```

### Prompt Caching

Prompt caching is **enabled automatically** on Microsoft Foundry. Repeated prefixes in your messages are cached and reused, reducing latency and cost for subsequent requests.

For an extended 1-hour cache TTL (instead of the default 5-minute TTL):

```bash
export ENABLE_PROMPT_CACHING_1H=1
```

## Foundry-Specific Limitations

- **Unsupported APIs**: Admin API, Models API, and Message Batch API are not available on Foundry
- **Rate limit headers**: Foundry does not return Anthropic's standard `x-ratelimit-*` headers; use Azure Monitor for usage metrics
- **Model Router**: Currently supports `claude-haiku-4-5`, `claude-opus-4-1`, and `claude-sonnet-4-5` only (not Sonnet 4.6 or Opus 4.6+)

## Next Steps

- [Authentication](authentication.md) — Detailed setup for API key and Entra ID
- [VS Code Setup](vscode-setup.md) — Configure Claude Code for development workflows
