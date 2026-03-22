📊 YieldNest (YND) – On-Chain Forensic Analysis

Date: March 2026
Author: Independent On-Chain Analysis

📌 Overview

This report evaluates YieldNest’s on-chain behavior relative to its stated tokenomics, with a focus on:

Fee collection
Treasury structure
YND buybacks
Value accrual to veYND holders
Liquidity ownership
🔴 Key Findings
❌ No observable on-chain evidence of systematic YND buybacks
⚠️ Only partial visibility into fee collection (one confirmed Safe)
⚠️ Fee Safe holds ~$30K vs ~$78K reported cumulative revenue
⚠️ Treasury structure is fragmented and not explicitly defined
⚠️ A single wallet controls the majority of YND/ynETH liquidity
❌ No clearly traceable path from protocol revenue → tokenholder value
📘 Claimed Tokenomics

YieldNest documentation states:

“Protocol revenue is used to repurchase YND and distributed to veYND holders”

“veYND holders receive all protocol revenue”

Expected Model

Fees → Buybacks → veYND Rewards

🔍 Buyback Analysis
Methodology
Analyzed YND transfers over 6–12 months
Filtered DEX swap activity (Uniswap, Sushi, aggregators)
Identified wallets performing YND market buys
Ranked by consistency, duration, and size
🚨 Result

No wallet meets the criteria for a systematic buyback executor

📊 Largest Buyers Identified
Wallet	Duration	# Buys	YND Bought	USD Value	Type
0xA1bC…	~5 mo	5	~750k	~$500	Retail
0xF7e9…	~4 mo	4	~500k	~$350	Retail
0x3D9a…	~3–4 mo	4	~480k	~$320	Retail
🧠 Interpretation
All buying activity is:
Small
Irregular
Retail-sized
No:
Treasury-scale accumulation
Automated buybacks
Sustained market support
🔴 Conclusion

No observable on-chain evidence of systematic YND buybacks

💰 Fee Collection
🟢 Confirmed Fee Receiver

Address: 0xc92d…6183
Type: Gnosis Safe

Behavior
Inbound-only
No observable outbound transfers
Holds ~$30K
Multi-sig controlled
📊 Revenue Comparison
DefiLlama Revenue: ~$78K
Wallet Balance: ~$30K
Difference: ~$48K
🧠 Interpretation
Revenue is:
Not fully retained here
Not consolidated into a single wallet

Possible explanations:

Multi-chain flows
Additional fee receivers
Partial deployment
⚠️ Key Finding

Only partial fee visibility exists on-chain

🏦 Treasury Structure
🔴 Core Wallet

0x0329aCa1a15139e2288E58c8a8a057b7723af4f2

Observed
Holds significant YND
Controls 8,084 / 8,576 StakeDAO vault shares (~94.3%)
Vault holds >97% of all YND/ynETH LP tokens
Activity
Staking
Governance
Incentive distribution
Reward interactions
🧠 Interpretation

0x0329 controls the overwhelming majority of YND/ynETH liquidity via StakeDAO

🟡 Supporting Wallet

0xee984…fa05

Historical distribution / staging wallet
🧩 System Model

Fees (fragmented)
→ 0xc92d (fee Safe – passive)
→ 0xee984 (staging)
→ 0x0329 (treasury + execution)
→ liquidity / staking / incentives / governance

🌊 Liquidity & PoL
🔍 Observations
Majority of YND/ynETH liquidity:
Held via StakeDAO vault
Controlled by 0x0329
⚠️ Conclusion

No clearly defined or transparently managed Protocol-Owned Liquidity (PoL) system is observable

🔗 Value Accrual
Claimed

Fees → Buybacks → veYND holders

Observed

Fees → fragmented → treasury wallet → deployment

🚨 Gap
No observable:
Buyback pipeline
Reward funding linkage
🧠 Conclusion

Revenue → tokenholder value is not transparently observable on-chain

⚠️ Risk Assessment
Transparency Risk
Fragmented revenue flows
No single source of truth
Centralization Risk
0x0329 controls:
liquidity
treasury-aligned assets
governance influence
Attribution Risk
Hard to separate:
treasury vs operational funds
Value Accrual Risk
No visible link between:
revenue → token value
📌 Final Conclusion

YieldNest operates a multi-wallet, partially opaque system where:

A fee Safe exists but is incomplete
A single wallet (0x0329) controls liquidity + execution
No systematic buybacks are observable
Value accrual pathways are not clearly traceable
🔥 Bottom Line

On-chain behavior does not clearly match the stated “buyback-and-distribute” model

✅ Confidence Levels
Finding	Confidence
No systematic buybacks	High
Fee Safe identified	High
Fee fragmentation	High
Treasury concentration	High
No clear value accrual path	High
PoL unclear	Medium
