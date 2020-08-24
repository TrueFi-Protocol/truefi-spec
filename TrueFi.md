# TrueFi Specification
TrueFi is a protocol for creating interest bearing pools with a high APR for liquidity providers. TrueFi includes a staking mechanism using TrustTokens (TRU) and rewards stakers for keeping stable, high APRs. Un-collateralized loans and decentralized lending are utilized in order to achieve high interest rates for pools.

TrueFi is a product that we are actively designing and this document is intended to act as a plan and specification for design. We are aiming to launch TrueFi on the Ethereum mainnet by the end of November 2020.

## Terminology

**Token** - An ERC20 token running on the Ethereum blockchain.
**Stablecoin** - A token which is pegged to another asset.  
**DeFi** - Decentralized finance using smart contracts.  
**Pool** - A smart contract which allows accounts to pool tokens with the goal of earning interest.  
**Deposit Tokens** - Tokens deposited into a pool which are used to earn interest through lending or arbitrage opportunities.  
**Pool Tokens** - Tokens issued by a pool to represent share in the pool's deposit tokens.  
**TrustToken** - An ERC20 token used to provide utility in the TrueFi ecosystem.  
**TrueUSD** - A stablecoin used frequently in TrueFi to provide liquidity.

# Background

While a lot of DeFi’s success so far has been built on collateralized lending (Compound, AAVE, etc.), many people on the cutting edge of DeFi see that for DeFi to get to its next level of success, it will need to be able to tap into un-collateralized lending as well. Innovative projects such as AAVE are already making moves in this direction.

Reputable institutions and crypto hedge funds funds such as Alameda, Three Arrows, etc. are offering 8-12% for large un-collateralized loans. These rates are higher than what’s available in DeFi today, primarily because DeFi today does almost exclusively collateralized lending (which borrowers will systematically pay less for)
Most individual investors do not have access to invest in funds of this type because their check-size is too small. Many of these funds are only interested in loans of $1mm at the very minimum.
By keeping un-deployed capital in DeFi, TrueFi can offer an interest rate that is at least as high as the highest rate available in DeFi today. It will only make loans when the proposed rate exceeds this rate.
TrueFi can use TrustToken incentive rewards to bootstrap growth similar to how Compound used $COMP
Down the road, TrueFi can expand to a wide set of borrowers as its systems for assessing and managing risk get more sophisticated

# TrueFi Pools

A TrueFiPool is an ERC20 contract with additional functions to support providing liquidity and staking TrustTokens.

### TrueFiPool Functions

**value()** 
- returns the value of one pool token

**join(amount)**
- called to join a TrueFi pool by depositing an amount of deposit tokens and minting pool tokens 
1. transfer deposit tokens from sender
2. mint pool tokens to sender equal to amount of deposit tokens 

**exit(amount)** 
- called to exit a TrueFi pool by burning pool tokens in exchange for deposit tokens 
1. transfer pool tokens from sender and burn 
2. transfer deposit tokens to sender based on amount of pool tokens burned 

**stake(amount)**
- stake TRU on a pool

**unstake(amount)**
- unstake TRU from a pool

# LoanToken

A LoanToken is an ERC20 contract which represents the lender share of an under-collateralized loan. Creating a loan requires a borrower address, principal loan amount, loan length, and interest rate.

## Lifecycle



### LoanToken Functions

**constructor(borrower, principal, length, rate)**
- create a new loan opportunity
- owner of token is deployer, and only owner can approve the loan

**balance()** 
- get balance of deposit tokens

**value()** 
- get value of principal plus interest

**deposit(amount)** 
- transfer deposit tokens to this contract and mint loan tokens
- can only be called during fund-raising period

**redeem(amount)** 
- redeem and burn loan tokens, withdraw deposit tokens
- can only be called after loan expiry

**approve()** 
- approve loan
- can only be called if enough funds are raised
- can only be called during fund-raising period

**pay()**
- pay back loan by transferring deposit tokens to pool
- can only be called once loan is active

# Planned TrueFi Pools

## Pool 0
Pool 0 will be the first TrueFi pool which simply exposes depositors to the curve.fi pool. Pool 0 will be used to beta test TrueFi and liquidity mining.

## Pool 1: Un-collateralized Lending Pool
Pool 1 is a TrueFi pool which exposes depositors to uncollateralized loans. A select set of institutional borrowers will be chosen for this pool in order to keep the pool initially secure. Borrower addresses will be whitelisted and will be the only addresses which can request loans.

### Applying for a loan

Depositors put TrueUSD or other TrueCurrencies into a pool
Borrowers (such as Alameda, Three Arrows, etc.) can submit proposals to borrow capital from the pool. They submit how much capital they want, the % APR, the term, and the Ethereum address to send the capital to if the proposal is approved. 
These proposals are voted on by TrustToken holders. Voting YES on a proposal stakes your TrustTokens on that loan and exposes you to upside & downside from it.
If a proposal is approved, the capital is given as an uncollateralized loan to the borrower
The borrower must return the capital before the term is up. If they return the full amount including interest, TrustToken voters that voted YES on the proposal receive a reward. If they fail to return the full amount including interest, TrustToken voters that voted YES on the proposal will have some of their TrustTokens burned. 
Capital that is not actively being loaned out will be sitting in curve.fi or another DeFi product where it can earn a high interest rate

### Approving a loan


### Withdrawing funds after loan expiry

Capital that is not locked in a loan will be deposited into the curve.fi y pool. This guarantees that capital which is not loaned out is always earning interest. When the pool approves a loan

### Deposit
Upon deposit, given depositAmount
Compute value of entire pool, assuming all loans are good
For each active loan:


Calculate depositor share as depositAmount * 
Mint

### Withdraw:

Get a percentage of loan tokens based on your liquidity share
Going in/out locks you into a bunch of loans

Stake:
- TRU reward for staking / voting based on Amount of TUSD in interest
- Stake THEN Vote
- Vote YES or NO
- Ways to approve loans
    -> Based on TRU staked
    -> Votes needed to approve a loan based on size of loan
    -> Same amount of votes required to approve any loan
    -> Constant margin required between YES and NO
- Amount of time to vote on loans
    -> What is the ideal amount of time
- Reward voting in general

Pools Parameters:
- "risk", "signal risk profile"
- incentive factor
- minimum / maximum loan size (min_loan_size, max_loan_size)
- minimum / maximum interest rate
- amount of time needed to vote
- margin needed to vote
- whitelisted addresses that can create loans
- where & at what interest rate to deposit into defi

What makes an amazing product?
- What interest rate can we offer. Success/failure depends on *stable* interest rate
- 

Further Ideas:
- Create uniswap market for pool tokens
- Swapping pool tokens effectively at market price
- 


Distribution:
f(t, n) = (t-5)^2 * (n / 25,000,000)
t = time in years
n = number of TRU allocated for incentive distribution


