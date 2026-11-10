# Safe Free Mint Gate Skill

Apply the hard safe-and-free gate to a free-mint candidate on any network before showing a preview, and skip it when a material owner control, unverified source, proxy, or paid lane is live.

## Networks

The gate applies to every network. The principles are identical on every chain; only the verification tools differ.

| Network | Chain ID | Source verification | Explorer / radar | Gas token |
|---|---|---|---|---|
| Ethereum | 1 | Sourcify exact_match | Etherscan | ETH |
| BSC | 56 | Sourcify exact_match | MintRadar (datt.my), BscScan | BNB |
| Base | 8453 | Sourcify exact_match | Basescan | ETH |
| Arbitrum | 42161 | Sourcify exact_match | Arbiscan | ETH |
| Polygon | 137 | Sourcify exact_match | Polygonscan | POL |
| Optimism | 10 | Sourcify exact_match | Optimistic Etherscan | ETH |
| Avalanche | 43114 | Sourcify exact_match | Snowtrace | AVAX |
| Hyperliquid (HyperEVM) | 999 | Sourcify exact_match where supported, otherwise the explorer verified source | HyperEVMScan | HYPE |
| Robinhood Chain | — | Arbitrum Orbit L2, chain ID to confirm when live; EVM rules apply | Chain explorer once live | To confirm |
| Elysium | 99901 | Explorer verified source (Sourcify support unconfirmed) | elysium.kinetiq.xyz/explorer | HYPE |
| Solana | — | Program verified on Solscan / SolanaFM | Solscan, SolanaFM | SOL |
| Any other chain | — | Same principles with that chain's explorer and public RPC | Chain explorer | Chain gas token |

## What it does

- **Hard gate after audit, before preview**: runs after a contract audit and before any mint preview or yes is asked.
- **Material owner controls**: skips when any live owner control exists — withdraw, sweep, rescue, setBaseURI, setMintOpen, pause, blacklist, invalidateFree, setTreasury — even when the audit decision is CAUTION and the preview looks clean.
- **Proxy / source / paid checks**: skips upgradeable proxies with a live admin, unverified sources, radar red flags, and any paid lane.
- **Liveness check**: reads owner, paused state, and source before skipping on a selector alone — a control in dead code or behind a renounced owner is not live.
- **Degraded audit = unknown**: a failed selector scan is recorded as unknown, never as clean.
- **Free only**: requires a value-0 simulation to pass and the mint price to be zero, gas as the only cost.
- **Predicted token ID is an estimate**: supply moves while the wallet reconnects, so the actual ID is verified from the receipt after the mint.
- **Solana**: requires the program verified on Solscan / SolanaFM with no freeze or mint authority held by the team.
- **Chains without reliable verification**: skipped, with the reason stated.

## Install

```bash
git clone https://github.com/Rainryu1/safe-free-mint-gate-skill ~/workspace/custom-skills/safe_free_mint_gate
```

Then search for `safe free mint gate` in your agent skill list.

## Requirements

- A contract-audit skill to score candidates before the gate.
- Public RPC access for the chain being checked, for on-chain reads and simulations.
- A wallet CLI (such as `baw` for Binance Agentic Wallet) for contract-call previews.

## Companion skill

This gate is the companion to [`airdrop-research`](https://github.com/Rainryu1/airdrop-research-skill), which finds and sorts airdrop candidates across all networks and calls this gate before showing a preview.

## Rules the skill enforces

- Skip on any live material owner control, even on a CAUTION audit.
- Never present a predicted token ID as fact before the mint.
- Never mint on a failed selector scan unless the verified source, no flags, a value-0 simulation, and a clean preview all still hold.
- Record every gate result with block number, verification result, radar or explorer result, owner address, and simulation result.

## License

MIT
