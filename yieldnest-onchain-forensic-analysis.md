# 📊 YieldNest (YND) – On-Chain Forensic Analysis Report

**Date:** March 2026  
**Author:** James Nexus - Curve Bonds DAO Risk Evaluations  

---

# 1. Executive Summary

This report evaluates YieldNest’s on-chain behavior relative to its stated tokenomics, with a focus on:

- Fee collection  
- Treasury structure  
- YND buybacks  
- Value accrual to veYND holders  
- Liquidity ownership  

---

## 🔴 Key Findings

- No observable on-chain evidence of systematic YND buybacks  
- Only partial visibility into fee collection (one confirmed Safe)  
- Fee Safe holds ~$30K vs ~$78K reported cumulative revenue  
- Treasury structure is fragmented and not explicitly defined  
- A single wallet (0x0329) controls the majority of YND/ynETH liquidity via StakeDAO  
- No clearly traceable path from protocol revenue → tokenholder value  

---

# 2. Claimed Tokenomics (Documentation)

YieldNest documentation states:

> “Protocol revenue is used to repurchase YND and distributed to veYND holders”

and:

> “veYND holders receive all protocol revenue”

This implies the following model:

```
Fees → Buybacks → veYND Rewards
```

---

# 3. Buyback Analysis

## 🔍 Methodology

- Analyzed YND token transfers over the past 6–12 months  
- Filtered for DEX-based purchase activity (Uniswap, Sushi, aggregators)  
- Identified and ranked wallets performing YND market buys  
- Evaluated duration, consistency, and size of purchases  

---

## 🚨 Key Result

**No wallet meets the criteria for a systematic or programmatic buyback executor**

---

## 📊 Observed Buyer Activity

- No wallet executed continuous buys over a 6+ month period  

All detected buying behavior was:

- Short-lived (3–5 months max)  
- Low frequency  
- Small size (hundreds of dollars total)  

---

## Largest Buyers Identified

| Wallet | Duration | # Buys | YND Bought | USD Value | Classification |
|--------|---------|--------|-----------|----------|---------------|
| 0xA1bC… | ~5 mo | 5 | ~750k | ~$500 | Retail |
| 0xF7e9… | ~4 mo | 4 | ~500k | ~$350 | Retail |
| 0x3D9a… | ~3–4 mo | 4 | ~480k | ~$320 | Retail |

---

## 🧠 Interpretation

All identified buyers are retail-sized participants  

No:

- treasury-scale accumulation  
- automated buyback behavior  
- sustained market presence  

---

## 🔴 Conclusion

There is no observable on-chain evidence of systematic YND buybacks  

---

⚠️ Note:

This does not prove buybacks never occurred, but they are:

- not visible  
- not systematic  
- not traceable as described in documentation  

---

# 4. Fee Collection Analysis

## 🟢 Confirmed Fee Receiver

**0xc92d…6183 (Gnosis Safe)**

### Behavior:

- Inbound-only  
- No observable outbound transfers  
- Holds approximately $30K  
- Controlled by multiple signers  

---

## 📊 Revenue Comparison

- DefiLlama cumulative revenue: ~$78K  
- Observed wallet balance: ~$30K  
- Difference: ~$48K  

---

## 🧠 Interpretation

Revenue is:

- not fully retained in this wallet  
- not consolidated into a single visible location  

Likely explanations:

- multi-chain flows  
- additional fee receivers  
- partial deployment elsewhere  

---

## ⚠️ Key Finding

Only partial visibility into fee collection exists on-chain  

---

# 5. Treasury & Wallet Structure

## 🔴 Core Wallet

**0x0329aCa1a15139e2288E58c8a8a057b7723af4f2**

### Observed:

- Holds significant YND balance  
- Controls 8,084 / 8,576 YND/ynETH StakeDAO vault shares (~94.3%)  
- The StakeDAO vault itself holds >97% of all YND/ynETH LP tokens  

### Actively performs:

- staking  
- governance participation  
- reward interactions  
- incentive campaign activity  

---

## 🧠 Interpretation

0x0329 controls the overwhelming majority of YND/ynETH liquidity via the StakeDAO vault  

### This implies:

**Effective control over:**

- protocol-aligned liquidity  
- emissions / gauge influence  

**Indirect control of:**

- >97% of YND/ynETH LP exposure  

---

## 🟡 Supporting Wallet

**0xee984…fa05**

- Historical staging / distribution wallet  
- Source of large YND transfers to 0x0329  

---

## 🧩 System Model

```
Fees (fragmented)
      ↓
0xc92d (fee Safe – passive)
      ↓ (unclear routing)
0xee984 (staging)
      ↓
0x0329 (treasury + execution)
      ↓
- liquidity (StakeDAO vault)
- staking
- incentives
- governance
```

---

# 6. Liquidity & PoL

## 🔍 Observations

Majority of YND/ynETH liquidity is:

- held via StakeDAO vault  
- effectively controlled by 0x0329  

---

## 🧠 Interpretation

Protocol-aligned liquidity likely exists, but is:

- not labeled as treasury-owned  
- not isolated  
- not transparently structured  

---

## ⚠️ Conclusion

No clearly defined or transparently managed Protocol-Owned Liquidity (PoL) system is observable on-chain  

---

# 7. Value Accrual Path

## Claimed Model

**Fees → Buybacks → veYND holders**

## Observed Model

**Fees → fragmented → treasury wallet (0x0329) → deployment**

---

## 🚨 Critical Gap

No observable:

- buyback flow  
- reward funding linkage  

No clear connection between:

- protocol revenue  
- tokenholder value  

---

## 🧠 Conclusion

Value accrual from protocol revenue to YND/veYND holders is not transparently observable on-chain  

---

# 8. Risk & Transparency Assessment

## ⚠️ Transparency Risk

- Revenue flows fragmented across multiple wallets  
- No single on-chain source of truth  

---

## ⚠️ Centralization Risk

0x0329 controls:

- liquidity  
- treasury-aligned assets  
- governance influence  

---

## ⚠️ Attribution Risk

Difficult to distinguish:

- treasury funds  
- operational funds  

---

## ⚠️ Value Accrual Risk

No clearly observable mechanism linking:

- revenue → token value  

---

# 9. Final Conclusion

YieldNest operates a multi-wallet, partially opaque system where:

- A fee collection Safe exists, but captures only part of revenue  
- A single dominant wallet (0x0329) controls liquidity and acts as treasury + execution  
- No systematic buybacks are observable on-chain  
- The revenue → tokenholder value pathway is not clearly traceable  

---

## 🔥 Bottom Line

**YieldNest’s on-chain behavior does not currently match its stated “buyback-and-distribute” tokenomic model in a clearly observable or verifiable way.**
