# Safe Free Mint Gate

## Apply the hard gate before any preview

- Run this gate after `bsc-contract-audit` and before you show a preview or ask the user for a yes.

- Use `baw-nft-hunter` only to find candidates and use `bsc-contract-audit` only to score them, then let this gate make the final mint or skip call.

- Skip a candidate when any material owner control is live, even when the audit decision is CAUTION and even when the preview looks clean.

- Treat these controls as material when the owner is live and the function can still run, namely withdraw, sweep, rescue, setBaseURI, setMintOpen, pause, unpause, setPaused, blacklist, invalidateFree, and setTreasury.

- Skip a candidate when the contract is a proxy with a live upgrade admin, when the source is not a Sourcify exact match, when MintRadar reports a red flag or a privileged function, or when any part of the mint is paid.

- Check liveness before you skip on a selector alone by reading owner, paused state, and the source, because a selector in dead code or behind a renounced owner does not make a control live.

- Treat an owner address of zero or the dead address as renounced only when no other live role, proxy admin, or non-owner setter can still use the control.

- Require the full contract address verified character for character from a trusted radar or official source, and never guess an address or mint from a partial address.

- Require a value zero simulation to pass, and require the mint price to be zero with gas as the only cost.

- Record the gate result for every candidate with the block number, the Sourcify match, the MintRadar result, the owner address, and the simulation result.

- Report a skipped candidate with the specific control or gap that caused the skip, and do not show a preview or ask for a yes for that candidate.

## Treat a degraded audit as unknown, not clean

- Record the privilege inventory as unknown when the selector scan fails twice with a rate limit or access error, and never write that the privileges are clean from a failed scan.

- Proceed after a failed selector scan only when Sourcify exact match, MintRadar no flags, a value zero simulation, and a clean preview all still hold.

- Skip the candidate when Sourcify or MintRadar is also unavailable, because unknown stays unknown until probed.

- Check the remaining BNB balance before a batch, and report it with every gate result so a batch never assumes gas is available.

## Present the predicted token ID as an estimate

- Present a predicted token ID only as an estimate, and explain in the same message that supply moves while the wallet reconnects and the mint waits.

- Verify the actual token ID after the mint from the receipt Transfer log and a balanceOf read, and report the actual ID to the user.

- Report a difference between the predicted ID and the actual ID as normal supply movement, and never present the predicted ID as a fact before the mint.

## Available connectors

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.

- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.

## Websites

- [Sourcify](https://sourcify.dev/) for verified source matching. Carry the contract address into `browser.browse` and verify that the match is exact_match before you pass the gate.

- [MintRadar](https://datt.my/) for free-mint radar and pre-sign contract checks. Carry the contract address into `browser.browse` and verify that it reports no flags and no privileged function.

- [BscScan](https://bscscan.com/) for contract state and transaction receipts. Carry the contract address or transaction hash into `browser.browse` and verify owner, total supply, and the Transfer log for the actual token ID.

## Alternatives for better results

- Wait for a later drop from the same project when the current contract carries a live owner control, because a renounced redeploy can pass the gate where the first contract cannot.

- Ask the user for a fresh explicit yes only after a candidate passes every gate check, because a batch yes given earlier does not cover a candidate that failed the gate.

- Keep hunting on demand only, with no automatic screening cron and no auto-mint, because the gate is a check on a human approved mint and not a trigger to mint on its own.
