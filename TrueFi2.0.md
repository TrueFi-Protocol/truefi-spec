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
  
# Phase 4

Phase 4 overhauls handling defaults and adds automatic interest rate pricing for borrowers. This phase is the most significant update for TrueFi and will likely be what the protocol looks like in the long term. Phase 4 also moves all farming to a single Liquidity Gauge contract to handle scaling farms in TrueFi.

## Liquidity Gauge / TrueMultiFarm
A smart contract will launch which will replace the existing tfLP farms. Any valid tfLP token can be staked here. Each valid pool is assigned a weight, and distribution is weighted based on governance allocation of weights. At first all pools will be weighted equally. All current tfLP farm incentives will be migrated to the Liquidity Gauge. In this phase, snapshot votes will decide the allocation points, and a single weekly distribution of TRU for all farms. This model greatly simplifies farming TRU and scales for any number of assets.

## SAFU (Secure Asset Fund for Users)

The SAFU is an overhaul of how TrueFi handles borrower defaults. In Phase 4, the SAFU contract is responsible for all bad debt accrued by the protocol. The SAFU can be funded by external parties and will use its funds to help cover defaults. When a default occurs, TrueFi Lending Pools transfer all bad debt assets to the SAFU in exchange for the full expected value of those assets. The SAFU is responsible for slashing staked TRU tokens, up to 10% of the defaulted amount. If the value of these tokens is not enough to cover the default the SAFU can use its own funds to help repay the lending pool for lost funds.

### Handling Defaults for Fixed Term Loans
  
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
  
### SAFU Smart Contract Architecture
  
SAFU will effectively replace what was called “Liquidator”. SAFU will have the permission to slash TRU according to the above algorithm. Funds in the SAFU will be managed by an approved address, automating as much capital management as possible through defi. The initial version of SAFU funds management will be somewhat centralized. Allowing centralized management of SAFU funds is useful in order to maximize the capital efficiency when making exchanges between tokens. For example, the price impact of exchanging TRU on decentralized exchanges is much higher than the impact of OTC or centralized exchange opportunities. In following the ethos of progressive decentralization, future phases will include updates to the SAFU which remove the dependency on centralized parties to manage SAFU funds.

## Lines of Credit

Lines of Credit (LOC) is a major step for automating lending and interest rate calculation in TrueFi. In this upgrade, borrowers with high enough credit scores will be able to open a line of credit with a credit limit. This will allow a borrower to withdraw and repay debt at any time with a variable rate rather than submitting requests for fixed term loans. Lines of credit and term loans are funded from the same liquidity pools and each have their own token representing unique risk and attributes. Interest rates will be calculated automatically for fixed and variable term loans based on the Credit Oracle.

### Mitigating Risk

It’s important to take measures which protect lenders from downside. In addition, Line of Credit mechanisms need to be future-proof and flexible to support future changes such as new models or inputs. This current design assumes that a TrueFi credit score is the most important factor in calculating borrower credit limits and risk. Therefore, it is critical to calculate and closely monitor these scores as a more decentralized solution is built.

Some centralized steps to lower risk include:
- Regular check-ins on borrower health
- Ability to reduce borrowers’ credit limits at any time
- Borrower agreement legal covenants
- Only allow “high-quality” borrowers with high credit scores access to Credit Lines

Some of the main risks associated with Lines of Credit include:
- Borrowers’ creditworthiness is changing in real-time depending on their trading positions
- Market volatility
- Lack of real-time credit monitoring
- Borrowers attempting to withdraw funds quickly to cover positions elsewhere

#### Loan Term

Reduced term is instituted in order to lower risk by requiring borrowers with a line of credit to repay their full balance every term and establish repayment history. The term for lines of credit in TrueFi will be 365 days, starting when the line of credit request is approved. This number can be increased as stability of the product is established.

### Credit Oracle

A smart contract is utilized as an oracle which returns credit scores, borrow limits, and borrow eiligibility. Until credit scores are automated in a trustless way, these parameters will be written by a manager to the smart contract. This way, accurate and effective scores can be implemented now, and in the future the same interface & contract can be used to provide trustless borrower data. 

Borrowers that are "on hold" are temporarily suspended from borrowing from TrueFi. Any borrower that does not have their score updated within 30 days or misses a 30 day payment will automatically be put on hold. Borrowers can be marked as "ineligible" by the contract owner, and ineligible borrowers will not be able to borrow from TrueFi. Borrowers marked as ineligible will be open to enter technical default once marked.

### Interest Rates Overview

Interest rates will be calculated automatically based on a set of inputs including borrower credit scores. Borrowers will have the ability to take out loans calculated on a fixed term, fixed interest basis and a line of credit which is variable term, variable interest. Interest accrues on a per-block basis, increasing the virtual price of a lending pool as profit is realized.

Interest for lines of credit accumulates per block on the principal borrow based on the variable rate. Borrowers utilizing lines of credit are required to pay the interest balance every 30 days.

##### Borrow Limit

Credit limits and borrower scores are calculated off-chain and written to a smart contract. Borrow limits are calculated based on a credit limit and credit score. Limits are adjusted by a multisig and written to a smart contract. Limits must be calculated off-chain in order to keep borrower information private.

A borrower cannot borrow more than 15% from any pool or more than their remaining credit limit. This is to both limit the risk of a single borrower defaulting in any pool, and manage the maximum amount a default can affect the entire protocol.

A max_borrow_limit and credit_score for each borrower are written by a multisig and stored on-chain. An adjustment_ratio is calculated which decides how much to reduce credit limits for borrowers with lower scores. For each borrower, their credit_limit is calculated by multiplying max_credit_limit * adjustment ratio.

Finally we calculate a pool_borrow_max, which represents the maximum a borrower can borrow from a single pool. Each borrower will have its own maximum borrow from each pool. A borrower cannot borrow more than their remaining credit limit or 15% of a single pool.

#### Borrow Rates

Borrow rates for fixed and variable term loans are calculated on-chain. Starting with a base rate collected from the Aave and Compound markets, rates are adjusted based on utilization and credit scores. A lower credit score causes higher interest rates to borrow, as do higher pool utilizations. These adjustments are based on inverse power function models with tunable parameters. Parameter tuning can allow for targeting particular creditworthy borrowers or pool utilization levels.

The final_rate is equal to the base adjusted for utilization and credit score. We start with a base_rate which is a secured_rate + risk_premium. The secured_rate is the average 7 day borrowing rate for the Aave USDC market. The risk_premium is a value set by a multisig representing a premium for uncollateralized lending.

Once the base rate is calculated, we then perform some calculations per pool, and per borrower, to adjust for pool utilization and credit scores. The general idea of these calculations is to incentivise borrowing from pools with low utilization, and punish borrowers with low credit scores.

The utilization_adjustment is a formula which increases the interest rate for a specific pool. Pools with utilization above 50% have an increasing rate. Utilizations below 50% have a minimum effect on interest rates. Pools with utilization above 80% have a rapidly increasing rate.

The credit_adjustment is a formula which increases the interest rate for borrowers with low credit. Having the highest credit score (255) will give you a 0% increase in interest rates. Then, every point lower than 255, interest rates increase by a small percentage. Borrowers with a score below 159 have a major increase in interest rates.

Once all these values are calculated, we can get the final rate:

final_rate = base_rate + utilization_adjustment + credit_adjustment

#### Stable Borrowing

Rates for fixed-term loans are calculated using the stable borrow rate. The stable rate is the final rate adjusted based on the term length of a loan.

We add to the final_rate a time_adjustment, which increases the interest rate for stable borrowing by 1% per 30 days. We then calculate the stable rate:

stable_rate = final_rate + time_adjustment

#### Handling Deficiency in Lines of Credit

If a borrower’s limit is dropped and they owe more than their limit, they will be given a cure period to repay their balance to below their new credit limit. If a borrower cannot repay, The SAFU will attempt to purchase the debt tokens from the lending pool in the same way it would with fixed-term loans. (see: SAFU Specification)

### Interest Rates Calculations

In order to calculate interest rates on Ethereum, every time an input to an interest rate equation changes, the interest amount owed to lenders needs to be re-calculated. For a protocol such as Aave, this is straightforward, since every borrower in an Aave pool pays the same interest rate. For TrueFi, where interest rates vary between borrowers, this calculation becomes slightly more complicated. Therefore, TrueFi Pools will keep track of 2 types of interest for Lines Of Credit:

- A base rate paid by all variable rate borrowers
- A rate per-borrower (see buckets section below)

#### Buckets

Buckets are a solution designed to make lines of credit scalable. Every borrower is placed in a "bucket" consisting of all the possible credit scores. Credit scores are stored in the oracle as a uint8, which means the range of credit scores are 0-255. In order to be able to efficiently calculate the scores for a high number of borrowers, borrowers are grouped based on their score. When a change is made to utilization or rates, that change is applied to all borrowers with the same score. Thus, if 3 borrowers have a score of 192, all 3 borrower interest rates can be updated at the same time instead of updating 3 different scores.

#### Borrow Limit

Credit limits and borrower scores are calculated off-chain and written to a smart contract. Borrow limits are calculated based on a credit limit and credit score. A borrower cannot borrow more than 15% from the TVL of all pools or more than their max borrow limit (after adjusting for credit score).

```
max_borrower_limit = [written on-chain by multisig]

max_pool_limit = 0.15 * truefi_tvl

credit_limit = min(max_borrower_limit, max_pool_limit) * limit_adjustment

limit_adjustment = borrower_score < score_floor ? 0 : (borrower_score/MAX_CREDIT_SCORE)^limit_adjustment_power

    score_floor = 40
    MAX_CREDIT_SCORE = 255
    limit_adjudtment_power = 0.75

pool_borrow_max = min(0.15 * pool_value, credit_limit)

remaining_credit_limit = pool_borrow_max - total_borrow
```

#### Borrow Rates

Starting with a base rate, rates are adjusted based on utilization and credit scores. Higher utilization in a lending pool causes higher interest rates to borrow based on an inverse power model. Score adjustment uses an inverse power model.

```
risk_premium = [value set by governance]

secured_rate = [returned by credit oracle]

base_rate = secured_rate + risk_premium

final_rate = min(rate_ceiling, base_rate + utilization_adjustment + credit_adjustment)

    rate_ceiling = 500.0%

utilization_adjustment = utilization_adjustment_coefficient * (1/(pool_liquid_ratio)^utilization_adjustment_power - 1)

    utilization_adjustment_coefficient = 0.50%
    utilization_adjustment_power = 2

credit_adjustment = credit_adjustment_coefficient * ((MAX_CREDIT_SCORE/borrower_score)^credit_adjustment_power - 1)

    MAX_CREDIT_SCORE = 255
    creditAdjustment_coefficient = 10.00%
    credit_adjustment_power = 1
```

#### Stable Borrowing

Rates for fixed-term loans are calculated using the stable borrow rate. A stability adjustment is added to incentivise borrowers to take out shorter term loans.

```
term_days = term length in days

stability_adjustment_coefficient = [value set by governance]

stability_adjustment = (term_days / 30) * stability_adjustment_coefficient

stable_rate = final_rate + stability_adjustment
```

# Phase 5

Phase 5 will mainly focus on improving lines of credit. This phase also includes some smaller changes to token distribution and governance. The phase 4 version of lines of credit we will refer to as a "pilot" or LOC v0. This version will be launched with an initial set of borrowers (or one borrower) in order to test the feature and get feedback.

We will refer to Lines of Credit as "LOC" and Fixed Term Loans as "FTL". The difference between the two is LOC allow borrowers to borrow at any time, up to their limit, as tracked in a smart contract. FTL allow borrowers to create discrete, tokenized loans with a fixed term and fixed interest rate.

## Lines of Credit v1

LOC v1 is not only an overhaul of lines of credit, but also an overhaul of how fixed term loans (FTL) function.

### Automatic Caulcation of Borrow Rates

The main feature of Phase 5 is that all interest rates will be calcuated automatically. At the time of drawing down a line of credit, a borrower will be bucketed with other borrwers that have the same score. The smart contract called Credit Model calculates the interest rates for both LOC and FTL.  

### Handling Defaults for Lines of Credit

Handling defauls for LOC is similar to fixed term loans with a few changes.
  
In the event of a LOC default, the following occurs:  
1. Up to 10% of TRU is slashed from the staking pool and transferred to SAFU to cover the amount of default, equal to principal amount plus full amount of expected interest (“Defaulted Amount”)  
2. If the current SAFU funds are insufficient to cover the Defaulted Amount:
    - SAFU can sell TRU for the respective borrowed asset at its manager’s discretion  
3. If the value of the SAFU funds(“Assurance Fund”) can not satisfy the Defaulted Amount:  
    - The difference between the Defaulted Amount and the Assurance Fund is calculated (“Uncovered Amount”)  
    - SAFU mints ERC-20 tokens representing claim for the Uncovered Amount (“Deficiency Claim”)  
    - Lending Pool receives a Deficiency Claim for the Uncovered Amount assuming successful recovery  
    - Lending Pool has a first priority claim on funds recouped through arbitration for the Deficiency Claim amount  
4. If a debt is repaid:  
    - The recouped funds will be used to re-purchase Deficiency Claim tokens from the lending pool which the default occurs in   
    - SAFU repurchases Deficiency Claim tokens from the Lending Pool up to the Recovered Amount. Those tokens are burned by the Lending Pool  
    - If there are additional recovered funds after repurchasing the Lending Pool’s Deficiency Claim, the SAFU keeps those remaining funds  
5. If any portion of the original loan amount is not repaid after the completion of the legal recovery process:  
    - Lending Pool’s remaining Deficiency Claim tokens are burned (decrease LP price)
    - Losses are incurred in the Lending Pool    

### Smart Contract Design

#### Credit Model

The Credit Model module calculates interest rates for LOC and FTL. The adjuster works according to the Phase 4 specification for calculating interest rates, taking into account the borrow limit and credit scores for each borrower.  

#### BorrowMutex

BorrowMutex is a smart contract designed to restrict which products (LOC, FTL) a borrower can use. A borrower can choose between either a line of credit (LOC), or a fixed-term loan (FTL). The mutex tracks which product a borrower is using, and restricts the borrower from using the other product if they are already using one. A borrower can have one LOC per pool, or one FTL per pool. Between all pools, a borrwer can borrow up to their credit limit as calculated by the Credit Model.  

#### DebtToken  

DebtToken is a token which represents a claim on future funds from the SAFU. DebtTokens are used as an accounting method for tracking bad debt owned by a lending pool. When a default occurs in FTL or LOC, DebtTokens are minted by the SAFU and transferred to the lending pool.  
