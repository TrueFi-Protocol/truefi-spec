# TrueFi 2.0 Specification
 
TrueFi v2 is a series of upgrades to the original TrueFi specification. These upgrades improve and overhaul the TrueFi lending product, and focus on value accrual, decentralization, and automation. As the TrueFi roadmap is developed, upgrades will be made in phases synchronized with unlocked tokens entering circulation.
  
[Original Specification (Phase 1)](README.md)
  
### Terminology

**TRU Staking** - Depositing TRU into a smart contract in exchange for protocol fees and rewards.  
**stkTRU** - An ERC20 token which represents a share in the TRU staking pool.  
**tfLP** - An ERC20 token which represents a share of the TrueFi lending pool.  
**Liquidity Provider** - A party which provides liquidity to the TrueFi lending pool.  
**TRU Governance** - Decentralized voting using TRU to adjust protocol parameters and make upgrades.  
 
# Phase 2
 
Phase 2 will focus on value accrual for TRU holders and protocol governance. This phase will include a staking and governance smart contract. In this phase ownership of TrueFi will be transferred from the current multisig contract to a timelock contract. The staking contract will allow TRU holders to stake TRU to earn a share of loan origination fees.
 
## Staking
 
A staking functionality will be added which allows TRU holders to stake TRU in exchange for protocol fees. Depositing TRU into the staking contract will mint a new token for the staker called stkTRU. stkTRU is a standard ERC20 token that represents the depositor's share of the stkTRU pool. Holding stkTRU entitles an account to rewards for holding the asset over time. Rewards are claimed in the form of tfLP tokens and TRU.
 
Users will no longer rate loans with TRU directly, but rather will rate using stkTRU. stkTRU holders are taking on a blended risk of all loans. In essence, they are staking on the security of the borrower approval process. Raters will earn a small TRU reward when participating in the rating process.
If a loan is passed, all stakers earn the origination fee. In the future, when TrueFi moves to open-term loans, stakers will be entitled to origination fees and (upon governance approval) a percentage of the interest revenue. 
 
100% of origination fees (in the form of tfLP) will go towards stakers. In addition, stakers will receive TRU incentives every block. In the future when open-term loans are implemented, stakers may also receive a % of the interest generated. Stakers who vote on loans will also receive additional TRU incentives.
  
TRU staked in the protocol will be used as collateral in the case of loan defaults. In a default scenario, up to 10% of all staked TRU will be used to cover losses in the TrueFi pool. TRU stakers are taking on the risk of loan defaults in the protocol in exchange for the staking rewards. The percentage of TRU lost due to a default can be adjusted by governance. Liquidation events are determined automatically whenever a loan defaults.

In order to ensure stakers are committed to taking on the risk of loans defaulting, a 14 day cooldown will be required for unstaking. This ensures stakers cannot unstake right before a default event. To ensure fees from loans are paid fairly, 50% of origination fees are paid at the start of the loan, and 50% are paid when the loan is successfully repaid. This fee split incentivises stakers to be actively staked when a loan might default, and rewards stakers that hold stkTRU for a long period of time.
  
### Staking Parameters
- 14 day cooldown for unstaking  
- 2 day window to unstake after cooldown
- Staking more during cooldown increases cooldown time
- Maximum 10% penalty on loan defaults  
- 100% origination fees to all stakers  
- stkTRU required to approve loans  
- 50% of fees paid at start of loan
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

# Phase 3
  
## SUMMARY:
Phase 3 will focus on overhauling how uncollateralized lending works in TrueFi.
  
The first major upgrade will introduce multi-asset borrowing and liquidity pools for multiple assets. A new asset pool is created by passing an ERC20 token to a factory. This factory will deploy a new pool, and allow the TrueLender contract to borrow directly from this pool. LoanTokens can now support any ERC20 token and borrowers will repay the borrowed asset to the LoanToken contract when a loan term is completed. Each pool will have its own strategy for lending idle assets to other defi protocols to maximize yield.
  
Open term lending with lines of credit will be allowed for borrowers. TRU stakers will be able to allocate stake oton individual lines of credit to increase the credit available. Each borrower will be able to use their line of credit to borrow from the TrueFi pool. A credit model will be voted in by governance which calculates the credit limit and risk factors for each borrower. Tokenized, fixed term, fixed interest loans will still be available, but will automatically calculate the loan terms depending on borrower credit and pool liquidity.
  
## MULTI-ASSET BORROWING
  
- Create lending pools for any ERC20 token.
- Borrowers can apply to borrow one asset at a time
  
### Smart Contracts
  
#### TrueFiPoolFactory
* owner can update a whitelist which allows certain tokens
* new function: create(address token)
* create() deploys a new TrueFiPool
* create() registers new pool address
* create() can only create one pool per ERC20 token
* create() defaults to using no TrueFiStrategy
* public mapping of token address => pool address
* support precision of new tokens
* pool value is denominated in tokens (1.01 price = 1.01 ETH)
* ability to add price feed
  
#### TrueFiPool & TrueFiStrategy
* Pools will now include a TrueFiStrategy smart contract
* TrueFiStrategy is allowed to store idle funds in other defi contracts
* Pools all use liquidExit() as calculated the same way
* Use governance to add pools to the full TrueFi system
* Use governance to set TrueFiStrategy for a given pool
  
#### TrueLender
* withdraw from any TrueFi pool
* each loan has a single asset
* reclaim() transfers asset back to its respective asset pool
* TrueLender handles liquidations and transfers TRU to the asset pool a default occurred in
* Need a mapping (address => parameters) to track lending params
* register() function which sets up a new pool with default params
* need to calculate the amount of votes differently
  
#### LoanToken
* each LoanToken has a stored asset() that returns address of ERC20 borrowed
* call repay() to automatically pay back the loan with asset
* burning LoanTokens returns asset
* support precision of new tokens
  
#### TrueRatingAgency
* LoanTokens are created for specific assets and registered with TrueRatingAgency
* Keep incentives the same for rating any loan
* Store LoanTokens in same array we currently are
  
#### TrueFiStrategy
* allows transferring idle funds into defi opportunities
* withdraw idle funds, deposit into defi opportunity, transfer tokens to pool
* move funds out of opportunity when borrowers withdraw
  
### Liquidator
* In liquidate function, check LoanToken asset type, transfer slashed TRU to respective pool
* Support multiple price feeds for Liquidator
* Governance decides what pools liquidator can track
  
### TrueFiGauge & Updated Incentives
* Create a TrueFiGauge smart contract which will replace the existing tfTUSD farm
* Any valid lp token can be staked here. Each valid pool is assigned a weight, and distribution is weighted based on governance allocation of weights. At first all pools will be weighted equally.
* All current tfLP farm incentives will be migrated to the TrueFi gauge

# Phase 4

Phase 4 overhauls handling defaults and adds automatic interest rate pricing for borrowers. This phase is the most significant update for TrueFi and will likely be what the protocol looks like in the long term.

## SAFU (Secure Asset Fund for Users)

The SAFU is an overhaul of how TrueFi handles borrower defaults. In Phase 4, the SAFU contract is responsible for all bad debt accrued by the protocol. The SAFU can be funded by external parties (in this case, TrustToken) and will use its funds to help cover defaults. When a default occurs, TrueFi Lending Pools transfer all bad debt assets to the SAFU in exchange for the full expected value of those assets. The SAFU is responsible for slashing staked TRU tokens, up to 10% of the defaulted amount. If the value of these tokens is not enough to cover the default the SAFU can use its own funds to help repay the lending pool for lost funds.

### Handling Defaults
  
In the event of a default, the following occurs:  
1. Up to 10% of TRU is slashed from the staking pool and transferred to SAFU to cover the amount of default, equal to principal amount plus full amount of expected interest (“Defaulted Amount”)  
2. All LoanTokens for the defaulted loan are transferred from the Lending Pool to the SAFU  
3. If the current SAFU funds are insufficient to cover the Defaulted Amount:
    - SAFU can sell TRU for the respective borrowed asset at its manager’s discretion  
4. If the value of the SAFU funds(“Assurance Fund”) can not satisfy the Defaulted Amount:  
    - The difference between the Defaulted Amount and the Assurance Fund is calculated (“Uncovered Amount”)  
    - SAFU mints ERC-20 tokens representing claim for the Uncovered Amount (“Deficiency Claim”)  
    - Lending Pool receives a Deficiency Claim for the Uncovered Amount assuming successful recovery  
    - Lending Pool has a first priority claim on funds recouped through arbitration for the Deficiency Claim amount  
5. If a debt is repaid:  
    - The recouped funds will be used to purchase the asset that the Loan Token was originally denominated in, which will be transferred to the LoanToken contract  
    - SAFU burns Loan Tokens for the underlying value of those tokens (“Recovered Amount”)  
    - SAFU repurchases Deficiency Claim tokens from the Lending Pool up to the Recovered Amount  
    - If there are additional recovered funds after repurchasing the Lending Pool’s Deficiency Claim, the SAFU keeps those remaining funds  
6. If any portion of the original loan amount is not repaid after the completion of the legal recovery process:  
    - Lending Pool’s remaining Deficiency Claim tokens are burned (decrease LP price)  

### Default Algorithm

```
Smart Contracts:
    Liquidity Pool
    Staking Pool
    SAFU
    LoanToken
    StableCoin Token
    TRU Token
    DeficiencyToken
    Arbitration

Variables:
    L = Expected Value of LoanToken in Liquidity Pool
    P = Amount of Loan Value Repaid
    μ = Ratio of LoanTokens held in pool to total LoanToken supply
    S = StableCoin Balance in SAFU
    D = Loan Deficit = L - P
    R = Remaining USD Deficit
    T = Balance of TRU in Staking Pool
    U = USD Price of TRU
    K = Slash Ratio of Staking Pool
    M = Max USD Staking Coverage = U * T * K
    Y = Arbitration Repayment Amount

Algorithm:
    1. Transfer L LoanToken from Liquidity Pool to SAFU
    2. Calculate Loan Deficit
       D = L - μP
    3. Slash TRU
        a. Calculate Max Staking Coverage 
           M = U * T * K
        b. Transfer min(M, D)/U TRU from Staking Pool to SAFU
    4. If S >= L : 
       Cover the deficit using SAFU funds
        a. Calculate R = 0
        b. Transfer L StableCoin from SAFU to Liquidity Pool
    5. If S < L :
       SAFU funds are insufficient to cover
        a. Calculate R = L - S
        b. Transfer S StableCoin from SAFU to Liquidity Pool
    6. If (R > 0) :
       Deficit is still not covered
        a. Calculate D’ = R
        b. Add D’ deficiency value to loanDeficiency mapping and poolDeficiency mapping
    7. If (Arbitration Complete) :
       Arbitration completes with Y StableCoin
        a. Transfer Y StableCoin from Arbitration to LoanToken
        b. SAFU burns L LoanToken for μ(Y+P) StableCoin
        c. Subtract D’ deficiency value from loanDeficiency mapping and poolDeficiency mapping
```
  
### Smart Contract Architecture
  
SAFU will effectively replace what was called “Liquidator”. SAFU will have the permission to slash TRU according to the above algorithm. Funds in the SAFU will be managed by an approved address, automating as much capital management as possible through defi. The initial version of SAFU funds management will be somewhat centralized. Allowing centralized management of SAFU funds is useful in order to maximize the capital efficiency when making exchanges between tokens. For example, the price impact of exchanging TRU on decentralized exchanges is much higher than the impact of OTC or centralized exchange opportunities. In following the ethos of progressive decentralization, future phases will include updates to the SAFU which remove the dependency on centralized parties to manage SAFU funds.