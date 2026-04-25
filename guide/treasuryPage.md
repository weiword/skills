# Multi-Address Cross-Chain Portfolio Summary Page Skill

## Purpose

Given a list of public wallet addresses across one or more blockchain networks, create or update a GitHub Markdown page that gives users a clear, factual, and useful total portfolio summary. The page should aggregate holdings, DeFi positions, NFTs, activity, counterparties, approvals, risk indicators, and source-backed observations across all supplied addresses and chains.

The final output of this skill is an updated GitHub Markdown page, not a standalone report.

## Inputs

### Required

- `addresses`: A list of wallet entries to analyze. Each entry must include:
  - `chain`: The blockchain network where the address exists, such as Ethereum, Base, Arbitrum, Optimism, Polygon, BNB Chain, Solana, Avalanche, or another supported chain.
  - `wallet_address`: The public wallet address to analyze.
- `github_page_path`: The repository path of the Markdown page to update.

### Optional

- `github_repo`: Repository owner and name, if not already configured.
- `branch`: Target branch for the Markdown update. Default to the repository default branch.
- `portfolio_name`: Human-readable name for the grouped portfolio, such as “Treasury Wallets,” “User Portfolio,” or “DAO Multisig Portfolio.”
- `analysis_window`: Time period for activity analysis, such as `7d`, `30d`, `90d`, `1y`, or `all-time`. Default to `90d` when data is available.
- `display_currency`: Default to `USD`.
- `audience`: Intended reader, such as retail user, analyst, compliance reviewer, product user, or internal team.
- `known_labels`: User-provided labels or hypotheses for specific addresses, such as treasury, whale, deployer, CEX hot wallet, DAO, market maker, or personal wallet.
- `dedupe_same_owner`: Whether to treat identical addresses on different EVM chains as one cross-chain address group. Default to `true` for EVM chains.
- `include_zero_value_addresses`: Whether to include addresses with no indexed value. Default to `true` so coverage gaps are visible.

## Core Requirements

The skill must produce a GitHub-compatible Markdown page that is easy to scan, source-aware, and clear about uncertainty.

The page must aggregate the portfolio across all provided address-chain pairs and also show per-chain and per-address detail. It should not imply that all addresses share the same owner unless the grouping was explicitly provided by the user or supported by reliable labels. Public blockchain data can show wallet behavior, but it does not prove who controls the addresses.

Do not present the page as financial, legal, tax, investment, or compliance advice. Use neutral language and cite data sources where possible.

## Workflow

### 1. Validate Inputs

- Confirm every `chain` is supported by the selected data providers.
- Validate each `wallet_address` format for its specified chain.
- Normalize address casing where appropriate.
- For EVM chains, preserve checksum address when available.
- Detect duplicate address-chain pairs and deduplicate them before analysis.
- When the same EVM address appears on multiple EVM chains, group it as a cross-chain address when `dedupe_same_owner` is enabled, but keep chain-level balances separate.
- Reject obviously invalid addresses with a short explanation.
- If any chain and address format conflict, stop for those entries and continue with valid entries only when the user has requested best-effort processing. Otherwise, ask for corrected inputs.

### 2. Collect Address-Level Overview Data

For each address-chain pair, gather the following where available:

- Native token balance.
- Fungible token balances, such as ERC-20, SPL, or equivalent tokens.
- Estimated address-chain portfolio value.
- Stablecoin balance and share of the address-chain portfolio.
- NFT holdings count and notable collections.
- DeFi positions and protocol-held assets.
- First seen date.
- Last active date.
- Total transaction count.
- Number of active days.
- Wallet age.
- Known labels from trusted providers.
- ENS, Basename, Lens, Farcaster, Solana name service, or other naming records if applicable.
- Contract-vs-EOA status where applicable.
- Smart contract wallet indicators, such as Safe, Argent, or account abstraction wallet patterns.

### 3. Aggregate Portfolio and Financial Data

Aggregate across all valid address-chain pairs:

- Total estimated portfolio value.
- Total native asset value by chain and in aggregate.
- Total fungible token value by asset and chain.
- Total stablecoin value and share of the total portfolio.
- Total NFT count and estimated NFT value, if reliable.
- Total DeFi position value by protocol, chain, position type, and asset.
- Top holdings by value across all addresses and chains.
- Token concentration across the total portfolio.
- Chain allocation by estimated value.
- Address allocation by estimated value.
- Protocol allocation by estimated value.
- Illiquid, suspicious, spam, or unpriced tokens.
- Open debt or liquidation-sensitive positions, if visible and applicable.
- Unrealized PnL only when the source methodology is clear.
- Realized PnL only when calculated from a reliable transaction-cost basis model or provider.

Avoid overstating valuation precision. Use phrases like “estimated,” “reported by,” or “not available” when data is incomplete. Do not double-count assets held inside DeFi positions and also reported as wallet balances unless the provider clearly separates wallet-held and protocol-held assets.

### 4. Reconcile and Deduplicate Data

- Reconcile totals from portfolio APIs against itemized holdings where possible.
- Avoid double-counting bridged representations of the same asset unless they are distinct balances on distinct chains.
- Keep wrapped, bridged, and canonical tokens separate unless the data source explicitly normalizes them.
- Mark assets with stale, missing, or unreliable prices.
- Exclude obvious spam tokens from top holdings unless they materially affect user understanding.
- Treat NFT floor prices as estimates, not realizable value.
- Include retrieval timestamps for volatile data.

### 5. Collect Activity Data

Analyze activity for each address-chain pair over the requested `analysis_window` and, where useful, all-time:

- Transaction count.
- Inbound and outbound transfer counts.
- Total inflow and outflow value.
- Average transaction size.
- Largest inbound transfer.
- Largest outbound transfer.
- Most active day or period.
- Recent transactions.
- Most common interaction types.
- Gas spent or fees paid.
- Failed transactions.
- Bridge activity.
- DEX swaps.
- NFT mints, buys, sells, and transfers.
- Staking or unstaking activity.
- Lending and borrowing activity.
- Contract deployments, if any.

Then aggregate activity across the whole portfolio:

- Total transactions across all chains.
- Transactions by chain.
- Transactions by address.
- Total fees paid by chain and estimated display currency value.
- Recent cross-portfolio activity.
- Cross-chain bridge activity and likely bridge routes, where source data supports it.

### 6. Identify Counterparties and Protocols

Where data allows, summarize:

- Top counterparties by value.
- Top counterparties by transaction count.
- Known exchange interactions.
- Known protocol interactions.
- Bridge contracts used.
- DEXs used.
- NFT marketplaces used.
- Lending protocols used.
- Treasury, multisig, or DAO interactions.
- New counterparties in the analysis window.
- Counterparties or protocols shared across multiple addresses.

Clearly distinguish between labeled entities and raw addresses. Do not infer common ownership based only on repeated interactions.

### 7. Add Risk and Safety Indicators

Include a balanced risk section for the total portfolio and for individual addresses when relevant. Possible indicators:

- Interactions with sanctioned, hacked, phishing, mixer, scam, or high-risk addresses, if supported by reputable sources.
- Exposure to suspicious or unverified tokens.
- Approvals granted to contracts.
- Large or unlimited token approvals.
- Recent approval changes.
- Dormant-wallet reactivation.
- Rapid inflow/outflow behavior.
- High concentration in one token, address, chain, or protocol.
- Use of privacy tools or mixers, where label data is reliable.
- Connections to known exploit addresses, where supported by reliable source data.
- Liquidation risk from borrowing positions, where available.

Use careful wording. For example, say “has interacted with an address labeled as high risk by [source]” rather than “is malicious.”

### 8. Add Behavioral Insights

Provide concise, evidence-based observations such as:

- “Portfolio value is concentrated on Ethereum and Base.”
- “Stablecoin exposure represents a large share of the total portfolio.”
- “Activity is primarily DeFi trading across L2 networks.”
- “Several addresses interact with the same multisig or treasury contracts.”
- “Portfolio has long-term holding behavior with limited recent activity.”
- “Bridge usage suggests assets are actively moved between chains.”
- “One address accounts for most transaction activity.”

Avoid speculation about real-world owners or intent.

### 9. Build the GitHub Markdown Page

The Markdown page should use the following structure unless the existing page has a different established template.

```markdown
---
title: Cross-Chain Portfolio Summary
portfolio_name: <portfolio_name>
last_updated: <ISO-8601 timestamp>
display_currency: <display_currency>
address_count: <count>
chains:
  - <chain>
data_sources:
  - <source name>
---

# Cross-Chain Portfolio Summary: <portfolio_name>

> Last updated: **<human-readable date/time>**  
> Data currency: **<display_currency>**  
> Analysis window: **<analysis_window>**  
> Addresses analyzed: **<count>**  
> Chains covered: **<chain list>**

## Summary

<Plain-language overview of the aggregated portfolio, total estimated value, chain allocation, largest holdings, activity level, major labels, and notable observations.>

## Total Portfolio Metrics

| Metric | Value |
|---|---:|
| Estimated total portfolio value | <value> |
| Wallet-held assets | <value> |
| DeFi positions | <value> |
| NFT estimated value | <value> |
| Stablecoin balance | <value> |
| Stablecoin share | <percent> |
| Chains covered | <count> |
| Addresses analyzed | <count> |
| Fungible token holdings | <count> |
| NFT holdings | <count> |
| Total transactions | <count> |
| Transactions in analysis window | <count> |
| First seen | <earliest date> |
| Last active | <latest date> |
| Known labels | <labels or None found> |

## Addresses Analyzed

| Chain | Address | Label / Name | Type | Estimated Value | First Seen | Last Active | Notes |
|---|---|---|---|---:|---|---|---|
| <chain> | `<address>` | <label> | <EOA / contract / Safe / unknown> | <value> | <date> | <date> | <notes> |

## Allocation by Chain

| Chain | Estimated Value | Portfolio Share | Native Asset Value | DeFi Value | NFT Value | Notes |
|---|---:|---:|---:|---:|---:|---|
| <chain> | <value> | <percent> | <value> | <value> | <value> | <notes> |

## Allocation by Address

| Address | Chain(s) | Estimated Value | Portfolio Share | Main Assets / Protocols | Notes |
|---|---|---:|---:|---|---|
| `<address>` | <chains> | <value> | <percent> | <assets/protocols> | <notes> |

## Top Holdings Across Portfolio

| Asset | Chain(s) | Balance | Estimated Value | Portfolio Share | Held By | Notes |
|---|---|---:|---:|---:|---|---|
| <asset> | <chains> | <balance> | <value> | <percent> | <addresses> | <notes> |

## Stablecoin Exposure

| Stablecoin | Chain(s) | Balance | Estimated Value | Portfolio Share | Notes |
|---|---|---:|---:|---:|---|
| <stablecoin> | <chains> | <balance> | <value> | <percent> | <notes> |

## DeFi Positions

| Chain | Address | Protocol | Position Type | Asset(s) | Estimated Value | Debt / Risk Notes |
|---|---|---|---|---|---:|---|
| <chain> | `<address>` | <protocol> | <type> | <assets> | <value> | <notes> |

## NFT Holdings

| Chain | Address | Collection | Count | Estimated Value | Notes |
|---|---|---|---:|---:|---|
| <chain> | `<address>` | <collection> | <count> | <value> | <notes> |

## Activity Overview

| Scope | Transactions | Inflow | Outflow | Fees Paid | Most Active Period | Notes |
|---|---:|---:|---:|---:|---|---|
| Total portfolio | <count> | <value> | <value> | <value> | <period> | <notes> |
| <chain> | <count> | <value> | <value> | <value> | <period> | <notes> |
| `<address>` on <chain> | <count> | <value> | <value> | <value> | <period> | <notes> |

## Recent Transactions

| Date | Chain | Address | Type | Asset | Amount | Counterparty | Transaction |
|---|---|---|---|---|---:|---|---|
| <date> | <chain> | `<address>` | <type> | <asset> | <amount> | <counterparty> | [View](<explorer_url>) |

## Top Counterparties and Protocols

| Entity / Address | Label | Chain(s) | Interaction Count | Estimated Value Moved | Used By | Notes |
|---|---|---|---:|---:|---|---|
| <entity> | <label> | <chains> | <count> | <value> | <addresses> | <notes> |

## Approvals and Permissions

| Chain | Address | Spender | Asset | Allowance | Last Updated | Risk Note |
|---|---|---|---|---:|---|---|
| <chain> | `<address>` | <spender> | <asset> | <allowance> | <date> | <note> |

## Risk and Safety Notes

<Neutral portfolio-level risk summary. Include source-backed warnings only. Distinguish total-portfolio concentration risk from address-level or chain-level issues.>

## Observations

- <Evidence-based observation>
- <Evidence-based observation>
- <Evidence-based observation>

## Data Limitations

- Portfolio values are estimates and may exclude illiquid, unpriced, bridged, staked, vesting, escrowed, or protocol-held assets.
- Cross-chain aggregation may double-count assets if a provider reports wrapped, bridged, or protocol-held balances inconsistently.
- Labels are sourced from third-party providers and may be incomplete or outdated.
- Public wallet activity does not prove real-world identity, common ownership, or intent.
- Some chains, protocols, NFTs, and approvals may have incomplete indexing coverage.
- NFT floor prices are estimates and may not represent executable sale value.

## Sources

| Source | Used For | Retrieved At |
|---|---|---|
| <source> | <data type> | <timestamp> |
```

### 10. Update Existing GitHub Markdown Page

When an existing Markdown file is present:

- Preserve custom frontmatter fields unless replacing stale generated values is necessary.
- Preserve manually written notes outside clearly marked generated sections.
- Prefer generated-section markers to make future updates safe:

```markdown
<!-- PORTFOLIO_SUMMARY_START -->
<generated content>
<!-- PORTFOLIO_SUMMARY_END -->
```

If the markers exist, replace only the content between them. If they do not exist, append a clearly marked generated section or update the whole file only when explicitly instructed.

### 11. Commit or Return the Update

Depending on available permissions and user instructions:

- Update the GitHub Markdown page directly; or
- Create a branch and pull request; or
- Return the complete Markdown content and a clear patch/diff for manual application.

The final response should state:

- Which GitHub Markdown page was updated.
- The portfolio name, chains, and number of addresses analyzed.
- The data timestamp.
- Any major missing data or limitations.

## Recommended Data Sources

Use the best available sources for each selected chain. Possible sources include:

- Chain explorers such as Etherscan, Basescan, Arbiscan, Optimistic Etherscan, Polygonscan, BscScan, SnowTrace, Solscan, or equivalent.
- Portfolio APIs such as Zerion, Zapper, DeBank, Covalent, Moralis, Alchemy, QuickNode, or SimpleHash.
- Price APIs such as CoinGecko, CoinMarketCap, DefiLlama, DexScreener, or trusted exchange/index sources.
- DeFi protocol data from DefiLlama, protocol subgraphs, or official protocol APIs.
- NFT data from OpenSea, Reservoir, SimpleHash, Magic Eden, or chain-specific NFT APIs.
- Risk and labeling data from TRM, Chainalysis, Elliptic, Arkham, Nansen, Etherscan labels, public exploit databases, or reputable community-maintained sources, where available.

Prefer official, transparent, or widely used sources. Record every source in the Markdown page.

## Data Quality Rules

- Cross-check total portfolio value against holdings where possible.
- Separate wallet-held balances from protocol-held positions where possible.
- Do not double-count DeFi collateral, LP tokens, vault shares, or bridged assets.
- Do not include spam tokens in top holdings unless they materially affect user understanding.
- Mark suspicious, illiquid, stale-priced, or unpriced tokens clearly.
- Treat NFT floor prices as estimates, not realizable value.
- Do not calculate PnL unless transaction history and pricing data are sufficient.
- Do not infer private identity or common ownership from public wallet data.
- Use “unknown,” “not available,” or “not found” instead of guessing.
- Include retrieval timestamps for volatile data.
- Use a consistent methodology for currency conversion across chains.

## Formatting Rules

- Use GitHub-flavored Markdown.
- Use tables for metrics, addresses, allocations, holdings, activity, and sources.
- Use short paragraphs and concise bullets.
- Format addresses in backticks.
- Link transactions, tokens, NFTs, protocols, and counterparties to the relevant block explorer or official source where possible.
- Keep the summary understandable for non-technical readers.
- Use consistent currency formatting.
- Use UTC timestamps unless the user requests another timezone.
- For long address lists, show a summarized table and move lower-value or inactive entries to an appendix.

## Error Handling

If some addresses cannot be analyzed:

- Explain whether each issue is invalid input, unsupported chain, unavailable data provider, rate limit, empty wallet history, or missing index coverage.
- Continue with valid addresses when best-effort processing is appropriate.
- Include a “Coverage and Errors” section in the GitHub Markdown page.
- Include enough diagnostic detail for the user to fix the issue.

If no addresses can be analyzed:

- Do not fabricate portfolio data.
- Update the GitHub Markdown page with a short status section only if instructed.
- Return a clear explanation and the failed address-chain pairs.

## Security and Privacy

- Analyze only public blockchain data and user-provided context.
- Do not attempt to deanonymize wallet owners.
- Do not expose private API keys, provider credentials, internal notes, or secrets in the Markdown page.
- Do not claim criminal, sanctioned, or malicious behavior unless a reputable source explicitly labels the address or counterparty that way.
- Do not imply that grouped addresses have common ownership unless this was user-provided or source-backed.

## Final Output Contract

The final output of this skill is an updated GitHub Markdown page for the provided cross-chain, multi-address portfolio.

The generated page must include:

- Total portfolio summary.
- Total portfolio metrics.
- Addresses analyzed.
- Allocation by chain.
- Allocation by address.
- Top holdings across the portfolio.
- Stablecoin exposure.
- DeFi positions.
- NFT holdings.
- Activity overview.
- Recent transactions.
- Counterparties and protocols.
- Approvals and permissions where available.
- Risk and safety notes.
- Data limitations.
- Source list with timestamps.

The assistant’s final message to the user should be brief and should link or identify the updated GitHub Markdown page.
