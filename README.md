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

TrueFi can use TrustToken incentive rewards to bootstrap growth similar to how Compound used $COMP. Down the road, TrueFi can expand to a wide set of borrowers as its systems for assessing and managing risk get more sophisticated.

# LoanToken

A LoanToken is an ERC20 contract which represents the lender share of an under-collateralized loan. LoanTokens are an important building block of TrueFi, as they can operate independently from the TrueFi pools. Creating a LoanToken requires a borrower address, principal loan amount, loan length, and interest rate.

## LoanToken Lifecycle

#### I. Fundraising
    - Mint/Burn Loan Tokens is enabled
    - Depositors can send deposit tokens to contract
        -> mint LoanTokens equal to deposit
    - Can never deposit more than the principal
        -> Return amount over principal if sending more than principal
    - Fundraising is complete once we reach principal amount
    - During fundraising, can exchange LoanTokens for deposit tokens
        -> Funds aren't locked until Lending Phase

#### II. "Approval"
    - Once smart contract has raised the principal amount of deposit tokens:
        -> Borrower can call a function to approve the loan
        -> Approving the loan withdraws funds to borrower address
        -> Loan expiry is set to: block.timestamp + length
        -> Mint/Burning Loan tokens is disabled

#### III. "Lending"
    - Here we're waiting for block timestamp > expiry
    - Mint/Burning Loan tokens is disabled
    - Borrower can pay back deposit tokens at any time (but cannot withdraw)

#### IIII. "Complete"
    - Once block timestamp > expiry, we enter the complete phase
    - Burning enabled (Minting disabled)
    - LoanTokens can be burned in exchange for a share of the deposit tokens in contract
    - Ideally borrower would have paid back (principal + principal * rate)

## LoanToken Functions

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

# TrueFi Pools

A TrueFiPool is an ERC20 contract with additional functions to support providing liquidity and staking TrustTokens. TrueFi Pools are another building block of TrueFi, allowing pool depositors to receive pool tokens which represent their share of a pool.

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

# Planned TrueFi Pools

## Pool 0
Pool 0 will be the first TrueFi pool which simply exposes depositors to the curve.fi pool. Pool 0 will be used to beta test TrueFi and liquidity mining.

## Pool 1: Un-collateralized Lending Pool
Pool 1 is a TrueFi pool which exposes depositors to un-collateralized loans. A select set of institutional borrowers will be chosen for this pool in order to keep the pool initially secure. Borrower addresses will be whitelisted and will be the only addresses which can request loans.

#### Applying for a loan

Depositors deposit TrueCurrencies into a pool. Borrowers can submit proposals to borrow capital from the pool. They submit how much capital they want, the % APR, the term, and the Ethereum address to send the capital to if the proposal is approved. 

#### Approving a loan

Loan proposals proposals are voted on by TrustToken holders. Voting YES on a proposal stakes your TrustTokens on that loan and exposes you to upside & downside from it. If a proposal is approved, the capital is given as an un-collateralized loan to the borrower
The borrower must return the capital before the term is up. If they return the full amount including interest, TrustToken voters that voted YES on the proposal receive a reward. If they fail to return the full amount including interest, TrustToken voters that voted YES on the proposal will have some of their TrustTokens burned.  Capital that is not actively being loaned out will be used to provide liquidity in safe, high-interest rate defi smart contracts (such as Aave or curve.fi).

#### Paying back a loan

A borrower can pay back their loan at any time before the expiry date. Borrowers are expected to pay back principal + (principal * interest).

#### Withdrawing funds after loan expiry

Once a loan is past its expiry date, a function will allow LoanTokens held by the pool will be exchanged for the underlying deposit tokens. Assuming a loan is paid back in full, the amount of deposit tokens will be the principal amount plus interest.

### Pool Parameters

Pool #1 will have several initial parameters:

- Minimum loan size: 1M TUSD
- Maximum loan size: 10M TUSD
- Minimum % APR: 10%
- Maximum % APR: 20%
- Minimum Term: 30 days
- Maximum Term: 90 days
- Assets: TUSD only
- Idle Funds usage: 100% CRV

# TRU Distribution

39% of TRU will be distributed to liquidity providers (LPs) over 4 years. The TRU intended to be distributed to LPs will be sent to a smart contract which is owned by a multisig. The multisig will have control over the smart contract, otherwise the tokens cannot be spent. The multisig can approve transfers to smart contracts which will hold funds for liquidity mining.

Max Token Supply: 1,450,000,000

|                 |         |               |                   |
|-----------------|---------|---------------|-------------------|
| Private Sale    | 28.64%  | 415,314,034   | 4 year vesting    |
| Team            | 18.50%  | 268,250,000   | 4 year vesting    |
| Future Team     | 4.50%   | 65,250,000    | 4 year vesting    |
| Incentive       | 39.00%  | 565,500,000   | LPs & stakers     |
| Company         | 9.36%   | 135,685,966   | unlocked          |
|                 |         |               |                   |

## Liquidity Mining

Of the 565M TRU to be used for incentives, we plan on distributing through multiple channels:

|        |                 |             |               |
|--------|-----------------|-------------|---------------|
|        | Third Party LPs | TrueFi LPs  | TRU Stakers   |
| Ratio  | 0.10            | 0.4         | 0.5           |
| Time   | 6 Months        | 4 years     | 4 years       |
| Total  | 56,550,000      | 226,200,000 | 282,750,000   |
|        |                 |             |               |

These values were chosen in order to distribute TRU to early adopters of the protocol, and incentivise providing liquidity for TRU pairs on DEXs such as Uniswap and Balancer in the short term. We also want to reward long term users of the protocol by providing the majority of incentive distribution to users of TrueFi.

There will be two types of distributions: one for providing liquidity on external market making protocols, and one for providing liquidity on TrueFi. These will be handled with similar but slightly different mechanisms.

To farm TRU by providing liquidity outside of TrueFi, LP tokens need to be staked in exchange for farm tokens (see below). Holders of farm tokens can claim rewards in TRU as incentive for providing liquidity on external markets. Markets considered are:

- Uniswap ETH/TRU
- Uniswap TrueFi LP / TUSD
- Balancer 95% BAL / 5% TRU

To farm TRU by providing liquidity within TrueFi, accounts will simply deposit tokens into one of the TrueFi pools. In addition to providing liquidity, TRU holders can stake TRU and vote to approve/disapprove loans within the LendingPool. Ways to farm TRU on TruFi include:

- Providing Liquidity for a TrueFi pool
- Voting & staking on loans in the Lending pool

## Distribution Smart Contracts

 LP tokens such as pool tokens, Uniswap LP tokens, etc. can be staked on any TrueFarm contract to earn TRU. Depositors of LP Tokens will be able to claim TRU rewards for staking.

### TrueFarm Contract

A TrueFarm contract is an interface which allows any smart contract to reward TRU.

### Distributor Contract

The distributor contract will be transferred the portion of TRU intended to be distributed. This ensures that the supply is permanently locked into the contract and can help calculate the circulating supply for oracles and price-tracking websites (such as CoinGecko or CoinMarketCap). The contract will only allow a certain amount of TRU to be withdrawn for distribution based on a function, guaranteeing the distribution formula is set over time.

### Future Incentives & Considerations

Because TRU distribution will eventually run out, an alternative to reward voters on successful loans could be to award a percentage of the loan interest to stakers, thus creating an incentive to continue voting after TRU is distributed. Another option could be to introduce an inflationary model.
