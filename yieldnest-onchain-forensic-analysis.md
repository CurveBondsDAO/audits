# YieldNest (YND) – On-Chain Forensic Analysis

**Date:** March 2026  
**Author:** Independent On-Chain Analysis  

---

## Overview

This report evaluates YieldNest’s on-chain behavior relative to its stated tokenomics, focusing on:

- Fee collection  
- Treasury structure  
- YND buybacks  
- Value accrual to veYND holders  
- Liquidity ownership  

---

## Key Findings

- No observable on-chain evidence of systematic YND buybacks  
- Only partial visibility into fee collection (one confirmed Safe)  
- Fee Safe holds ~$30K vs ~$78K reported cumulative revenue  
- Treasury structure is fragmented and not explicitly defined  
- A single wallet controls the majority of YND/ynETH liquidity  
- No clearly traceable path from protocol revenue to tokenholder value  

---

## Claimed Tokenomics

YieldNest documentation states:

“Protocol revenue is used to repurchase YND and distributed to veYND holders”

“veYND holders receive all protocol revenue”

Expected model:

Fees → Buybacks → veYND Rewards

---

## Buyback Analysis

### Result

No wallet meets the criteria for a systematic buyback executor.

### Observations

- No continuous buying over 6+ months  
- All activity is small, irregular, and retail-sized  
- No treasury-scale accumulation or automated buybacks  

Conclusion:

There is no observable on-chain evidence of systematic YND buybacks.

---

## Fee Collection

### Confirmed Fee Receiver

Address: 0xc92d…6183  
Type: Gnosis Safe  

Behavior:
- Inbound-only  
- No observable outbound transfers  
- Holds ~$30K  

### Revenue Comparison

- Reported revenue: ~$78K  
- Observed balance: ~$30K  

Conclusion:

Fee collection is only partially visible and not consolidated.

---

## Treasury Structure

### Core Wallet

0x0329aCa1a15139e2288E58c8a8a057b7723af4f2

Observations:
- Holds significant YND  
- Controls 8,084 / 8,576 StakeDAO vault shares (~94.3%)  
- Vault holds >97% of all YND/ynETH LP tokens  

Activity:
- Staking  
- Governance  
- Incentives  
- Rewards  

Conclusion:

This wallet functions as the de facto treasury and execution layer.

---

## Liquidity

- Majority of YND/ynETH liquidity is controlled via StakeDAO  
- Controlled indirectly by a single wallet  

Conclusion:

Liquidity is highly concentrated and not transparently structured.

---

## Value Accrual

Claimed:
Fees → Buybacks → veYND holders  

Observed:
Fees → fragmented → treasury → deployment  

Conclusion:

No clear on-chain path from revenue to tokenholder value.

---

## Risk Assessment

- Transparency: fragmented revenue flows  
- Centralization: single wallet controls liquidity and execution  
- Attribution: unclear separation of funds  
- Value accrual: not observable  

---

## Final Conclusion

YieldNest operates a multi-wallet system where:

- Fee collection is partial  
- Treasury is not clearly defined  
- Buybacks are not observable  
- Value accrual is not traceable  

---

## Confidence

- Buybacks: High confidence (none observed)  
- Fee wallet: High  
- Fragmentation: High  
- Treasury concentration: High  
- Value accrual gap: High  

---

## Next Steps

- Trace veYND reward funding  
- Identify all fee inflow sources  
- Map wallet clusters  
- Reconcile revenue vs deployment  
