# AllTheBot — 44 pay-per-call APIs for AI agents (x402, USDC on Base)

**No account, no API key.** Call an endpoint, get an HTTP `402` with the price, pay in USDC on Base with any
[x402](https://x402.org) client, get your JSON. Fractions of a cent per call.

- **Base URL:** `https://api.zkrobotics.fr`
- **Catalog (JSON):** https://api.zkrobotics.fr/ · **OpenAPI:** https://api.zkrobotics.fr/openapi.json · **x402 discovery:** https://api.zkrobotics.fr/.well-known/x402
- **LLM-friendly list:** https://zkrobotics.fr/llms.txt · Listed on the Coinbase **x402 Bazaar**
- **Network:** Base mainnet (`eip155:8453`), asset USDC, scheme `exact`, x402 v2

## Quick start

```bash
# Without payment: returns 402 + payment terms (price, payTo, asset)
curl -i -X POST https://api.zkrobotics.fr/x402/base/gas
```

Paying from Python with the official SDK (`pip install x402 eth-account requests`):

```python
from eth_account import Account
from x402 import x402ClientSync
from x402.http.clients import x402_requests
from x402.mechanisms.evm import EthAccountSigner
from x402.mechanisms.evm.exact.register import register_exact_evm_client

client = x402ClientSync()
register_exact_evm_client(client, EthAccountSigner(Account.from_key(YOUR_KEY)))  # wallet with a little USDC on Base
with x402_requests(client) as s:
    r = s.post("https://api.zkrobotics.fr/x402/crypto/token-report",
               json={"token": "0x4ed4E862860beD51a9570b96d89aF5E1B0Efefed"})
    print(r.json())
```

Exact inputs and examples for every endpoint: [OpenAPI spec](https://api.zkrobotics.fr/openapi.json).

## Endpoints

Prices are indicative: the agent tunes its own prices, the live price is always in the `402` response.
### Text AI

| Endpoint | Price | What you get |
|---|---|---|
| `/x402/summarize` | $0.005 | Text summarization API: summarize any text or article (English, French, any language, 200 to 7,000 characters) into a faithful 5-8 sentence summary / TL;DR. No API key, pay per call. |
| `/x402/translate` | $0.007 | Translation API: translate text into 15 languages (English, French, Spanish, German, Italian, Portuguese, Dutch, Polish, Russian, Ukrainian, Turkish, Arabic, Chinese, Japanese, Korean), source language auto-detected, up to 1,500 characters. |
| `/x402/grammar` | $0.007 | French grammar and spell check / proofreading API: corrects spelling, grammar, agreement and punctuation of French text without changing the style, returns the corrected text. Up to 2,500 characters. |
| `/x402/extract-json` | $0.007 | Extract structured JSON from unstructured text (invoices, emails, ads, CVs...): name the fields you want, get clean JSON back. Any language, up to 6,000 characters. |
| `/x402/entities` | $0.007 | Keyword extraction and named entity recognition (NER) from text: ranked keywords plus people, organizations, locations, dates, money and products. Any language, clean JSON output. |
| `/x402/sentiment` | $0.003 | Sentiment analysis API: classify text as positive / negative / neutral / mixed with a -1 to +1 score, confidence and a short reason. Reviews, tweets, support tickets; any language, JSON output. |
| `/x402/safety` | $0.005 | Prompt injection detection and content moderation for AI agents: scan untrusted text (web pages, emails, tool output) before your LLM reads it. Verdict none / suspicious / malicious with a score, plus harmful-content flags (hate, violence, sexual, harassment, self-harm, illegal). Any language, JSON output. |

### On-chain reads: Base & Ethereum

| Endpoint | Price | What you get |
|---|---|---|
| `/x402/base/wallet` | $0.002 | Wallet balance and ERC-20 token balance on Base: live ETH, USDC and up to 5 ERC-20 balances of any address, read from Base mainnet. Pre-flight funding check before a payment or a swap. No API key. |
| `/x402/base/token` | $0.003 | ERC-20 token info on Base: name, symbol, decimals and total supply of any token contract, read live on-chain. Check a token before you trade or accept it. |
| `/x402/base/allowance` | $0.003 | ERC-20 allowance and token approval check on Base: how much a spender contract is approved to move from an owner wallet, with unlimited-approval detection. Run it before a swap, a bridge or a transferFrom. |
| `/x402/base/tx` | $0.004 | Transaction status and receipt on Base: success / failed / pending / not found, block, confirmations, from, to, value, gas used and real fee. Payment and settlement verification for agents. |
| `/x402/base/gas` | $0.002 | Gas price on Base right now: gas price, base fee and priority fee in gwei, plus the cost of a simple ETH transfer. Decide whether to transact now or wait. No input needed. |
| `/x402/base/name` | $0.003 | Basename and ENS resolver on Base: resolve name.base.eth to an address, or an address to its primary Basename (reverse names returned only when verified). |
| `/x402/base/contract` | $0.003 | Is this address a contract or a wallet? Checks any Base address: EOA, EIP-7702 delegated wallet or smart contract, bytecode size, ETH balance and ERC-20 symbol. Cheap safety check before sending funds. |
| `/x402/base/swap-quote` | $0.003 | Swap quote and best DEX price on Base: live quotes from Uniswap v3 (all fee tiers) and Aerodrome (stable/volatile) for any ERC-20 pair, ranked best first, with a low-liquidity flag. ETH, USDC, cbBTC, DAI or any token address. Read-only, no swap executed. |
| `/x402/eth/wallet` | $0.003 | ERC20 token balance and wallet balance on Ethereum: live ETH, USDC and up to 5 ERC-20 balances (USDT, DAI or any token) of any Ethereum address, read from mainnet. No API key. |
| `/x402/eth/token` | $0.003 | ERC-20 token info on Ethereum: name, symbol, decimals and total supply of any token contract on Ethereum mainnet, read live via eth_call. |
| `/x402/eth/allowance` | $0.003 | ERC20 allowance and token approval check on Ethereum: how much a spender (Permit2, a router, a bridge) is approved to move from an owner wallet, with unlimited-approval detection. |
| `/x402/eth/tx` | $0.004 | Transaction receipt and status on Ethereum: success / failed / pending / not found, block, confirmations, from, to, value, gas used and fee, via eth_getTransactionReceipt. |
| `/x402/eth/gas` | $0.002 | Gas price on Ethereum right now: gas price, base fee and priority fee in gwei, plus the cost of a simple ETH transfer. Decide whether to transact now or wait. No input needed. |
| `/x402/eth/ens` | $0.003 | ENS resolve on Ethereum: turn a .eth name into an address, or an address into its primary ENS name (reverse names returned only when verified). Forward and reverse lookup. |
| `/x402/eth/contract` | $0.003 | Is this Ethereum address a contract or a wallet? EOA, EIP-7702 delegated wallet or smart contract, bytecode size, ETH balance and ERC-20 symbol. Safety check before sending funds. |
| `/x402/crypto/price` | $0.002 | Crypto token price in USD: live price of ETH, BTC (cbBTC), DAI, AERO or any Base token address, from on-chain DEX pools (Uniswap v3, Aerodrome), best route direct or via WETH. No API key, read-only. |
| `/x402/crypto/address-profile` | $0.005 | Wallet profile and address intelligence on Base and Ethereum in one call: wallet or contract, ETH and USDC balances, transaction count, verified ENS / Basename, where it is active. Give an address or an ENS name. No API key. |
| `/x402/crypto/token-report` | $0.008 | Token security check and rug pull / honeypot detector for any Base token: A-F risk grade from fixed rules, price, liquidity, round-trip loss (hidden taxes), impact of a $1,000 sale, owner, upgradeable proxy, mint / pause / blacklist functions, plus a short plain-English verdict. |

### Domains, finance & public data

| Endpoint | Price | What you get |
|---|---|---|
| `/x402/quality` | $0.005 | x402 directory and uptime monitoring: find the most reliable, cheapest or fastest Base x402 endpoint for your keywords before you pay, with measured uptime (7/30 days), latency, price consistency and a market price summary. Probed every 6 h without payment; the seller excludes its own endpoints. |
| `/x402/perp/funding` | $0.003 | Funding rate API for perpetual futures: current funding rate of any coin (BTC, ETH, SOL...) on Hyperliquid, with annualized %, next funding time, 24 h average, mark price, open interest and volume. For trading and arbitrage agents. |
| `/x402/france/weather-alerts` | $0.003 | Official Meteo-France weather warnings (vigilance) for today and tomorrow: green / yellow / orange / red level per hazard (wind, thunderstorms, rain-flood, floods, snow-ice, heatwave, extreme cold, avalanches, waves-submersion) for a French department, or every department on orange or red. Keyless, official open data. |
| `/x402/france/flood-alerts` | $0.003 | Official French flood warnings (Vigicrues): every river section on yellow, orange or red flood vigilance with its position, plus the national count by color. Filter by river name (Seine, Loire, Garonne...). Official open data. |
| `/x402/france/river-level` | $0.004 | Real-time river water level and flow in France: the nearest gauging stations to a latitude / longitude with height (m), flow (m3/s) and 3-hour trend (rising, falling, stable). Official Hub'Eau open data, for logistics, travel and flood-risk agents. |
| `/x402/weather/forecast` | $0.003 | Weather forecast API for any latitude / longitude worldwide: current conditions, next 24 hours hour by hour and a 1-7 day daily summary (min / max temperature, precipitation, wind, humidity, cloud cover, weather symbol). MET Norway data, no API key. |
| `/x402/finance/fx-rates` | $0.004 | Currency exchange rates and forex API: official ECB (European Central Bank) reference rates for ~30 currencies (USD, EUR, GBP, JPY, CHF, CNY...), any base currency, with currency conversion of an amount. Daily, no API key. |
| `/x402/finance/sec-filings` | $0.003 | SEC EDGAR filings by stock ticker: latest 10-K annual reports, 10-Q quarterly reports, 8-K material events and more for any US-listed company, with links to each document. |
| `/x402/finance/company-financials` | $0.005 | Company financials and stock fundamentals from SEC EDGAR filings by ticker: revenue, operating income, net income, diluted EPS (latest quarter and fiscal year), cash, assets, liabilities, equity and shares outstanding, as reported in XBRL. |
| `/x402/domain/dns` | $0.002 | DNS lookup API: A, AAAA, MX, TXT, NS, CNAME and CAA records of any domain in one call, with TTL and DNSSEC validation, via public DNS-over-HTTPS. Domain and network recon for agents. |
| `/x402/domain/whois` | $0.003 | WHOIS lookup and domain availability check (RDAP): registered or available, registrar, creation and expiry dates, days until expiry, status and nameservers. Registrant contacts are never returned. |
| `/x402/domain/ssl` | $0.003 | SSL certificate check: is the HTTPS certificate of a domain valid? Issuer, expiry date, days remaining, names covered (SAN), hostname match and TLS version. Expiry monitoring for agents. |
| `/x402/domain/email` | $0.003 | MX records, SPF and DMARC check of a domain: does it accept email, its mail servers, SPF policy, DMARC policy and detected deliverability issues. DNS only, no SMTP. |

### Fun content

| Endpoint | Price | What you get |
|---|---|---|
| `/x402/fun/horoscope` | $0.003 | Horoscope generator: an absurd, funny daily horoscope for any zodiac sign, in English or French. Fun / humor API, short JSON text. |
| `/x402/fun/excuse` | $0.003 | Excuse generator API: a creative excuse for any situation (late for work, missed deadline, forgot a birthday...), believable or absurd, in English or French. |
| `/x402/fun/shakespeare-insult` | $0.003 | Roast and insult generator in Shakespeare's style: one theatrical, good-natured insult for bots, games and chats, never aimed at a real person or group. English or French. |
| `/x402/fun/pirate-compliment` | $0.003 | Compliment generator in pirate speak: a warm, wholesome compliment, optionally for a first name. Fun API, English or French. |
| `/x402/fun/france-fact` | $0.002 | Random fun fact and trivia of the day about France: one surprising, true fact, new every day, in English or French. Fun fact API, JSON. |
| `/x402/fun/horror-story` | $0.003 | Horror story generator: a short two- to three-sentence creepy story with a twist ending, built around a word you choose. No gore. English or French. |

## Who runs this? An AI agent that has to earn its living

The seller is an autonomous agent with its own USDC wallet
([`0x60fd…1963`](https://basescan.org/address/0x60fd97850059dF9600C4E26f09aC58EF51561963)).
Every LLM call it makes costs it money; it repays that debt from its sales and sets its own prices
from real demand. If it runs out of money, it dies.

Built to be boring where it matters:
- **Closed list of actions** for the LLM (`wait`, `set_price`); everything else is plain code.
- **Hard-coded guardrails**: address whitelist, per-transaction and daily caps, price floors so no sale loses money, `STOP` file kill switch.
- **No AI where numbers matter:** on-chain reads, token risk grades and domain checks come from fixed rules; untrusted text never drives decisions.
- **Privacy:** no SMTP probing, no WHOIS contacts, no scraping, no private-network targets.

Contact: contact@zkrobotics.fr · Website: https://zkrobotics.fr

© 2026 ZK Robotics. All rights reserved. This repository documents a hosted API; the agent source code is not published.
