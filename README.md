Basic Wallet Activity Tracing

This case documents simple on-chain actions performed by a single wallet:
- Deposits
- Withdrawals
- Token swaps

The objective is to demonstrate basic manual tracing and interpretation
of transaction intent using public blockchain data.

---

## Wallet Overview
- Network: Ethereum
- Wallet Type: Externally Owned Account (EOA)
- Observation Period: Short-duration activity snapshot

-- - - - -- - - - - -  - - - -- - - - - - - - - - - - - - - - - - - -- - - - - - - -- - - - - - - - - - - - - - - - -- - - -  -  - - - - - - - -  - - - - - -  - -
### Case 001 Deposit/Swap — Ether to Token

<img width="1418" height="588" alt="9CCA8174-0455-4A97-8DFA-8135A9EA29A0" src="https://github.com/user-attachments/assets/f022d13a-dee7-48e1-8703-c4a7f041e153" />

Tx Hash: https://etherscan.io/tx/0x270d8e09e4c96facadee702190eae79b589dd1373fb99c73e6baa8cf35b12366

ETH Deposited: 0.076615 ETH (internally by Banana Gun Router)
Method: deposit() (called internally by router)
Internal Transactions: Transfer 0.076615 ETH from Router to WETH contract + WETH minted back to Router

**Flow Summary**:
- Router sent ETH to WETH contract → called deposit() → minted 0.076615 WETH.
- WETH then sent to Uniswap V2 pool → swapped for 1,133,562.945 BITPENGU tokens.
- Output: User received large quantity of low-liquidity meme token (BITPENGU).
  
**Compliance Note**:
- Internal WETH Deposit is standard for DeFi routers (automation/gas efficiency).
- BITPENGU swap (high token count, low per-token value) could be speculative or layering step — flag for downstream monitoring.
- The intent of the transaction appears to be **asset conversion**, not custody transfer


### Case 002 ETH Unwrap Action — WETH to ETH

![1B5181AB-02B6-49FA-8812-C4AC76E6A306](https://github.com/user-attachments/assets/11c1ee8b-a876-4fe1-b91e-ef006c1f649d)

Tx Hash: https://etherscan.io/tx/0x45b6728d38f4aa1509b2baac74c6dd6302665db24f67475002249c4ec7716f5a

WETH Withdrawn: 0.05 WETH (burned)

Method: withdraw(uint256) / 0x2e1a7d4d
Logs: Withdrawal event (wad matches amount)

Note: Simple WETH contract withdrawal (unwrap). User sent WETH to contract → called withdraw() → contract burned WETH → sent equal ETH back. This is the reverse of a Deposit, not a traditional funds withdrawal. This is a normal DeFi preparatory or closing action and has no inherent risk indicators.





### Case 003 ERC-20 Token Transfer — WETH (Tiny Amount)


![BC4B0476-C4A9-4FA8-8A97-54797A8C63D7](https://github.com/user-attachments/assets/87b3b58a-61ee-4f9a-8fb4-a4976c05be96)

Tx Hash: https://etherscan.io/tx/0x16b34e6279cb7d79d14ee8195fb9f64247b92cc60dfe0fd407d295888c6dedc7

Type: ERC-20 Token Transfer (WETH)

From → To: 0xF5Ac3cc6dB8131e12A5A77cF423424E5862B5FB7 → 0x5af33A0F7a7C6DbF2caA3F66Ce0C2d9C9D1E9Ba0

Amount: 0.00000001 WETH (10000000000 wei)
Logs: Transfer event (from, to, value = 10000000000 = 0.00000001 WETH)

Note: Simple ERC-20 transfer of WETH. Sender moved existing WETH tokens to receiver. No mint/burn — just movement.
There was no contract interaction beyond the `transfer` call and no DeFi activity.

Compliance Note: This could be dusting or peel chain? or maybe just a test transfer



### Case 004 Unlimited Approval 

![033D0B26-7559-49FD-A554-4B32594D82AA](https://github.com/user-attachments/assets/016c44c9-9ded-4553-9ab8-85c52791df6c)


Tx Hash: https://etherscan.io/tx/0x8cdb894ae780cf9fad047061be34eef373abf736bb1a1e6b4f5c059e88dd1d54

Type: ERC-20 Approval (RIZO token)

Method: approve / 0x095ea7b3

Approved Amount: Unlimited (max uint256)

Spender: Uniswap V2 Router 2

Note: User granted unlimited permission to Uniswap V2 Router for RIZO. No tokens moved, just allowance set.

Compliance risk: Unlimited approval = high risk (drain potential if spender compromised). Alone = flag & monitor. Needs downstream txns to confirm layering/malicious use.




### Case 005 Simple Swap (Uniswap V2)


![24DC181B-6822-4449-A9F7-638AF27841FC](https://github.com/user-attachments/assets/53433989-aabe-4456-9a3e-c5781e2cc618)



Tx Hash: https://etherscan.io/tx/0x49face7224a8785529914c1089e92d8b48a1ee6ad8327dc30fb32b7240eac7f9
Type: Uniswap V2 Swap (ETH → Token)
Input: 0.06817 ETH (~$133.64)
Output: 2,962,989.756 RocketRiders (RDRS)
Internal: WETH Deposit (ETH wrapped automatically)
Method: swapExactETHForTokens (or similar)
Logs: Swap event
Note: User swapped ETH for large amount of low-liquidity meme token via Uniswap V2 Router. Pattern: ETH → internal WETH wrap → swap → token out.
Compliance risk: High token count + low-value token = potential layering/pump-dump. Flag downstream transfers (bridge/mixer?).





## Disclaimer
This case study is for educational and research purposes only.
All addresses are public and no illicit activity is asserted.
