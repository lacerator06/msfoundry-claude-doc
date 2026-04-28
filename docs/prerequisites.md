# Prerequisites

Before deploying Claude models on Microsoft Foundry, verify that your Azure environment meets these requirements.

## Azure Subscription

You need a **paid Azure subscription** with an active pay-as-you-go billing method.

The following subscription types are **not supported**:

- Student accounts
- Free trial subscriptions
- Startup credit-only accounts
- Sponsored subscriptions using only credits
- Enterprise accounts in South Korea
- Cloud Solution Provider (CSP) subscriptions

> [!IMPORTANT]
> Only **Enterprise Agreement (EA)** and **Microsoft Customer Agreement - Enterprise (MCA-E)** subscriptions receive default rate limits for Claude models. Other subscription types may default to 0 RPM / 0 TPM and require a quota increase request.

## Azure Marketplace Access

Claude models are offered through **Azure Marketplace** as a third-party service. Your subscription must have Marketplace purchasing enabled at the subscription or tenant level.

To verify:

1. Go to the [Azure portal](https://portal.azure.com)
2. Navigate to **Subscriptions** > your subscription > **Resource providers**
3. Ensure `Microsoft.Marketplace` is registered

If Marketplace purchasing is blocked by policy, Claude model deployments will fail.

## Role Requirements

You need one of the following roles on the resource group where your Foundry resource lives:

- **Contributor** (minimum)
- **Owner**

For Entra ID authentication, you also need the **Azure AI Developer** role (or legacy **Cognitive Services User** role) assigned on the Foundry resource.

## Supported Regions

Claude models are available in these Azure regions only:

| Region | Region Code |
|---|---|
| East US 2 | `eastus2` |
| Sweden Central | `swedencentral` |

Your Foundry resource and project must be created in one of these regions. 
**If you already created a Foundry resource in Norway East and deploy a Claude model, Azure Foundry will automatically create a Foundry resource in the supported region**


## Checklist

Before proceeding to [Azure Setup](azure-setup.md), confirm:

- [ ] Paid Azure subscription (EA or MCA-E recommended)
- [ ] Azure Marketplace purchasing enabled
- [ ] Contributor or Owner role on the target resource group
- [ ] Region selected: East US 2 or Sweden Central
