# Safe Free Mint Gate

## Apply the hard gate before any preview — on any network

- This gate applies to **every network**, not just BSC: Ethereum, BSC, Base, Arbitrum, Polygon, Optimism, Avalanche, Hyperliquid (HyperEVM), Robinhood Chain, Elysium, Solana, and any other chain where a free mint appears. The principles are identical on every chain; only the verification tools differ.
- Run this gate after the contract audit and before you show a preview or ask the user for a yes.
- Use the hunter only to find candidates and the audit only to score them, then let this gate make the final mint or skip call.

## Networks

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

## Gate rules — identical on every chain

- Skip a candidate when any material owner control is live, even when the audit decision is CAUTION and even when the preview looks clean.
- Treat these controls as material when the owner is live and the function can still run, namely withdraw, sweep, rescue, setBaseURI, setMintOpen, pause, unpause, setPaused, blacklist, invalidateFree, and setTreasury.
- Skip a candidate when the contract is a proxy with a live upgrade admin, when the source is not verified, when the radar or explorer reports a red flag or a privileged function, or when any part of the mint is paid.
- Check liveness before you skip on a selector alone by reading owner, paused state, and the source, because a selector in dead code or behind a renounced owner does not make a control live.
- Treat an owner address of zero or the dead address as renounced only when no other live role, proxy admin, or non-owner setter can still use the control.
- Require the full contract address verified character for character from a trusted radar or official source, and never guess an address or mint from a partial address.
- Require a value zero simulation to pass, and require the mint price to be zero with gas as the only cost.
- Record the gate result for every candidate with the block number, the verification result, the radar or explorer result, the owner address, and the simulation result.
- Report a skipped candidate with the specific control or gap that caused the skip, and do not show a preview or ask for a yes for that candidate.

## EVM chains

- Require Sourcify exact_match where Sourcify supports the chain; where it does not (for example Elysium), require the verified source on the chain's own explorer instead.
- Check owner, paused state, and free-lane state with `eth_call` against a public RPC for that chain.
- Require a value-0 simulation to pass with the user wallet as the sender, and a clean contract-call preview with value 0.
- Check the remaining gas-token balance on that chain before a batch (BNB on BSC, ETH on Ethereum/Base/Arbitrum/Optimism, POL on Polygon, AVAX on Avalanche, HYPE on Hyperliquid and Elysium), and report it with every gate result so a batch never assumes gas is available.

## Solana

- Require the program to be verified on Solscan / SolanaFM, with no freeze authority and no mint authority held by the team (or both revoked).
- Skip any program with an unverified source, a live upgrade authority that can change the rules, or a paid lane.
- Require the free claim to be actually open for the user wallet, and check the remaining SOL balance before a batch.

## Chains without reliable verification

- If a chain has no reliable source verification (no Sourcify support and no trustworthy explorer verification), skip the candidate and say why. Unknown stays unknown until probed.

## Treat a degraded audit as unknown, not clean

- Record the privilege inventory as unknown when the selector scan fails twice with a rate limit or access error, and never write that the privileges are clean from a failed scan.
- Proceed after a failed selector scan only when the verified source, no flags, a value zero simulation, and a clean preview all still hold.
- Skip the candidate when the verification source or the radar is also unavailable, because unknown stays unknown until probed.

## Present the predicted token ID as an estimate

- Present a predicted token ID only as an estimate, and explain in the same message that supply moves while the wallet reconnects and the mint waits.
- Verify the actual token ID after the mint from the receipt Transfer log and a balanceOf read, and report the actual ID to the user.
- Report a difference between the predicted ID and the actual ID as normal supply movement, and never present the predicted ID as a fact before the mint.

## Available connectors

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.
- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.

## Websites

- [Sourcify](https://sourcify.dev/) for verified source matching on EVM chains. Carry the contract address into `browser.browse` and verify that the match is exact_match before you pass the gate.
- [MintRadar](https://datt.my/) for BSC free-mint radar and pre-sign contract checks. Carry the contract address into `browser.browse` and verify that it reports no flags and no privileged function.
- [Solscan](https://solscan.io/) and [SolanaFM](https://solana.fm/) for Solana program verification. Carry the program address into `browser.browse` and verify the program is verified with no freeze or mint authority held by the team.
- Chain explorers (Etherscan, BscScan, Basescan, Arbiscan, Polygonscan, Snowtrace, HyperEVMScan, elysium.kinetiq.xyz) for contract state and transaction receipts on their chain. Carry the contract address or transaction hash into `browser.browse` and verify owner, total supply, and the Transfer log for the actual token ID.

## Alternatives for better results

- Wait for a later drop from the same project when the current contract carries a live owner control, because a renounced redeploy can pass the gate where the first contract cannot.
- Ask the user for a fresh explicit yes only after a candidate passes every gate check, because a batch yes given earlier does not cover a candidate that failed the gate.
- Keep hunting on demand only, with no automatic screening cron and no auto-mint, because the gate is a check on a human approved mint and not a trigger to mint on its own.
