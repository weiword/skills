# Token Information Page Skill

## Purpose

Given a blockchain `chain` and `token_address`, create or update a GitHub Markdown page that gives a user a clear, sourced, and decision-useful information page for that token.

The page must present standard market data such as price, volume, liquidity, market capitalization, fully diluted valuation, supply, and price changes, plus additional context that helps the user understand liquidity quality, holder concentration, contract risk, links, and recent activity.

This skill does **not** provide financial advice. It produces an informational page only.

## Inputs

Required:

- `chain`: The blockchain/network identifier, for example `ethereum`, `base`, `solana`, `bsc`, `arbitrum`, `polygon`, or the chain identifier required by the selected data source.
- `token_address`: The token contract address or token mint address.
- `github_repo`: The target GitHub repository in `owner/repo` format.
- `github_page_path`: The Markdown file path to create or update, for example `tokens/base/0x1234...abcd.md`.

Optional:

- `branch`: Git branch to update. Default: repository default branch.
- `quote_currency`: Default: `USD`.
- `page_title`: Optional override for the Markdown title.
- `commit_message`: Optional override. Default: `Update token information page for {chain}:{token_address}`.
- `data_source_preferences`: Ordered list of preferred market data providers.
- `include_risk_section`: Default: `true`.
- `include_technical_section`: Default: `true`.
- `include_source_links`: Default: `true`.
- `max_pairs_to_analyze`: Default: `5`.

## Required Output

The final output of the skill is an updated GitHub Markdown page.

The skill must:

1. Retrieve token, market, liquidity, and metadata for the provided `chain` and `token_address`.
2. Generate a complete Markdown page using the structure below.
3. Create or update `github_page_path` in `github_repo`.
4. Commit the change to the requested branch.
5. Return a concise confirmation containing:
   - repository
   - branch
   - updated file path
   - commit SHA or pull request URL, depending on the integration used
   - any major data gaps or warnings

## Data Source Strategy

Use multiple sources when available. Prefer primary or widely used market-data APIs, and cross-check key figures when possible.

Recommended data sources:

1. **DEX Screener** for DEX pair data, price, liquidity, 24h volume, transactions, pair age, DEX venue, token links, boosted-token indicators, and paid order checks. DEX Screener exposes endpoints for token profiles, token boosts, paid orders, and pair lookups; its token listing docs note that tokens are listed automatically once added to a liquidity pool with at least one transaction.
2. **CoinGecko** for market data, metadata, contract-address token lookup, market cap, FDV, circulating supply, categories, and centralized market context when available.
3. **DefiLlama / Llama APIs** for price cross-checks, protocol/TVL context, and DeFi ecosystem data where relevant.
4. **Chain explorer APIs** such as Etherscan, Basescan, Arbiscan, BscScan, Polygonscan, Solscan, or equivalent for contract verification, decimals, supply, deployer, holders when supported, and transaction links.
5. **On-chain RPC** as a fallback for token decimals, total supply, name, symbol, and contract existence.

### Source Selection Rules

- Resolve the token by exact chain and exact address first. Never rely only on token symbol or name.
- Prefer USD-denominated metrics when available.
- For DEX tokens, select the primary pair by liquidity unless a stronger signal exists, such as materially higher volume and credible liquidity.
- Analyze up to `max_pairs_to_analyze` pairs and summarize the most relevant pair.
- If data differs materially across sources, show the range or identify the preferred source and explain why.
- If market cap is unavailable, estimate it only when both price and circulating supply are available. Clearly label estimates.
- If circulating supply is unavailable, do not fabricate market cap. Use FDV where total supply is available and label it correctly.

## Data to Collect

### Identity and Metadata

- Token name
- Token symbol
- Chain
- Token address
- Decimals
- Token standard, for example ERC-20, SPL, BEP-20
- Official website
- Documentation or whitepaper link
- Social links, such as X/Twitter, Telegram, Discord, GitHub
- Token image or logo, if available
- Explorer link
- DEX Screener / CoinGecko / other market page links

### Market Data

- Current price in `quote_currency`
- 1h, 6h, 24h, and 7d price change where available
- 24h trading volume
- Liquidity
- Market cap
- Fully diluted valuation
- Circulating supply
- Total supply
- Max supply, if available
- All-time high and all-time low, if available
- Number of listed pairs or markets
- Primary exchange or DEX venue

### Liquidity and Trading Activity

- Primary pair address
- Base token / quote token
- DEX or venue name
- Pair creation date or age
- Buy/sell transaction counts over available windows
- Volume over available windows, typically 5m, 1h, 6h, 24h
- Liquidity depth and quote-token composition when available
- Price impact warning if liquidity is low
- Whether the token has boosted/promoted status or paid orders, if available

### Holder and Supply Analysis

When explorer or indexer data is available, include:

- Holder count
- Top holder concentration
- Top 10 holder percentage
- Burn address balance
- Known team/treasury/vesting wallets, if identifiable from labels or official docs
- Contract owner or authority status
- Mint authority and freeze authority for Solana SPL tokens, where applicable

Do not guess wallet identities. Label wallet identity as unknown unless verified by explorer labels, official docs, or credible public sources.

### Contract and Risk Signals

Include a risk section with neutral, evidence-based observations:

- Contract verification status
- Proxy or upgradeable contract status
- Owner/admin privileges
- Minting capability
- Pausable/blacklist/tax/transfer-restriction functions, if detected
- Honeypot or sellability checks when available from a reputable source
- Liquidity lock or burn status, if available
- Token age
- Low-liquidity warning
- Extreme holder concentration warning
- Data-source mismatch warning

Avoid definitive claims such as “safe” or “scam” unless directly supported by reliable sources. Use language such as “observed risk signal” or “not found in available data.”

### Optional Technical Indicators

If historical OHLCV data is available, include:

- 7d and 30d price trend
- 24h high/low
- Simple moving averages, such as 7d and 30d
- Volatility summary
- Relative volume compared with recent average

If historical data is unavailable, omit this section or state that historical data was not available from the selected sources.

## Markdown Page Structure

Create or update the GitHub Markdown page using this structure:

```markdown
---
title: "{token_name} ({token_symbol}) Token Information"
chain: "{chain}"
token_address: "{token_address}"
last_updated: "{iso_timestamp_utc}"
data_sources:
  - "{source_name}: {source_url}"
---

# {token_name} ({token_symbol})

> Informational token profile for `{chain}:{token_address}`. This page is not financial advice.

## Summary

| Field | Value |
|---|---:|
| Chain | {chain} |
| Token Address | `{token_address}` |
| Price | {price} |
| 24h Volume | {volume_24h} |
| Liquidity | {liquidity} |
| Market Cap | {market_cap} |
| FDV | {fdv} |
| Circulating Supply | {circulating_supply} |
| Total Supply | {total_supply} |
| Primary Pair | {primary_pair} |
| Last Updated | {iso_timestamp_utc} |

## Market Performance

| Metric | Value |
|---|---:|
| Price Change 1h | {price_change_1h} |
| Price Change 6h | {price_change_6h} |
| Price Change 24h | {price_change_24h} |
| Price Change 7d | {price_change_7d} |
| 24h High | {high_24h} |
| 24h Low | {low_24h} |
| All-Time High | {ath} |
| All-Time Low | {atl} |

## Liquidity and Trading

| Metric | Value |
|---|---:|
| Primary Venue | {primary_venue} |
| Pair Address | `{pair_address}` |
| Pair Age | {pair_age} |
| 5m Volume | {volume_5m} |
| 1h Volume | {volume_1h} |
| 6h Volume | {volume_6h} |
| 24h Volume | {volume_24h} |
| 24h Buys | {buys_24h} |
| 24h Sells | {sells_24h} |

## Supply and Holders

| Metric | Value |
|---|---:|
| Decimals | {decimals} |
| Holder Count | {holder_count} |
| Top Holder Concentration | {top_holder_pct} |
| Top 10 Holder Concentration | {top_10_holder_pct} |
| Burned Supply | {burned_supply} |

## Contract and Risk Signals

| Signal | Status | Notes |
|---|---|---|
| Contract Verified | {verified_status} | {verified_notes} |
| Upgradeable / Proxy | {proxy_status} | {proxy_notes} |
| Owner/Admin Privileges | {owner_status} | {owner_notes} |
| Mint Capability | {mint_status} | {mint_notes} |
| Transfer Restrictions | {transfer_restriction_status} | {transfer_restriction_notes} |
| Liquidity Lock/Burn | {liquidity_lock_status} | {liquidity_lock_notes} |
| Data Quality | {data_quality_status} | {data_quality_notes} |

## Links

- Website: {website_url}
- Explorer: {explorer_url}
- Market Page: {market_url}
- Documentation: {docs_url}
- Social: {social_links}

## Source Notes

Data was collected from the sources listed in the frontmatter. Figures may change rapidly, especially for newly launched or low-liquidity tokens.
```

## Formatting Requirements

- Use GitHub-flavored Markdown.
- Include YAML frontmatter.
- Format large numbers compactly, for example `$1.24M`, but keep raw values available in source notes when useful.
- Use `N/A` only when a field is unavailable after checking appropriate sources.
- Use backticks for addresses.
- Use absolute URLs.
- Include UTC timestamp in ISO 8601 format.
- Do not include unsourced claims.
- Do not include investment recommendations such as “buy,” “sell,” “hold,” or price targets.

## GitHub Update Workflow

1. Authenticate to GitHub using the available GitHub token or configured integration.
2. Check whether `github_page_path` already exists on the target branch.
3. If the file exists:
   - Read the current content.
   - Replace the full page with the newly generated Markdown, unless preserving custom sections is explicitly requested.
4. If the file does not exist:
   - Create the file and any needed directory path.
5. Commit the change with `commit_message`.
6. Return the commit SHA or PR URL.

### Commit Message Default

```text
Update token information page for {chain}:{token_address}
```

### Pull Request Mode

If direct commits to the target branch are not allowed:

1. Create a new branch named `token-info/{chain}-{short_token_address}-{yyyyMMddHHmm}`.
2. Commit the Markdown update to that branch.
3. Open a pull request into the target branch.
4. Return the PR URL.

## Validation Rules

Before writing to GitHub:

- Validate that `chain` is supported by at least one configured source.
- Validate that `token_address` matches the expected address format for the chain where possible.
- Verify that the token exists on-chain or in at least one reputable market-data source.
- Confirm the token symbol/name from address-based lookup, not from user-provided text.
- Ensure all required Markdown sections are present.
- Ensure the page contains `last_updated` in UTC.
- Ensure the page contains a disclaimer that it is not financial advice.

## Error Handling

If the token cannot be found:

- Do not create a misleading page.
- Return a message explaining which sources were checked.
- Optionally create a minimal “not found” page only if explicitly requested.

If some data is missing:

- Still update the page if identity and price or liquidity data are available.
- Mark missing fields as `N/A`.
- Add a `Data Quality` note describing the missing fields.

If GitHub update fails:

- Return the generated Markdown content and the error message.
- Do not claim the GitHub page was updated.

## Data Quality Labels

Use one of these labels in the page:

- `High`: Token identity, price, liquidity, volume, supply, and at least two independent source links are available.
- `Medium`: Token identity, price, and liquidity are available, but supply or holder data is incomplete.
- `Low`: Token identity is available, but market, liquidity, or supply data is sparse.
- `Insufficient`: Token cannot be confidently resolved by chain and address.

## Safety and Compliance

- Do not provide financial advice.
- Do not encourage trading.
- Do not make guarantees about safety, legitimacy, or future performance.
- Clearly distinguish observed data from interpretation.
- Treat sponsored, boosted, or paid placement indicators as disclosure signals, not as quality signals.
- Prefer cautious language for risk findings.
