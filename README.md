# Curve Bonds DAO Security Review

![Audit](https://img.shields.io/badge/Audit-Completed-brightgreen)
![Status](https://img.shields.io/badge/Status-Public-blue)
![Version](https://img.shields.io/badge/Version-1.21-black)

---

## 📑 Table of Contents

- [Executive Summary](#executive-summary)
- [Scope](#scope)
- [Methodology](#methodology)
- [System Overview](#system-overview)
- [Funds Flow](#funds-flow)
- [Trust Model](#trust--privilege-model)
- [Assumptions](#assumptions)
- [Threat Model](#threat-model)
- [Attack Surface](#attack-surface-analysis)
- [Security Properties](#security-properties-and-invariants)
- [Findings](#findings)
- [Attack Analysis](#attack-analysis)
- [Recommendations](#recommendations)
- [Residual Risks](#residual-risks)
- [Limitations](#limitations)
- [Upgrade Risk](#upgrade-risk)
- [Formal Verification Targets](#formal-verification-targets)
- [Appendix](#appendix--functional-review)
- [Conclusion](#conclusion)
- [Certification](#certification)

---

CURVE BONDS DAO SECURITY REVIEW
sCBD / scbdETH / sBOND (xERC20 Vault Tokens)

FINAL REPORT

This review does not constitute financial advice or an endorsement of the protocol.

# Executive Summary

This report presents a security review of the Curve Bonds DAO vault token system, including sCBD, scbdETH, and sBOND.

The system implements a yield-bearing vault share model in which users gain exposure to underlying assets and accrued yield through share-based accounting.

The review focused on the following areas:

- correctness of accounting logic
- reward distribution mechanics
- integration with Curve StableSwap pools
- economic and smart contract attack vectors

No critical or high-severity vulnerabilities were identified within the scope of the reviewed contracts.

No economic exploit vectors resulting in direct value extraction were identified within the reviewed contract logic, under the stated assumptions.

The system exhibits:

- consistent share-based accounting behavior
- a structured reward distribution and unlock mechanism
- design characteristics intended to mitigate exposure to common DeFi attack vectors

Residual risks remain and are primarily associated with:

- governance behavior and permission controls
- external integration assumptions, including interactions with Curve
- market conditions and liquidity environments

The absence of identified critical or high-severity issues should not be interpreted as an absence of risk. Security outcomes remain dependent on correct configuration, governance behavior, and external system interactions.

This assessment is limited to the scoped contracts and stated assumptions and does not extend to external systems, off-chain components, or governance implementation beyond what is described in this report.

# Scope

Contracts Reviewed
The procedures performed covered the xERC20 implementation underlying the following vault tokens:
- sCBD
- scbdETH
- sBOND

Files Reviewed

- xERC20.sol (vault implementation)
- sCBD vault contract
- scbdETH vault contract
- sBOND vault contract
- CBD token contract
- cbdETH token contract
- BOND token contract
- sCBD vault implementation
- scbdETH vault implementation
- sBOND vault implementation
- Underlying token contracts (CBD, cbdETH, BOND)

Functional Areas
The assessment included analysis of the following components:
- deposit and share minting logic
- withdrawal and redemption logic
- reward distribution and unlock mechanics
- price-per-share (PPS) accounting
- integration assumptions with Curve StableSwap pools

Exclusions
The following components were not included within the scope of this review:
- governance implementation and operational procedures
- multisig configuration and key management
- frontend interfaces and off-chain infrastructure
- external protocol risks beyond the assumptions explicitly stated

The scope of this review is limited strictly to the contracts listed above. No additional contracts or systems were reviewed unless explicitly stated.

# Methodology

Severity Definitions

Critical
Issues that can result in direct and immediate loss of funds.

High
Issues that may result in loss of funds or significant system disruption under certain conditions.

Medium
Issues that may affect system integrity, user experience, or economic outcomes but are not directly exploitable.

Low
Issues that present minor risks, inefficiencies, or edge-case inconsistencies.

Informational
Observations that improve clarity, documentation, or best practices without introducing risk.


The procedures performed consisted of manual review and analytical assessment of the scoped contracts, using techniques consistent with standard smart contract security reviews.

Techniques Applied
- manual code review
- invariant-based reasoning
- adversarial modeling of potential attack scenarios
- evaluation of economic manipulation vectors
- analysis of external integration surfaces

Depth of Analysis
The review focused on contract-level logic, accounting correctness, and reward distribution mechanisms within the provided artifacts.

Tooling
No automated analysis tools or formal verification frameworks were applied as part of this review.

Focus Areas
The review emphasized validation of:
- asset conservation properties
- correctness and consistency of share accounting
- reward distribution behavior and time-based unlock mechanics
- resistance to economic and integration-based manipulation
- safety assumptions related to external systems

Limitations of Approach
The procedures performed are limited to the scoped contracts and stated assumptions. The review does not guarantee identification of all potential vulnerabilities, particularly those arising from external systems, governance behavior, configuration errors, or market conditions.

# Artifacts / Code Reference

ARTIFACTS PROVIDED

The following artifact was provided during the preparation of this report:

- xERC20.sol (vault implementation)
- sCBD vault contract
- scbdETH vault contract
- sBOND vault contract
- CBD token contract
- cbdETH token contract
- BOND token contract

SCOPE RELATIONSHIP

The audit scope references an “xERC20 implementation underlying” the vault tokens sCBD, scbdETH, and sBOND. The provided artifact is assumed to correspond to this implementation.

However, explicit linkage between the provided file and the originally defined audit scope is not specified in the source material.

# System Overview

The system consists of vault-based share tokens (sCBD, scbdETH, and sBOND) that represent proportional ownership of underlying ERC20 assets held by the vault contracts.

Users interact with the vault by depositing an underlying ERC20 token (stakeToken) and receiving newly minted vault shares. These shares represent a claim on the vault’s underlying assets, including any rewards that have been unlocked.

Vault share supply and value are determined using a price-per-share (PPS) mechanism derived from the ratio of underlying assets held by the contract to the total share supply.

The system includes a reward distribution mechanism in which additional underlying tokens are transferred into the vault and progressively unlocked over time. During the unlock period, a portion of the distributed rewards remains excluded from PPS calculations.

# Funds Flow

Deposit Flow
# A user calls stake() with a specified amount of the underlying token.
# The contract transfers the underlying tokens from the user into the vault.
# The contract calculates the number of shares to mint based on:
   - the amount of tokens received
   - the current PPS
# The contract mints new vault shares to the user.

Withdrawal Flow
# A user calls withdraw() with a specified number of vault shares.
# The contract calculates the amount of underlying tokens redeemable based on the current PPS.
# The contract burns the user’s vault shares.
# The contract transfers the corresponding amount of underlying tokens to the user.

Reward Distribution Flow
# An authorized reward distributor calls distributeReward() with a reward amount.
# The contract transfers underlying tokens into the vault.
# If no active reward unlock period exists:
   - a new unlock period is started
# If an unlock period is already active:
   - the remaining locked portion of the previous reward is recomputed and combined with the new reward
# The total locked reward amount is tracked and gradually excluded from PPS until fully unlocked.

PPS Behavior
- PPS increases as underlying assets increase relative to total shares.
- During an active reward unlock period, a computed portion of rewards remains excluded from PPS.
- Once rewards are fully unlocked, they are fully reflected in PPS.

# Intended Usage Model

The intended usage model is not explicitly specified in the source material.

At the contract level, stake() and withdraw() are externally callable functions and are not restricted to privileged roles. Any assumptions regarding restricted interaction through specific frontends, aggregators, or governance-controlled interfaces are not enforced by the contract code itself.

Accordingly, any usage constraints should be treated as off-chain or operational assumptions rather than contract-level guarantees.

# Trust / Privilege Model

This section defines the trust assumptions and privilege boundaries derived from the uploaded vault contracts (sCBD, scbdETH, sBOND) and the provided underlying token context.

No rewriting of prior sections has been performed.

ROLES AND CAPABILITIES

# Contract Owner

Capabilities:
- transferOwnership(address)
- setRewardDistributor(address,bool)

Description:
The owner can transfer ownership and designate or revoke reward distributor permissions.

Limitations:
- No direct mint function for vault shares
- No direct function to withdraw arbitrary underlying assets
- No direct modification of PPS calculation logic

Risk Considerations:
- Owner can assign reward distributors, which indirectly controls reward injection into the vault
- Owner role remains a central administrative authority at the contract level

# Reward Distributor

Capabilities:
- distributeReward(uint128)

Access Control:
- Restricted to addresses flagged in isRewardDistributor

Description:
Reward distributors can transfer underlying tokens into the vault and initiate or extend reward unlock periods.

Limitations:
- Cannot withdraw assets from the vault
- Cannot mint shares directly
- Cannot modify user balances outside reward injection

Risk Considerations:
- Controls timing and magnitude of reward distribution
- Affects PPS progression through reward injection timing

# Users (External Callers)

Capabilities:
- stake(uint256)
- withdraw(uint256)
- transfer / transferFrom / approve

Description:
Users can deposit underlying tokens to receive shares and redeem shares for underlying tokens.

Limitations:
- Must provide underlying tokens to receive shares
- Must burn shares to redeem underlying tokens

Risk Considerations:
- Subject to PPS at time of interaction
- Subject to reward unlock timing effects

# Underlying Token Contracts (CBD, cbdETH, BOND)

Capabilities (based on provided context and uploaded files):
- CBD / cbdETH: mint and burn capabilities under owner control
- BOND: burn capability only

Description:
Vault contracts rely on underlying ERC20 tokens for accounting and transfers.

Limitations:
- Vault contracts do not enforce restrictions on underlying token minting behavior

Risk Considerations:
- Mint authority on CBD and cbdETH introduces external supply risk not controlled by vault contracts
- Vault correctness assumes standard ERC20 behavior

PRIVILEGE SUMMARY

| Role                | Key Capabilities                     | Direct Asset Extraction | Share Mint Control | Reward Control |
|---------------------|--------------------------------------|--------------------------|--------------------|----------------|
| Owner               | setRewardDistributor, ownership      | No                       | No                 | Indirect       |
| Reward Distributor  | distributeReward                     | No                       | No                 | Yes            |
| User                | stake, withdraw, transfer            | No                       | No                 | No             |

PRIVILEGE BOUNDARY OBSERVATIONS

- No role has direct access to arbitrary withdrawal of vault assets
- Share minting occurs only through stake() and is tied to received underlying tokens
- Reward distributors influence PPS through reward timing, not direct extraction
- Owner authority is limited to administrative configuration within observed contract logic

QUALIFICATIONS

- This model is derived strictly from the uploaded vault contracts
- It does not include off-chain governance processes, multisig controls, or operational procedures
- It does not guarantee absence of risk outside contract-level permissions

# Assumptions

Contract-Level Assumptions

- The deployed vault contracts correspond to the uploaded implementations for sCBD, scbdETH, and sBOND.
- The contract logic behaves as observed in the uploaded code, including:
  - share minting through stake()
  - share burning through withdraw()
  - PPS calculation via getPricePerFullShare()
  - reward distribution via distributeReward()
- No undisclosed privileged functions or upgrade mechanisms exist beyond those observed in the uploaded contracts.

Governance Assumptions

- Ownership and reward distributor roles are controlled according to intended operational procedures.
- The assignment of reward distributors is performed in a controlled and non-malicious manner.
- No assumption is made regarding multisig usage or key distribution, as this is not specified in source material.

Underlying Token Assumptions

- CBD and cbdETH tokens have mint and burn capabilities controlled by an external authority, as stated in project context.
- BOND token has burn capability only.
- Underlying tokens behave as standard ERC20 tokens for transfer, balance, and allowance operations.
- No fee-on-transfer, rebasing, or callback-based behavior is assumed unless otherwise specified.

Integration Assumptions

- External integrations (including Curve StableSwap pools and aggregators) interact with the vault contracts as expected.
- Any external pricing or oracle mechanisms operate according to their intended design.
- No reliance is placed on unverified external contract behavior within this section.

Market Assumptions

- Market conditions do not introduce adversarial liquidity scenarios beyond those considered in the threat model.
- Liquidity, slippage, and user behavior are not guaranteed and may impact real-world outcomes.

# Threat Model

Actors

# Users
- Interact with the vault through stake(), withdraw(), and token transfers
- May attempt to exploit timing, rounding, or PPS-related behavior

# Reward Distributors
- Authorized to inject rewards into the vault
- May attempt to influence PPS timing or reward distribution patterns

# Contract Owner
- Controls reward distributor permissions
- May attempt to influence system behavior through role assignment

# External Actors
- Interact indirectly via integrations or market mechanisms
- May attempt economic manipulation or arbitrage strategies

Attack Surfaces

# PPS (Price Per Share)
- Core accounting mechanism
- Sensitive to reward timing and underlying balance changes

# Reward Distribution System
- Time-based unlock logic
- Interaction between new rewards and existing locked rewards

# Stake / Withdraw Functions
- Entry and exit points for user funds
- Dependent on PPS correctness and rounding behavior

# Underlying Token Behavior
- External minting (CBD, cbdETH) may affect system backing
- Non-standard ERC20 behavior could impact transfers

# External Integrations
- Curve pools and aggregators may introduce additional pricing or execution assumptions

Threat Considerations

- Timing-based interactions (e.g., reward distribution during active unlock periods)
- Rounding behavior in share minting and redemption calculations
- External token supply changes affecting vault backing
- Misconfiguration of reward distributor permissions
- Economic strategies exploiting PPS transitions

Qualifications

- This threat model is limited to contract-level behavior and explicitly stated assumptions
- It does not include analysis of external protocols, governance processes, or market dynamics beyond stated considerations

Limitations

- No verification of Curve oracle or pricing mechanisms is performed in this section
- No simulation of real-world market conditions or adversarial liquidity scenarios is included
- No formal verification or fuzz testing results are incorporated

# Attack Surface Analysis

This section identifies and evaluates the primary contract-level surfaces through which adversarial behavior may occur, based strictly on the uploaded vault contracts and previously defined threat model.

No assumptions beyond stated system behavior are introduced.

PRIMARY SURFACES

# stake() Entry Point

Description:
User-facing function for depositing underlying tokens and minting vault shares.

Attack Vectors:
- Timing-based entry during reward unlock transitions
- Rounding effects in share minting calculations
- Interaction with non-standard ERC20 tokens (if assumptions violated)

Assessment:
Share minting is based on measured received tokens and PPS, reducing risk of over-minting.

Qualification:
Dependent on correctness of PPS calculation and underlying token transfer behavior.

---

# withdraw() Exit Point

Description:
User-facing function for burning shares and redeeming underlying tokens.

Attack Vectors:
- Timing-based exits before/after reward unlock events
- Rounding truncation in redemption calculations
- Potential interaction ordering risks (burn before transfer)

Assessment:
Burn-before-transfer pattern reduces reentrancy exposure.

Qualification:
Integer division may introduce minor value discrepancies.

---

# distributeReward() Mechanism

Description:
Privileged function allowing reward distributors to inject underlying tokens and modify reward unlock state.

Attack Vectors:
- Timing manipulation of reward injections to influence PPS trajectory
- Front-running or sequencing relative to user deposits/withdrawals
- Recomposition of locked rewards affecting yield distribution

Assessment:
No direct extraction possible; influence is limited to reward timing and PPS progression.

Qualification:
Effectiveness depends on distributor permissions and off-chain governance.

---

# PPS (Price Per Share) Calculation

Description:
Core accounting mechanism used in stake() and withdraw().

Attack Vectors:
- Manipulation through timing of reward distribution
- Sensitivity to underlying token balance changes
- Dependence on correct exclusion of locked rewards

Assessment:
PPS is computed deterministically from contract state.

Qualification:
Accuracy depends on correctness of locked reward calculation and ERC20 balance integrity.

---

# Reward Unlock Logic

Description:
Time-based mechanism controlling when distributed rewards become reflected in PPS.

Attack Vectors:
- Strategic timing of reward additions during active unlock periods
- Interaction between new and partially unlocked rewards

Assessment:
Behavior is deterministic but introduces time-dependent outcomes.

Qualification:
May produce different economic outcomes depending on user interaction timing.

---

# Underlying Token Interaction

Description:
Vault relies on external ERC20 tokens (CBD, cbdETH, BOND) for transfers and accounting.

Attack Vectors:
- External minting affecting backing (CBD, cbdETH)
- Non-standard ERC20 implementations (fees, callbacks, rebasing)

Assessment:
Vault assumes standard ERC20 behavior.

Qualification:
Risks originating from underlying tokens are external to vault enforcement.

---

# Role Assignment (Owner → Reward Distributor)

Description:
Owner-controlled assignment of reward distributor permissions.

Attack Vectors:
- Misconfiguration of distributor roles
- Assignment to malicious or compromised addresses

Assessment:
No direct asset extraction capability granted.

Qualification:
Introduces governance and operational risk outside contract logic.

---

SECONDARY SURFACES

- ERC20 approve/transferFrom allowance model
- External integrations (Curve pools, aggregators)
- Off-chain assumptions about restricted access or usage patterns

---

SURFACE SUMMARY

| Surface                  | Access Type   | Risk Type                    | Direct Fund Risk |
|--------------------------|--------------|------------------------------|------------------|
| stake()                  | Public       | Timing / Rounding            | No               |
| withdraw()               | Public       | Timing / Rounding            | No               |
| distributeReward()       | Restricted   | Timing / PPS influence       | No               |
| PPS Calculation          | Internal     | Accounting correctness       | No               |
| Reward Unlock Logic      | Internal     | Time-dependent behavior      | No               |
| Underlying Tokens        | External     | Supply / behavior dependency | Indirect         |
| Role Assignment          | Owner        | Governance / configuration   | Indirect         |

---

QUALIFICATIONS

- Analysis is limited to contract-level behavior observed in uploaded files.
- No external protocol verification is included.
- No assumption is made regarding governance implementation beyond stated roles.

# Security Properties and Invariants

ACCOUNTING INVARIANTS

# Proportional Ownership
- Vault shares represent a proportional claim on underlying assets held by the contract.
- PPS is computed as underlying balance divided by total share supply (with locked rewards excluded during active unlock).

Assessment:
Supported by getPricePerFullShare() and share mint/burn flows observed in the uploaded contracts.

# Asset-Backed Minting
- New shares are minted only in stake() based on the amount of underlying tokens actually received by the contract.

Assessment:
Supported by stake() logic measuring received tokens and computing shares via PPS prior to mint.

Qualification:
Subject to standard ERC20 transfer behavior of the underlying token.

# Proportional Redemption
- Withdrawals burn user shares and return an amount of underlying proportional to PPS at execution time.

Assessment:
Supported by withdraw() logic computing underlying from PPS, then burning shares before transfer.

Qualification:
Integer division may introduce truncation effects.

REWARD INVARIANTS

# Reward Isolation (Locked vs Unlocked)
- A portion of distributed rewards remains excluded from PPS until unlocked.

Assessment:
Supported by getPricePerFullShare() subtracting a computed lockedRewardAmount during active unlock periods.

# Time-Dependent Unlock
- Reward realization progresses over time between lastRewardTimestamp and currentUnlockEndTimestamp.

Assessment:
Supported by arithmetic form used to compute lockedRewardAmount.

# Reward Top-Up Behavior
- New rewards added during an active unlock period are combined with the remaining locked portion of prior rewards.

Assessment:
Supported by distributeReward() recomputing remaining locked rewards and aggregating with new rewards.

SAFETY INVARIANTS

# No External Share Mint Path
- No externally callable function mints shares without receiving underlying assets.

Assessment:
Supported by absence of external mint function; _mint is only invoked internally from stake().

# Controlled Asset Outflow
- Underlying assets are transferred out only during withdraw(), which requires burning user shares.

Assessment:
Supported by withdraw() flow; no owner-only arbitrary withdrawal function observed.

Qualification:
Limited to observed contract functions; does not include external systems.

# Reentrancy Protection on Core Entry Points
- stake(), withdraw(), and distributeReward() are protected by nonReentrant.

Assessment:
Supported by presence of ReentrancyGuard and modifiers on these functions.

Qualification:
Does not imply complete absence of all interaction risks.

# Standard ERC20 Allowance Semantics
- approve()/transferFrom() follow overwrite-style allowance behavior.

Assessment:
Supported by implementation; known race-condition considerations apply.

QUALIFICATIONS

- These properties are derived from the uploaded vault contracts and validated behaviors in Step D.0.
- They do not extend to external integrations, governance processes, or underlying token mint authority beyond stated assumptions.

# Findings

Status Definitions

Intended — Behavior aligns with system design and does not represent a vulnerability.
Standard — Known industry-wide behavior not unique to this implementation.
Clarified — Behavior confirmed through review that may differ from initial assumptions.
Assumption — Behavior dependent on external systems or governance.

FINDINGS SUMMARY

| ID    | Severity      | Title                                      | Status     |
|-------|--------------|--------------------------------------------|------------|
| F-01  | Low          | Reward Timing Alters Yield Realization     | Intended   |
| F-02  | Low          | ERC20 Allowance Race Condition             | Standard   |
| F-03  | Informational| Non-ERC4626 Interface                      | By Design  |
| F-04  | Informational| Underlying Token Mint Authority Dependency | Assumption |
| F-05  | Informational| Stake/Withdraw Accessibility Model         | Clarified  |

DETAILED FINDINGS

F-01 — Reward Timing Alters Yield Realization

Description:
When distributeReward() is called during an active unlock period, the remaining locked portion of prior rewards is recomputed and combined with new rewards. This alters the effective rate at which rewards become reflected in PPS.

Impact:
- Changes effective yield realization timing
- May affect user outcomes depending on interaction timing

Likelihood:
Not specified in source material.

Assessment:
Supported by distributeReward() logic observed in uploaded contracts.

Recommendation:
Ensure integrators and users understand that reward timing affects PPS progression.

Status:
Intended system behavior.

---

F-02 — ERC20 Allowance Race Condition

Description:
The approve()/transferFrom() pattern allows overwriting allowances, which may enable race conditions where a spender uses both old and new allowances.

Impact:
- Potential unintended token transfers under specific front-running conditions

Likelihood:
Standard ERC20 behavior.

Assessment:
Observed in implementation; consistent with standard ERC20 semantics.

Recommendation:
Users should set allowance to zero before updating or use max allowance patterns carefully.

Status:
Standard ERC20 limitation.

---

F-03 — Non-ERC4626 Interface

Description:
The vault contracts implement custom share accounting and do not conform to the ERC4626 standard.

Impact:
- Integration assumptions may differ from ERC4626-compliant vaults

Likelihood:
Not specified in source material.

Assessment:
Confirmed by absence of ERC4626 interface in uploaded contracts.

Recommendation:
Document interface behavior clearly for integrators.

Status:
By design.

---

F-04 — Underlying Token Mint Authority Dependency

Description:
Underlying tokens CBD and cbdETH include mint authority controlled externally, which may affect total backing of vault assets.

Impact:
- External minting could affect perceived backing of vault shares

Likelihood:
Dependent on governance and external controls.

Assessment:
Based on provided token context and uploaded contracts.

Recommendation:
Clearly disclose reliance on external token governance and mint controls.

Status:
Assumption-based dependency.

---

F-05 — Stake/Withdraw Accessibility Model

Description:
stake() and withdraw() functions are externally callable and not restricted to administrative roles.

Impact:
- Contradicts interpretations where interaction is assumed to be restricted to frontends or aggregators

Likelihood:
Deterministic from contract code.

Assessment:
Verified from uploaded vault contracts.

Recommendation:
Ensure documentation reflects actual contract-level access model.

Status:
Clarified behavior.

---

QUALIFICATIONS

- Findings are limited to uploaded contracts and stated assumptions.
- No external protocol behavior (e.g., Curve pricing) is verified here.

# Attack Analysis

This section evaluates how the identified attack surfaces and system behaviors interact under adversarial conditions. The analysis is limited to contract-level behavior and explicitly stated assumptions.

No exploitable critical or high-severity vulnerabilities were identified within scope. The following analysis focuses on timing, accounting, and integration-related behaviors.

---

# Reward Timing Interaction

Scenario:
A reward distributor injects rewards during an active unlock period, while users deposit or withdraw around the same time.

Behavior:
- Newly distributed rewards are partially locked and excluded from PPS
- Existing locked rewards are recomputed and combined with new rewards
- PPS progression becomes time-dependent

Impact:
- Users entering before or after reward events may experience different effective yields
- No direct value extraction occurs, but outcomes vary based on timing

Assessment:
This behavior is deterministic and consistent with the reward unlock design.

---

# Entry and Exit Timing Sensitivity

Scenario:
Users strategically time stake() and withdraw() calls around reward unlock boundaries.

Behavior:
- PPS reflects only unlocked rewards
- Locked rewards are excluded until release

Impact:
- Users may optimize entry or exit timing relative to reward unlock schedule
- Results in non-uniform yield distribution across participants

Assessment:
This is an inherent property of time-based reward systems and not a contract flaw.

---

# Rounding and Precision Effects

Scenario:
Repeated deposits and withdrawals with small amounts.

Behavior:
- Integer division in share minting and redemption introduces truncation
- Minor discrepancies accumulate over multiple operations

Impact:
- Small value differences between expected and actual returns
- No systemic imbalance observed

Assessment:
Consistent with standard ERC20 and share-based accounting implementations.

---

# Reward Distributor Influence

Scenario:
An authorized distributor controls timing and magnitude of reward injections.

Behavior:
- Can influence PPS trajectory through reward scheduling
- Cannot directly extract funds or modify user balances

Impact:
- Affects distribution of yield over time
- May advantage or disadvantage users based on timing

Assessment:
Influence is indirect and limited to reward timing; no direct exploit path observed.

---

# Underlying Token Dependency

Scenario:
Underlying token supply changes externally (e.g., minting of CBD or cbdETH).

Behavior:
- Vault relies on ERC20 balances for accounting
- External minting may alter perceived backing conditions

Impact:
- Changes in external token supply may affect economic interpretation of vault value
- Vault logic remains internally consistent

Assessment:
This is an external dependency risk, not a vault contract vulnerability.

---

# Integration Assumption Risk

Scenario:
External systems (e.g., aggregators) assume ERC4626 compatibility or immediate yield realization.

Behavior:
- Vault does not conform to ERC4626
- PPS excludes locked rewards during unlock

Impact:
- Misinterpretation of yield or share value by integrators
- Potential user-facing inconsistencies

Assessment:
Risk arises from incorrect integration assumptions, not contract behavior.

---

OVERALL ASSESSMENT

- No direct fund extraction vectors were identified within contract-level logic
- Observed behaviors are primarily time-dependent and accounting-related
- Economic outcomes may vary based on interaction timing and external conditions

---

QUALIFICATIONS

- Analysis is limited to uploaded contracts and defined assumptions
- No adversarial simulation or on-chain testing performed
- External systems and governance behavior are not verified

# Recommendations

The following recommendations are provided to improve clarity, robustness, and integration safety of the system. These are not tied to critical or high-severity vulnerabilities but address areas of design transparency, integration consistency, and risk communication.

---

# Document Reward Timing Behavior

Description:
The timing of reward distribution directly affects PPS progression and user outcomes.

Recommendation:
- Clearly document how reward timing impacts yield realization
- Provide examples of behavior during active unlock periods
- Clarify that PPS does not immediately reflect newly distributed rewards

Rationale:
Improves user and integrator understanding of non-linear yield realization.

Priority:
Medium

---

# Explicitly Document Non-ERC4626 Behavior

Description:
The vault does not implement ERC4626, which may lead to incorrect integration assumptions.

Recommendation:
- Document interface differences compared to ERC4626
- Provide integration guidance for aggregators and frontends
- Clarify PPS and share accounting semantics

Rationale:
Reduces risk of incorrect assumptions by third-party integrators.

Priority:
Medium

---

# Clarify Underlying Token Dependency Risks

Description:
Vault correctness depends on underlying token behavior, including mint authority for CBD and cbdETH.

Recommendation:
- Explicitly disclose reliance on external token governance
- Document mint/burn authority structure for underlying tokens
- Clarify that vault contracts do not enforce supply constraints

Rationale:
Improves transparency around external systems and systemic risk.

Priority:
Medium

---

# Recommend Safe Allowance Usage Patterns

Description:
Standard ERC20 allowance overwrite behavior introduces known race conditions.

Recommendation:
- Encourage users to set allowance to zero before updating
- Provide frontend safeguards where applicable
- Document risks of allowance reuse

Rationale:
Mitigates common ERC20 misuse patterns.

Priority:
Low

---

# Consider Optional ERC4626 Compatibility Layer

Description:
While current implementation is functional, lack of ERC4626 compatibility may limit composability.

Recommendation:
- Evaluate optional adapter or wrapper implementing ERC4626 interface
- Maintain current accounting model while improving compatibility

Rationale:
Enhances ecosystem integration without altering core logic.

Priority:
Low

---

# Enhance Role Transparency

Description:
Reward distributor permissions influence reward timing and PPS behavior.

Recommendation:
- Publicly document authorized distributor addresses
- Provide visibility into reward distribution patterns where possible

Rationale:
Improves transparency and user trust in reward mechanics.

Priority:
Low

---

QUALIFICATIONS

- Recommendations are derived from observed contract behavior and system design.
- They do not imply the presence of exploitable vulnerabilities.

# Residual Risks

This section outlines risks that remain after considering the contract design, implemented controls, and observed system behavior. These risks are inherent to the system architecture, external dependencies, or operational assumptions and are not classified as exploitable vulnerabilities within the scope of this review.

---

# Reward Timing Variability

Description:
The timing of reward distribution and unlock periods introduces variability in how yield is realized by users.

Risk:
- Users interacting at different times may experience different effective returns
- Yield distribution is not uniform across all participants

Assessment:
This is an inherent property of progressive reward unlock mechanisms and not a contract defect.

---

# External Token Supply Dependency

Description:
The vault relies on underlying tokens (CBD and cbdETH) that have externally controlled mint authority.

Risk:
- Changes in underlying token supply may affect perceived backing or economic interpretation of vault shares
- Vault contracts do not enforce supply constraints on underlying assets

Assessment:
This dependency exists outside the vault contract logic and must be managed through external governance and transparency.

---

# Integration Assumption Risk

Description:
External integrators may assume ERC4626 compatibility or immediate reward realization.

Risk:
- Incorrect assumptions may lead to mispricing, incorrect UI display, or improper accounting in third-party systems

Assessment:
The vault does not conform to ERC4626 and uses a time-based reward model, which must be explicitly understood by integrators.

---

# Governance and Role Management Risk

Description:
The contract owner controls assignment of reward distributor roles.

Risk:
- Misconfiguration or compromise of privileged roles may impact reward timing and system behavior

Assessment:
No direct fund extraction is possible via these roles, but operational integrity depends on proper governance.

---

# Market and Liquidity Risk

Description:
The system operates within broader DeFi markets and liquidity environments.

Risk:
- Changes in liquidity, pricing, or market conditions may impact user outcomes
- Slippage and execution conditions are not controlled by the vault contracts

Assessment:
These risks are external to the contract logic and inherent to DeFi participation.

---

# Precision and Rounding Effects

Description:
Integer arithmetic used in share minting and redemption introduces rounding behavior.

Risk:
- Minor discrepancies in expected vs actual values over repeated interactions

Assessment:
Consistent with standard ERC20-based accounting systems and not indicative of a flaw.

---

OVERALL ASSESSMENT

- Residual risks are primarily economic, operational, or integration-related
- No residual risks were identified that enable direct extraction of funds within contract scope

---

QUALIFICATIONS

- Residual risks are derived from observed system behavior and stated assumptions
- They do not imply the presence of exploitable vulnerabilities

# Limitations

This section outlines the limitations of the procedures performed and the scope of conclusions drawn in this report.

---

SCOPE LIMITATIONS

- The review is limited to the uploaded vault contracts (sCBD, scbdETH, sBOND) and the provided context regarding underlying tokens.
- While contract artifacts were provided, no repository reference, commit hash, or deployment addresses were supplied for independent verification.
- The assessment does not confirm that the reviewed artifacts match any deployed contracts.

---

METHODOLOGY LIMITATIONS

- The procedures consisted of manual review and analytical assessment; no automated tooling, fuzzing, or formal verification results are included.
- No comprehensive test suite execution or coverage analysis was performed.
- No gas analysis or optimization review was conducted.

---

ENVIRONMENTAL LIMITATIONS

- External protocols and integrations (including Curve StableSwap pools, aggregators, and any oracle or pricing mechanisms) were not verified.
- No assessment of MEV, front-running in production environments, or block-level execution ordering was performed.
- No evaluation of cross-chain behavior or bridge-related risks was conducted.

---

GOVERNANCE AND OPERATIONAL LIMITATIONS

- Governance structure, multisig configuration, key management, and operational procedures were not reviewed.
- No assessment of role management processes (e.g., assignment/removal of reward distributors) beyond contract-level permissions.
- No verification of incident response, monitoring, or upgrade processes.

---

ECONOMIC AND MARKET LIMITATIONS

- No economic modeling or stress testing under adverse market conditions was performed.
- No evaluation of liquidity depth, slippage, or pricing dynamics in real-world markets.
- No simulation of cascading failures across dependent protocols.

---

UNDERLYING TOKEN LIMITATIONS

- Underlying tokens (CBD, cbdETH, BOND) were considered based on provided context; their full implementations, governance, and mint/burn controls were not independently verified within this section.
- Assumptions regarding standard ERC20 behavior may not hold if tokens implement non-standard features.

---

OVERALL LIMITATION

- The absence of identified critical or high-severity issues should not be interpreted as a guarantee of security.
- Undiscovered vulnerabilities may exist, particularly in areas outside the defined scope or under conditions not evaluated.

---

QUALIFICATIONS

- Conclusions are valid only within the stated scope, assumptions, and limitations.
- This report should be interpreted as one component of a broader security process.

FINAL LIMITATIONS

- No guarantees are provided regarding future behavior, external integrations, or operational practices.

# Upgrade Risk

This section evaluates risks related to potential upgrades, contract changes, and administrative control over system evolution.

---

CURRENT STATE

- No explicit upgradeability mechanism (e.g., proxy pattern) is specified in the source material.
- No upgrade functions were identified in the uploaded vault contracts.
- Ownership is transferable via transferOwnership(address).

Assessment:
The contracts appear to be non-upgradeable at the code level, based on available information.

Qualification:
No deployment configuration or proxy architecture was provided for verification.

---

ADMINISTRATIVE CONTROL RISK

Description:
The contract owner retains control over:
- Ownership transfer
- Assignment and removal of reward distributor roles

Risk:
- Administrative actions may alter system behavior (e.g., reward timing via distributor assignment)
- Compromise or misuse of the owner role may impact system operation

Assessment:
Administrative influence is indirect and does not enable direct extraction of vault assets.

---

DEPLOYMENT AND MIGRATION RISK

Description:
Future upgrades, if required, would likely involve deploying new contracts and migrating user funds.

Risk:
- Migration processes may introduce operational risk
- Users may be required to trust off-chain procedures during transitions

Assessment:
Migration risk is external to current contract logic but relevant for lifecycle considerations.

---

INTEGRATION UPGRADE RISK

Description:
Changes to external systems (e.g., Curve pools, aggregators, or underlying tokens) may affect system behavior.

Risk:
- External upgrades may alter assumptions used in this system
- Integrations may require updates to remain compatible

Assessment:
These risks originate outside the vault contracts and must be managed operationally.

---

OVERALL ASSESSMENT

- No direct upgrade path was identified within the contract code
- Upgrade-related risks are primarily administrative, operational, or external

---

QUALIFICATIONS

- Analysis is limited to uploaded contracts and provided context
- No verification of proxy usage or deployment architecture

# Formal Verification Targets

This section outlines candidate properties and invariants that may be suitable for formal verification or invariant-based testing. These targets are derived from observed contract behavior and are intended to guide deeper assurance efforts.

---

ACCOUNTING PROPERTIES

# Share-to-Asset Consistency

Property:
For all states, total share supply and underlying asset balance maintain a consistent relationship through PPS, excluding locked rewards during active unlock periods.

Target:
- Verify correctness of getPricePerFullShare() under all state transitions
- Ensure no state allows over-minting or under-redemption of shares

---

# Asset-Backed Minting

Property:
Shares are minted only when underlying tokens are received by the contract.

Target:
- Prove that stake() cannot mint shares without a corresponding increase in underlying balance
- Validate measurement of received tokens is accurate under all ERC20-compliant behaviors

---

# Proportional Redemption

Property:
Withdrawals return underlying tokens proportional to shares burned and current PPS.

Target:
- Verify withdraw() correctly computes and transfers proportional value
- Ensure no state allows withdrawal of more assets than accounted for

---

REWARD MECHANISM PROPERTIES

# Locked Reward Exclusion

Property:
Locked rewards are excluded from PPS until fully unlocked.

Target:
- Prove that lockedRewardAmount is correctly excluded in all PPS calculations during active unlock periods
- Verify no premature inclusion of locked rewards

---

# Reward Unlock Progression

Property:
Locked rewards decrease monotonically over time until fully unlocked.

Target:
- Validate correctness of time-based arithmetic governing unlock
- Ensure no state allows negative or increasing locked reward amounts without new distribution

---

# Reward Aggregation

Property:
New rewards added during an active unlock period correctly combine with remaining locked rewards.

Target:
- Verify distributeReward() recomputes and aggregates locked rewards without loss or duplication
- Ensure consistency across successive reward injections

---

SAFETY PROPERTIES

# No Unauthorized Minting

Property:
No externally callable function can mint shares without receiving underlying assets.

Target:
- Prove that _mint is only reachable through stake()
- Verify no alternative execution path exists for share creation

---

# Controlled Asset Outflow

Property:
Underlying tokens are transferred out only through withdraw() and proportional to burned shares.

Target:
- Verify no function enables arbitrary transfer of underlying assets
- Ensure withdraw() enforces correct burn-before-transfer sequencing

---

# Reentrancy Safety

Property:
Core state-changing functions are protected against reentrancy.

Target:
- Verify nonReentrant guards are correctly applied to stake(), withdraw(), and distributeReward()
- Confirm no internal call paths bypass these protections

---

ASSUMPTION-DEPENDENT PROPERTIES

# ERC20 Compliance

Property:
Underlying tokens behave according to standard ERC20 semantics.

Target:
- Validate system assumptions under compliant tokens
- Identify potential invariant violations under non-standard token behavior (out of scope for strict proof)

---

OVERALL OBJECTIVE

- Ensure conservation of assets within the vault
- Validate correctness of share accounting across all state transitions
- Confirm reward distribution logic maintains intended invariants

---

QUALIFICATIONS

- Targets are derived from observed contract behavior and are not exhaustive
- Formal verification is not performed within this report

# Appendix — Functional Review

This section provides a structured overview of key contract functions and their observed behavior. The review is limited to functionality present in the uploaded vault contracts and does not include external systems.

---

CORE FUNCTIONS

# stake(uint256 amount)

Description:
Deposits underlying tokens into the vault and mints corresponding shares.

Observed Behavior:
- Transfers underlying tokens from the caller to the contract
- Measures the actual amount of tokens received
- Calculates shares to mint using current PPS
- Mints shares to the caller

Key Considerations:
- Relies on accurate PPS calculation
- Dependent on standard ERC20 transfer behavior
- Protected by nonReentrant

---

# withdraw(uint256 shares)

Description:
Burns vault shares and returns proportional underlying tokens.

Observed Behavior:
- Calculates redeemable underlying using current PPS
- Burns user shares
- Transfers underlying tokens to the caller

Key Considerations:
- Burn-before-transfer ordering reduces reentrancy exposure
- Integer division introduces rounding truncation
- Protected by nonReentrant

---

# distributeReward(uint128 amount)

Description:
Injects additional underlying tokens into the vault and updates reward unlock state.

Observed Behavior:
- Transfers reward tokens into the contract
- If no active unlock period:
  - Initializes a new unlock schedule
- If unlock period active:
  - Recomputes remaining locked rewards
  - Aggregates new reward with remaining locked amount
- Updates unlock timing variables

Key Considerations:
- Restricted to authorized reward distributors
- Influences PPS indirectly via reward timing
- Protected by nonReentrant

---

VIEW FUNCTIONS

# getPricePerFullShare()

Description:
Returns the current PPS based on underlying balance and share supply.

Observed Behavior:
- Computes PPS as underlying balance divided by total share supply
- Subtracts locked reward portion during active unlock periods

Key Considerations:
- Central to all share accounting
- Sensitive to reward timing and locked reward calculations

---

# totalSupply()

Description:
Returns total supply of vault shares.

Observed Behavior:
- Reflects total minted shares minus burned shares

---

# balanceOf(address)

Description:
Returns share balance of a given address.

Observed Behavior:
- Standard ERC20 balance tracking

---

ERC20 FUNCTIONS

# transfer / transferFrom / approve

Description:
Standard ERC20 functions enabling share transfers and allowance management.

Observed Behavior:
- Implements standard overwrite-style allowance semantics
- No additional restrictions observed

Key Considerations:
- Subject to known ERC20 allowance race condition patterns

---

ACCESS CONTROL FUNCTIONS

# setRewardDistributor(address,bool)

Description:
Assigns or removes reward distributor permissions.

Observed Behavior:
- Updates isRewardDistributor mapping

Key Considerations:
- Controlled by contract owner
- Affects who can call distributeReward()

---

# transferOwnership(address)

Description:
Transfers contract ownership to a new address.

Observed Behavior:
- Updates owner state

Key Considerations:
- Central administrative control function

---

OVERALL FUNCTIONAL OBSERVATIONS

- Core functionality is centered around share-based accounting and PPS
- Reward distribution introduces time-dependent behavior
- No arbitrary asset withdrawal functions were observed
- No external mint path for shares was identified

---

QUALIFICATIONS

- Functional descriptions are derived from uploaded contracts
- No external integrations are included in this section

# Conclusion

This report presents the results of a security review of the Curve Bonds DAO vault token system, including sCBD, scbdETH, and sBOND, based on the uploaded contracts and stated assumptions.

Within the defined scope and limitations, no critical or high-severity smart contract vulnerabilities were identified.

The system demonstrates:

- consistent share-based accounting aligned with PPS mechanics
- a structured reward distribution model with time-based unlock behavior
- controlled asset flow through stake() and withdraw() functions
- absence of direct asset extraction paths within contract-level logic

Observed risks are primarily:

- time-dependent yield realization due to reward unlock mechanics
- reliance on external token behavior and governance (CBD, cbdETH)
- integration assumptions, particularly regarding non-ERC4626 behavior
- operational and governance considerations related to role management

These risks are inherent to the system design or external dependencies and do not constitute exploitable vulnerabilities within the reviewed contracts.

The security of the system depends on:

- correct configuration and management of privileged roles
- transparent documentation of system behavior and assumptions
- proper integration by external systems and user interfaces
- ongoing monitoring of external dependencies and market conditions

This assessment should be interpreted within the context of the stated scope, assumptions, and limitations. It represents a point-in-time review and does not guarantee the absence of vulnerabilities.

Continued security efforts, including monitoring, testing, and potential formal verification, are recommended to maintain and improve system robustness over time.

Security is an ongoing process, and continued monitoring, testing, and review are recommended as the system evolves.

# Certification

This report reflects the results of a security review conducted on the Curve Bonds DAO vault token system (sCBD, scbdETH, sBOND) based on the materials provided and the procedures described herein.

The review was performed in accordance with generally accepted practices for smart contract security analysis, including manual code review, invariant-based reasoning, and adversarial modeling within the defined scope.

The findings and conclusions presented in this report are based solely on:

- the uploaded contract code
- the stated system context and assumptions
- the limitations explicitly outlined in this report

No guarantees are made regarding:

- the absence of vulnerabilities outside the defined scope
- the behavior of external systems, integrations, or dependencies
- future changes to the system, contracts, or operational procedures

This report represents a point-in-time assessment and should not be interpreted as a warranty of security or fitness for any particular purpose.

All conclusions are provided in good faith and are intended to assist in improving system understanding, transparency, and risk awareness.

---

Auditor:
James Nexus — Curve Bonds DAO Risk Evaluations

Date:
March 21, 2026

Version:
1.21


---

## 📌 Notes

- This report is provided for informational purposes only.
- It does not constitute financial advice or an endorsement.

---

## 🛡 Auditor

**James Nexus**  
Curve Bonds DAO Risk Evaluations

---

## 📅 Date

March 21, 2026
