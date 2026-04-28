# Troubleshooting

Common issues and solutions when using Claude on Microsoft Foundry.

## Deployment Failures

### "Marketplace purchase failed" or subscription not eligible

**Cause:** Azure Marketplace purchasing is disabled, or your subscription type is not supported.

**Fix:**
1. Verify Marketplace is enabled: Azure portal > **Subscriptions** > your subscription > **Resource providers** > ensure `Microsoft.Marketplace` is registered
2. Check your subscription type — free trials, student accounts, and CSP subscriptions are not supported
3. Contact your Azure administrator if Marketplace is blocked by organizational policy

### "Region not available" when deploying

**Cause:** Claude models are only available in East US 2 and Sweden Central.

**Fix:** Create a new Foundry resource in one of the supported regions. You cannot change the region of an existing resource.

## Authentication Errors

### 401 Unauthorized with API key

- Verify the API key matches the one shown in Foundry portal > **Models + endpoints** > your deployment > **Details**
- Confirm you are using the correct header: `x-api-key` or `api-key`
- Check that the key belongs to the same Foundry resource as your endpoint URL

### 403 Forbidden with Entra ID

- Verify your account has the **Azure AI User** or **Cognitive Services User** role on the Foundry resource
- Role assignments can take up to 5 minutes to propagate — wait and retry
- Ensure `az login` is using the correct tenant: run `az account show` to verify
- If using a different tenant: `az login --tenant <tenant-id>`

### Token expired errors

Entra ID tokens are short-lived (~1 hour). If you are using cached tokens:
- Re-run `az login` to refresh your session
- In code, use `DefaultAzureCredential` which handles token refresh automatically

## Rate Limits

### 0 RPM / 0 TPM — no requests allowed

**Cause:** Non-enterprise subscriptions (non-EA, non-MCA-E) default to zero rate limits.

**Fix:** File a quota increase request: Foundry resource > **Quotas** > **Request increase**. Include your use case in the justification.

### 429 Too Many Requests

**Cause:** You have exceeded your provisioned rate limit.

**Fix:**
- Reduce request frequency or batch smaller requests
- Use Azure Monitor to track your usage against limits
- Request a quota increase if your workload requires higher throughput

## Claude Code in VS Code

### Status shows wrong API provider

1. Verify `CLAUDE_CODE_USE_FOUNDRY=1` is set
2. Restart VS Code completely (not just reload window) — environment variables from shell profiles require a full restart
3. Run `/status` in Claude Code to confirm

### "Model not found" errors

- The model ID must match exactly (e.g., `claude-sonnet-4-6`, not `Claude Sonnet 4.6`)
- Verify the model is deployed in your Foundry project: **Models + endpoints** in the Foundry portal
- If using custom deployment names, set the corresponding `ANTHROPIC_DEFAULT_*_MODEL` environment variable

### Claude Code not detecting Entra ID

1. Run `az login` in a terminal **before** opening VS Code
2. Verify with `az account show` that you are logged into the correct subscription and tenant
3. Ensure the Azure CLI is on your system PATH

### Extension not loading

- Check VS Code version: Claude Code requires VS Code 1.85 or later
- Check the Output panel (`Ctrl+Shift+U`) > select **Claude Code** from the dropdown for error logs
- Try reinstalling the extension

## API and SDK Issues

### `base_url` and `resource` conflict

The `AnthropicFoundry` client accepts either `base_url` or `resource`, but **not both**. Using both raises an error.

```python
# Correct — use one or the other
client = AnthropicFoundry(api_key="...", resource="my-resource")
client = AnthropicFoundry(api_key="...", base_url="https://my-resource.services.ai.azure.com/anthropic")

# Wrong — do not use both
client = AnthropicFoundry(api_key="...", resource="my-resource", base_url="https://...")
```

### Missing rate limit headers

Foundry does not return Anthropic's standard `x-ratelimit-*` response headers. Use **Azure Monitor** to track usage:

1. Open your Foundry resource in the Azure portal
2. Go to **Monitoring** > **Metrics**
3. Select relevant metrics for token consumption and request counts

### Batch API not available

The Message Batch API is not supported on Microsoft Foundry. For batch processing, call the Messages API in a loop or use concurrent requests within your rate limits.

## Getting Help

- **Microsoft Foundry documentation**: [learn.microsoft.com/azure/foundry](https://learn.microsoft.com/azure/foundry)
- **Anthropic documentation**: [platform.claude.com/docs](https://platform.claude.com/docs)
- **Claude Code issues**: [github.com/anthropics/claude-code/issues](https://github.com/anthropics/claude-code/issues)
- **Azure support**: File a support request through the Azure portal for subscription and deployment issues
