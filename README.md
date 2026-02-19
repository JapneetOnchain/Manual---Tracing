
<div align="center">
  <h1>Manual On-Chain Tracing Portfolio</h1>
  <p><strong>Japneet</strong> • Crypto Compliance Specialist</p>
  <p>Building deep forensics skills for DeFi & Web3 risks</p>

  <img src="https://img.shields.io/badge/Ethereum-3C3C3D?style=for-the-badge&logo=ethereum&logoColor=white" alt="Ethereum" />
  <img src="https://img.shields.io/badge/DeFi-00D1B2?style=for-the-badge&logo=defi&logoColor=white" alt="DeFi" />
  <img src="https://img.shields.io/badge/Tracing-FF6B6B?style=for-the-badge&logo=tracing&logoColor=white" alt="Tracing" />

  <br/><br/>
  <em> • Smart contract risk analysis • Manual Tracing • Real cases</em>
  <br/>
</div>

## About This Repository
This repository contains my daily practice and deep-dive analyses of real Ethereum transactions.  
Crypto-native forensics skills beyond dashboard tools focusing on raw Etherscan tracing, contract function reading, and DeFi-specific risk identification.

### Key Skills Demonstrated
- Manual reconstruction of flows (deposits, withdrawals, transfers, approvals, swaps, liquidity events)
- Reading key smart contract functions & events (deposit, approve, swap, borrow, mint/burn)
- Identifying compliance red flags (unlimited approvals, low-liquidity pools, layering patterns, bridge hops)

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

Method: swapExactETHForTokens

Logs: Swap event

Note: User swapped ETH for large amount of low-liquidity meme token via Uniswap V2 Router. Pattern: ETH → internal WETH wrap → swap → token out.

Compliance risk: High token count + low-value token = potential layering/pump-dump. Flag downstream transfers (bridge/mixer?).



### Case 006 Balancer Vault Swap: AURA → WETH → ETH Unwrap

![7D14D218-6B3B-43E8-A24B-E7E622EE5A0D](https://github.com/user-attachments/assets/6ff2b63f-fb6a-49e1-9367-87ec55025706)


Tx Hash: https://etherscan.io/tx/0xcc25eec5f5fe3c25743a07ca0874b0076bce5e6936aa545dc733ebdcfeb512b0

Type: Balancer Swap (AURA → WETH → ETH)

Input: 69.87 AURA (~$2.38)

Output:0.001182 WETH unwrapped to ETH ($2.43)


Method: swap

Internal: WETH Withdrawal (unwrapped to ETH)

Logs: Swap event (AURA in, WETH out)

Note: User swapped AURA for ETH via Balancer Vault. Pattern: Token in → swap → WETH out → ETH unwrapped to user.

Compliance risk: Small amount on low-cap token — possible test/dusting or layering start. Flag downstream ETH flows (bridge/mixer?).



### Case 007 (Prior Approval + Swap)

![B858DDE8-8B77-4774-9BCD-9051BD77699C](https://github.com/user-attachments/assets/e0887d13-00f2-4e86-a5e3-6632546ca439)


Tx Hash (Approval): https://etherscan.io/tx/0x0fe9d3b10c075fea04aefa7d530e4266bca716cd847b1313732a1cf1638ff1a0

Type: ERC-20 Approval (Unlimited Allowance)

Token: RTX

Spender: Uniswap V2 Router 2

Allowance: Unlimited (2^256 - 1)

Method: approve(address spender, uint256 amount)

Logs: Approval event (owner → router, unlimited amount)
Note: User granted delegated authority to Uniswap V2 Router 2 to spend RTX via transferFrom.
No token balance change occurred. This approval enables future swaps without additional approval transactions.

Compliance Risk:
Unlimited approval exposes wallet to contract risk if spender contract is malicious or compromised. Verify spender legitimacy (Uniswap V2 Router 2 is verified and widely used).


![15F16120-8FA4-48C8-A845-F6F29ED942E0](https://github.com/user-attachments/assets/4e5c34e0-a703-4243-91e2-85516c717c6d)


Tx Hash (Swap): https://etherscan.io/tx/0xe0abae8eddbfb296089b68052180193e50a9278bc81e2c538449d04b664f6819

Type: Uniswap V2 Swap (RTX → WETH → ETH)

Input: 10.32M RTX

Output: ~0.158 ETH

Method: swapExactTokensForETH

Internal: WETH Withdrawal (WETH → ETH unwrap)

Logs:
Transfer event (RTX: wallet → Uniswap V2 Pair)
Swap event (pricing execution in pool)
WETH Transfer (pair → router)
Withdrawal event (WETH unwrapped to ETH)

Note:
User swapped RTX for ETH via Uniswap V2.

Execution flow:
Token in → Router transferFrom → Pool swap → WETH out → WETH unwrapped → ETH sent to wallet.
This swap was enabled by the prior unlimited RTX approval granted to the Uniswap V2 Router.

Compliance Risk:
Standard DEX swap pattern. Key validation point: delegated authority was previously granted, so token movement was authorized.


#### Case 008  Liquidity Added(MINT)



![29ED050E-49AE-4DA2-BA30-53A6264AACB1](https://github.com/user-attachments/assets/89fcab18-0ee2-4c53-b4ba-9dc147682013)


![E5FF69F4-7533-43D8-8B33-9D17C7CFF6AD](https://github.com/user-attachments/assets/eebeb45d-da24-4f6e-8447-e0673ad8c0c1)

Tx Hash: https://etherscan.io/tx/0x85f24d8e52788d0fa658c318a14b58f84a8e9ce773247264802e20ae520a3efa

Type: Uniswap V2 Liquidity Add

Tokens Added: 1,000,000,000 GMPEPE + 0.5 ETH ($967.68)

Method: Add Liquidity ETH

Logs: Approval, PairCreated, MaxTxAmountUpdated, Transfer (GMPEPE to pool), Deposit (WETH), Transfer (WETH to pool), Sync, Mint (LP tokens)

Note: Creator added 1B GMPEPE + 0.5 ETH to new Uniswap V2 pool → received ~707M UNI-V2 LP tokens.

Crypto-native insight: Low ETH pairing = very low liquidity → high impermanent loss & price manipulation risk.

Compliance risk: Creator holds LP tokens → rug pull potential. Small initial liquidity + new token = high pump/dump or layering risk.

Non compliance perspective: This is a standard token launch step — creator adds initial liquidity + enables trading via OpenTrading(). Common for meme/community tokens to bootstrap volume and earn fees as LP.




## Disclaimer
This case study is for educational and research purposes only.
All addresses are public and no illicit activity is asserted.
