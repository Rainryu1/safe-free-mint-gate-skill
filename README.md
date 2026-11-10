# Safe Free Mint Gate Skill

Apply the hard safe-and-free gate to a BSC free-mint candidate before showing a preview, and skip it when a material owner control, unverified source, proxy, or paid lane is live.

## What it does

- **Hard gate after audit, before preview**: runs after a contract audit and before any mint preview or yes is asked.
- **Material owner controls**: skips when any live owner control exists — withdraw, sweep, rescue, setBaseURI, setMintOpen, pause, blacklist, invalidateFree, setTreasury — even when the audit decision is CAUTION and the preview looks clean.
- **Proxy / source / paid checks**: skips upgradeable proxies with a live admin, non-exact Sourcify matches, MintRadar red flags, and any paid lane.
- **Liveness check**: reads owner, paused state, and source before skipping on a selector alone — a control in dead code or behind a renounced owner is not live.
- **Degraded audit = unknown**: a failed selector scan is recorded as unknown, never as clean.
- **Free only**: requires a value-0 simulation to pass and the mint price to be zero, gas as the only cost.
- **Predicted token ID is an estimate**: supply moves while the wallet reconnects, so the actual ID is verified from the receipt after the mint.

## Install

```bash
git clone https://github.com/Rainryu1/safe-free-mint-gate-skill ~/workspace/custom-skills/safe_free_mint_gate
```

Then search for `safe free mint gate` in your agent skill list.

## Requirements

- A contract-audit skill (such as `bsc-contract-audit`) to score candidates before the gate.
- Public BSC RPC access for on-chain reads and simulations.
- `baw` CLI (Binance Agentic Wallet) for contract-call previews.

## Companion skill

This gate is the companion to [`airdrop-research`](https://github.com/Rainryu1/airdrop-research-skill), which finds and sorts airdrop candidates and calls this gate before showing a preview.

## Rules the skill enforces

- Skip on any live material owner control, even on a CAUTION audit.
- Never present a predicted token ID as fact before the mint.
- Never mint on a failed selector scan unless Sourcify exact_match, MintRadar no flags, a value-0 simulation, and a clean preview all still hold.
- Record every gate result with block number, Sourcify match, MintRadar result, owner address, and simulation result.

## License

MIT
