
# Azure AI Foundry Networking Guide

Guidance for choosing between **Foundry Hub-based** and **standalone Project** architectures, and understanding the networking implications of each.

---

## Foundry Project (Standalone)

A **Foundry Project** is an independent workspace for building and operating an AI application.

Each project manages its own:

- Model deployments
- Data connections
- Networking configuration
- Identity and RBAC

**Best for:**

- Single-team AI applications
- Proof-of-concept or pilot workloads
- Teams that own their full infrastructure stack
- Scenarios where isolation between workloads is the priority

**Networking model:**

Each project controls its own private endpoints and network access. The team deploying the project is responsible for DNS, firewall rules, and connectivity.

---

## Foundry Hub (Shared Infrastructure)

A **Foundry Hub** provides centralized infrastructure that multiple projects share.

The hub manages:

- Network connectivity (Private Link, DNS)
- Shared model deployments
- Common data connections
- Governance policies

Projects deployed inside a hub inherit the hub's networking and security configuration.

**Best for:**

- Platform teams supporting multiple AI development teams
- Organizations requiring centralized network and security controls
- Environments where shared model deployments reduce cost and complexity
- Enterprises with existing Azure Landing Zone patterns

**Networking model:**

The platform team configures private endpoints, DNS zones, and firewall rules at the hub level. Projects inherit connectivity without needing to manage their own network resources.

---

## Decision Guide

| Factor | Standalone Project | Hub with Projects |
|---|---|---|
| Team autonomy | High — team owns everything | Moderate — inherits hub config |
| Networking control | Per-project | Centralized |
| Governance overhead | Higher per project | Lower — enforced at hub |
| Infrastructure reuse | None | Shared endpoints, models, connections |
| Setup complexity | Simpler for one project | Simpler at scale |
| Isolation | Full | Logical separation within hub |

**Rule of thumb:**

- **One team, one app** → Standalone Project
- **Multiple teams, shared platform** → Hub with Projects
- **Strict network governance required** → Hub with Projects

---

## Networking Essentials

Regardless of architecture model, enterprise deployments typically require:

### Private Link

Disable public access and use private endpoints for:

| Service | Purpose |
|---|---|
| Azure OpenAI | Model inference |
| Azure Storage | Prompts, artifacts, datasets |
| Azure AI Search | RAG knowledge base |
| Azure Key Vault | Secrets management |

### DNS

Private endpoints require private DNS zones:

- `privatelink.openai.azure.com`
- `privatelink.cognitiveservices.azure.com`
- `privatelink.blob.core.windows.net`
- `privatelink.vaultcore.azure.net`

> **Note:** DNS misconfiguration is the most common cause of connectivity failures in private endpoint deployments.

### Connectivity Pattern

```
On-premises / Corporate network
  → ExpressRoute or VPN
    → Hub VNet
      → Azure Firewall / NVA
        → Private Endpoints
          → Azure AI Foundry services
```

---

## Common Pitfalls

| Issue | Impact |
|---|---|
| Missing or incorrect private DNS zones | Services unreachable over private endpoints |
| Public access left enabled | Data exposure risk |
| Firewall not configured for AI service FQDNs | Outbound calls blocked |
| RBAC too broad at hub scope | Unintended cross-project access |
| No Dev/Test/Prod separation | Workload interference and governance gaps |

---

## License

MIT License
