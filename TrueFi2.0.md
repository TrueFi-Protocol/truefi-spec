# TrueFi 2.0 Specification
 
TrueFi v2 is a series of upgrades to the original TrueFi specification. These upgrades improve and overhaul the TrueFi lending product, and focus on value accrual, decentralization, and automation. As the TrueFi roadmap is developed, upgrades will be made in phases synchronized with unlocked tokens entering circulation.
  
[Original Specification](README.md)
  
### Terminology

**TRU Staking** - Depositing TRU into a smart contract in exchange for protocol fees and rewards.  
**stkTRU** - An ERC20 token which represents a share in the TRU staking pool.  
**TFI-LP** - An ERC20 token which represents a share of the TrueFi lending pool.  
**Liquidity Provider** - A party which provides liquidity to the TrueFi lending pool.  
**TRU Governance** - Decentralized voting using TRU to adjust protocol parameters and make upgrades.  
 
# Phase 1
 
Phase 1 will focus on value accrual for TRU holders and protocol governance. This phase will include a staking and governance smart contract. In this phase ownership of TrueFi will be transferred from the current multisig contract to a timelock contract. The staking contract will allow TRU holders to stake TRU to earn a share of loan origination fees.
 
## Staking
 
A staking functionality will be added which allows TRU holders to stake TRU in exchange for protocol fees. Depositing TRU into the staking contract will mint a new token for the staker called stkTRU. stkTRU is a standard ERC20 token that represents the depositor's share of the stkTRU pool. Holding stkTRU entitles an account to rewards for holding the asset over time. Rewards are claimed in the form of TFI-LP tokens and TRU.
 
Users will no longer rate loans with TRU directly, but rather will rate using stkTRU. stkTRU holders are taking on a blended risk of all loans. In essence, they are staking on the security of the borrower approval process. Raters will earn a small TRU reward when participating in the rating process.
If a loan is passed, all stakers earn the origination fee. In the future, when TrueFi moves to open-term loans, stakers will be entitled to origination fees and (upon governance approval) a percentage of the interest revenue. 
 
100% of origination fees (in the form of TFI-LP) will go towards stakers. In addition, stakers will receive TRU incentives every block. In the future when open-term loans are implemented, stakers may also receive a % of the interest generated. Stakers who vote on loans will also receive additional TRU incentives.
  
TRU staked in the protocol will be used as collateral in the case of loan defaults. In a default scenario, up to 10% of all staked TRU will be used to cover losses in the TrueFi pool. TRU stakers are taking on the risk of loan defaults in the protocol in exchange for the staking rewards. The percentage of TRU lost due to a default can be adjusted by governance. Liquidation events are determined automatically whenever a loan defaults.

In order to ensure stakers are committed to taking on the risk of loans defaulting, a 14 day cooldown will be required for unstaking. This ensures stakers cannot unstake right before a default event. To ensure fees from loans are paid fairly, 50% of origination fees are paid over the time of the loan, and 50% are paid when the loan is successfully repaid. This fee split incentivises stakers to be actively staked when a loan might default, and rewards stakers that hold stkTRU for a long period of time.
  
### Staking Parameters
- 14 day cooldown for unstaking  
- 2 day window to unstake after cooldown
- Staking more during cooldown increases cooldown time
- Maximum 10% penalty on loan defaults  
- 100% origination fees to all stakers  
- stkTRU required to approve loans  
- 50% of fees paid over time of loan
- 50% of fees paid when loan is repaid
 
## Governance
 
Governance in the TrueFi ecosystem is critical to achieving full decentralization. Currently the smart contracts are owned by a multisig, and upgrades are decided on via forum discussion and snapshot voting. Upgrades are then executed by multisig members. Holders of both TRU and stkTRU will be able to vote in governance. Governance will decide when to adjust protocol parameters or make smart contract upgrades.
 
Important aspects of governance are as follows:
* Governance ownership of smart contracts
* Creating & voting on proposals
* Executing code on-chain
* Allowing for voting using both TRU and staked TRU
 
Differences from Compound include:
* Ability to delegate votes with TRU and staked TRU
* Disallowing voting with locked TRU
 
### Governance Parameters

* Voting period: 3 days
* Proposal threshold: 100,000 TRU/stkTRU
* Min & Max Time-lock delay: 2 days * 30 days
* Quorum Votes: 10,000,000
* Proxy contract & governance will be upgradable, subject to governance
* Will have a ‘guardian’ at first
* Guardian can be set to 0 through governance vote
* Governance will be the timelock admin

# Phase 2

Phase 2 will focus on overhauling how uncollateralized lending works in TrueFi. Rather than having TRU stakers approve individual loans, TRU holders will be able to stake on individual lines of credit. Each borrower will be able to use their line of credit to borrow from the TrueFi pool. A credit model will be voted in by governance which calculates the credit limit and risk factors for each borrower. Tokenized, fixed term, fixed interest loans will still be available, but will automatically calculate the loan terms depending on the credit model and pool liquidity.

