# Wallet Address Information Page Skill

## Purpose

Given a blockchain `chain` and `wallet_address`, create or update a GitHub Markdown page that gives users a clear, factual, and useful overview of the wallet. The page should summarize wallet identity signals, portfolio composition, transaction activity, DeFi/NFT usage, risk indicators, and notable behavioral patterns.

The final output of this skill is an updated GitHub Markdown page, not a standalone report.

## Inputs

Required:

- `chain`: The blockchain network where the wallet exists, such as Ethereum, Base, Arbitrum, Optimism, Polygon, BNB Chain, Solana, Avalanche, or another supported chain.
- `wallet_address`: The public wallet address to analyze.
- `github_page_path`: The repository path of the Markdown page to update.

Optional:

- `github_repo`: Repository owner and name, if not already configured.
- `branch`: Target branch for the Markdown update. Default to the repository default branch.
- `analysis_window`: Time period for activity analysis, such as 7d, 30d, 90d, 1y, or all-time. Default to 90d when data is available.
- `display_currency`: Default to USD.
- `audience`: Intended reader, such as retail user, analyst, compliance reviewer, product user, or internal team.
- `known_label`: Any user-provided label or hypothesis for the wallet, such as treasury, whale, deployer, CEX hot wallet, DAO, market maker, or personal wallet.

## Core Requirements

The skill must produce a GitHub-compatible Markdown page that is easy to scan, source-aware, and clear about uncertainty.

The page should not imply identity ownership unless it is backed by a reliable label source or explicitly provided by the user. Public blockchain data can show wallet behavior, but it does not prove who controls the wallet.

Do not present the page as financial, legal, tax, or compliance advice. Use neutral language and cite data sources where possible.

## Workflow

### 1. Validate Inputs

- Confirm the `chain` is supported by the selected data providers.
- Validate the `wallet_address` format for the specified chain.
- Normalize address casing where appropriate.
- For EVM chains, preserve checksum address when available.
- Reject obviously invalid addresses with a short explanation.
- If the chain and address format conflict, stop and ask for corrected inputs.

### 2. Collect Wallet Overview Data

Gather the following where available:

- Native token balance.
- ERC-20, SPL, or equivalent token balances.
- Estimated total portfolio value.
- Stablecoin balance and share of portfolio.
- NFT holdings count and notable collections.
- First seen date.
- Last active date.
- Total transaction count.
- Number of active days.
- Wallet age.
- Known labels from trusted providers.
- ENS, Basename, Lens, Farcaster, Solana name service, or other naming records if applicable.
- Contract-vs-EOA status where applicable.
- Smart contract wallet indicators, such as Safe, Argent, or account abstraction wallet patterns.

### 3. Collect Portfolio and Financial Data

Include standard wallet financial data where available:

- Total estimated wallet value.
- Native asset value.
- Token balances and values.
- Top holdings by value.
- Token concentration.
- Stablecoin exposure.
- Illiquid, suspicious, or unpriced tokens.
- NFT estimated value, if reliable.
- DeFi positions, such as lending, borrowing, LP positions, staking, farming, bridges, and vaults.
- Open debt or liquidation-sensitive positions, if visible and applicable.
- Unrealized PnL only when the source methodology is clear.
- Realized PnL only when calculated from a reliable transaction-cost basis model or provider.

Avoid overstating valuation precision. Use phrases like “estimated,” “reported by,” or “not available” when data is incomplete.

### 4. Collect Activity Data

Analyze transaction behavior over the requested analysis window and, where useful, all-time:

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

### 5. Identify Counterparties and Protocols

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

Clearly distinguish between labeled entities and raw addresses.

### 6. Add Risk and Safety Indicators

Include a balanced risk section. Possible indicators:

- Interactions with sanctioned, hacked, phishing, mixer, scam, or high-risk addresses, if supported by reputable sources.
- Exposure to suspicious or unverified tokens.
- Approvals granted to contracts.
- Large unlimited token approvals.
- Recent approval changes.
- Dormant-wallet reactivation.
- Rapid inflow/outflow behavior.
- High concentration in one token.
- Use of privacy tools or mixers, where label data is reliable.
- Connections to known exploit addresses, where supported by reliable source data.

Use careful wording. For example, say “has interacted with an address labeled as high risk by [source]” rather than “is malicious.”

### 7. Add Behavioral Insights

Provide concise, evidence-based observations such as:

- “Wallet appears primarily used for DeFi trading.”
- “Wallet has long-term holding behavior with limited recent activity.”
- “Wallet shows frequent bridge usage across L2 networks.”
- “Portfolio is highly concentrated in one asset.”
- “Wallet activity is consistent with a treasury or multisig, if supported by transaction patterns and labels.”

Avoid speculation about the real-world owner.

### 8. Build the GitHub Markdown Page

The Markdown page should use the following structure unless the existing page has a different established template.

```markdown
---
title: Wallet Address Information
chain: <chain>
wallet_address: <wallet_address>
last_updated: <ISO-8601 timestamp>
data_sources:
  - <source name>
---

# Wallet Address Information: `<wallet_address>`

> Chain: **<chain>**  
> Last updated: **<human-readable date/time>**  
> Data currency: **<display_currency>**

## Summary

<Plain-language overview of the wallet, its estimated value, activity level, labels, and notable observations.>

## Key Metrics

| Metric | Value |
|---|---:|
| Estimated portfolio value | <value> |
| Native token balance | <value> |
| Stablecoin balance | <value> |
| Token holdings | <count> |
| NFT holdings | <count> |
| First seen | <date> |
| Last active | <date> |
| Total transactions | <count> |
| Transactions in analysis window | <count> |
| Known labels | <labels or None found> |

## Portfolio Breakdown

| Asset | Balance | Estimated Value | Portfolio Share | Notes |
|---|---:|---:|---:|---|
| <asset> | <balance> | <value> | <percent> | <notes> |

## DeFi Positions

| Protocol | Position Type | Asset(s) | Estimated Value | Risk Notes |
|---|---|---|---:|---|
| <protocol> | <type> | <assets> | <value> | <notes> |

## NFT Holdings

| Collection | Count | Estimated Value | Notes |
|---|---:|---:|---|
| <collection> | <count> | <value> | <notes> |

## Activity Overview

| Period | Transactions | Inflow | Outflow | Fees Paid | Notes |
|---|---:|---:|---:|---:|---|
| <period> | <count> | <value> | <value> | <value> | <notes> |

## Recent Transactions

| Date | Type | Asset | Amount | Counterparty | Transaction |
|---|---|---:|---:|---|---|
| <date> | <type> | <asset> | <amount> | <counterparty> | [View](<explorer_url>) |

## Top Counterparties and Protocols

| Entity / Address | Label | Interaction Count | Estimated Value Moved | Notes |
|---|---|---:|---:|---|
| <entity> | <label> | <count> | <value> | <notes> |

## Approvals and Permissions

| Spender | Asset | Allowance | Last Updated | Risk Note |
|---|---|---:|---|---|
| <spender> | <asset> | <allowance> | <date> | <note> |

## Risk and Safety Notes

<Neutral risk summary. Include source-backed warnings only.>

## Observations

- <Evidence-based observation>
- <Evidence-based observation>
- <Evidence-based observation>

## Data Limitations

- Portfolio values are estimates and may exclude illiquid, unpriced, bridged, staked, or protocol-held assets.
- Labels are sourced from third-party providers and may be incomplete or outdated.
- Public wallet activity does not prove real-world identity or intent.
- Some chains, protocols, and NFTs may have incomplete indexing coverage.

## Sources

| Source | Used For | Retrieved At |
|---|---|---|
| <source> | <data type> | <timestamp> |
```

### 9. Update Existing GitHub Markdown Page

When an existing Markdown file is present:

- Preserve custom frontmatter fields unless replacing stale values is necessary.
- Preserve manually written notes outside clearly marked generated sections.
- Prefer generated-section markers to make future updates safe:

```markdown
<!-- WALLET_INFO_START -->
<generated content>
<!-- WALLET_INFO_END -->
```

If the markers exist, replace only the content between them. If they do not exist, append a clearly marked generated section or update the whole file only when explicitly instructed.

### 10. Commit or Return the Update

Depending on available permissions and user instructions:

- Update the GitHub Markdown page directly; or
- Create a branch and pull request; or
- Return the complete Markdown content and a clear patch/diff for manual application.

The final response should state:

- Which GitHub Markdown page was updated.
- The chain and wallet address analyzed.
- The data timestamp.
- Any major missing data or limitations.

## Recommended Data Sources

Use the best available sources for the selected chain. Possible sources include:

- Chain explorers such as Etherscan, Basescan, Arbiscan, Optimistic Etherscan, Polygonscan, BscScan, SnowTrace, Solscan, or equivalent.
- Portfolio APIs such as Zerion, Zapper, DeBank, Covalent, Moralis, Alchemy, QuickNode, or SimpleHash.
- Price APIs such as CoinGecko, CoinMarketCap, DefiLlama, DexScreener, or trusted exchange/index sources.
- DeFi protocol data from DefiLlama, protocol subgraphs, or official protocol APIs.
- NFT data from OpenSea, Reservoir, SimpleHash, Magic Eden, or chain-specific NFT APIs.
- Risk and labeling data from TRM, Chainalysis, Elliptic, Arkham, Nansen, Etherscan labels, public exploit databases, or reputable community-maintained sources, where available.

Prefer official, transparent, or widely used sources. Record every source in the Markdown page.

## Data Quality Rules

- Cross-check total portfolio value against holdings where possible.
- Do not include spam tokens in top holdings unless they materially affect user understanding.
- Mark suspicious, illiquid, or unpriced tokens clearly.
- Treat NFT floor prices as estimates, not realizable value.
- Do not calculate PnL unless transaction history and pricing data are sufficient.
- Do not infer private identity from public wallet data.
- Use “unknown,” “not available,” or “not found” instead of guessing.
- Include retrieval timestamps for volatile data.

## Formatting Rules

- Use GitHub-flavored Markdown.
- Use tables for metrics and holdings.
- Use short paragraphs and concise bullets.
- Format addresses in backticks.
- Link transactions, tokens, NFTs, and counterparties to the relevant block explorer where possible.
- Keep the summary understandable for non-technical readers.
- Use consistent currency formatting.
- Use UTC timestamps unless the user requests another timezone.

## Error Handling

If the wallet cannot be analyzed:

- Explain whether the issue is invalid input, unsupported chain, unavailable data provider, rate limit, or empty wallet history.
- Still update the GitHub Markdown page with a short status section if instructed.
- Include enough diagnostic detail for the user to fix the issue.

## Security and Privacy

- Analyze only public blockchain data and user-provided context.
- Do not attempt to deanonymize the wallet owner.
- Do not expose private API keys or internal provider credentials in the Markdown page.
- Do not include sensitive internal notes in the generated page.
- Do not claim criminal, sanctioned, or malicious behavior unless a reputable source explicitly labels the address or counterparty that way.

## Final Output Contract

The final output of this skill is an updated GitHub Markdown page for the provided wallet address.

The generated page must include:

- Wallet summary.
- Key metrics.
- Portfolio breakdown.
- Activity overview.
- Recent transactions.
- Counterparties and protocols.
- Approvals and permissions where available.
- Risk and safety notes.
- Data limitations.
- Source list with timestamps.

The assistant’s final message to the user should be brief and should link or identify the updated GitHub Markdown page.
