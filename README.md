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


























## Disclaimer
This case study is for educational and research purposes only.
All addresses are public and no illicit activity is asserted.
