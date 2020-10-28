# TrueFi Specification

TrueFi is a protocol for creating interest bearing pools with a high APR for liquidity providers. TrueFi includes a utility and rewards mechanisms using TrustTokens (TRU) and rewards participants for maintaining stable, high APRs. Un-collateralized loans and decentralized lending are utilized in order to achieve high interest rates for pools.

TrueFi is a product that we are actively designing and this document is intended to act as a plan and specification for design. TrueFi is being developed by [TrustLabs](https://www.trusttoken.com/) in partnership with [EthWorks](https://ethworks.io/). TrueFi is aiming to be released on Ethereum mainnet November 22nd 2020.

### Terminology

**Token** - An ERC20 token running on the Ethereum blockchain.  
**Stablecoin** - A token which is pegged to another asset.  
**DeFi** - Decentralized finance using smart contracts.  
**Pool** - A smart contract which allows accounts to pool tokens with the goal of earning interest.  
**Deposit Tokens** - Tokens deposited into a pool which are used to earn interest through lending or arbitrage opportunities.  
**Pool Tokens** - Tokens issued by a pool to represent share in the pool's deposit tokens.  
**TrustToken** - An ERC20 token used to provide utility in the TrueFi ecosystem.  
**TrueUSD** - A stablecoin used frequently in TrueFi to provide liquidity.  
**Prediction Market** - Exchange-traded market created for the purpose of trading the outcome of events.

# Overview

A lot of DeFi’s success so far has been built on collateralized lending. Platforms such as  Compound, AAVE, and Maker have created a solid foundation which allows cryptocurrency holders to earn high interest rates in a permissionless manner. However, there is a massive opportunity for uncollateralized loans to yield even higher interest rates than existing collateralized lending protocols. Innovative projects such as AAVE are already building tools in this direction [credit delegation](https://docs.aave.com/developers/developing-on-aave/the-protocol/credit-delegation).

Based on a small survey of partners, TrustLabs found that reputable institutions and crypto hedge funds funds are willing to offer 8-12% interest rates for large un-collateralized stablecoin loans. These rates are higher than what’s available in DeFi today, primarily because DeFi today does almost exclusively collateralized lending (which borrowers will systematically pay less for). Most retail investors do not have access to invest in funds of this type because their check size is too small. By sourcing funds via deposits in a smart contract, TrueFi can open up uncollateralized lending opportunities to investors of any size. 

TrueFi consists of four major pieces:  
1. Tokenized Loans  
2. Lending Pools  
3. Credit Prediction Market  
4. TrustToken (TRU)  

### Tokenized Loans (LoanToken)

A LoanToken is an ERC20 contract which represents the lender share of an uncollateralized loan. LoanTokens are an important building block of TrueFi, as they can operate independently from the TrueFi pools. Tokenized loans open up opportunities for secondary markets, though TrueFi will initially not allow for the transfer of LoanTokens. 

Creating a LoanToken requires a borrower address, principal loan amount, loan length, and interest rate. Users mint LoanTokens by depositing funds into the token contract, and the minted tokens represent a share in the funds repaid plus interest at the end of the term. LoanTokens are minted at a discounted rate, meaning for every $1 deposited, $1 + ($1 * rate * term) tokens are minted. Once a loan is funded, the borrower can call a function which allows them to borrow the funds from the smart contract. The borrower pays funds back, and at the end of the term LoanToken holders can exchange their LoanTokens 1:1 for their original funds plus interest.

### Lending Pools (TrueFi Pools)

A Lending pool is an ERC20 contract with additional functions to support providing liquidity and staking TrustTokens. Lending Pools are another building block of TrueFi, allowing pool depositors to receive pool tokens which represent their share of a pool. Lending pools decide based on a set of rules which borrowers to fund, and thus chooses which LoanTokens to fund.

Initially TrueFi will have a single lending pool. This pool will act as a pilot for future pools, and will only lend to a whitelist of trusted borrowers

TrueFi can offer an interest rate that is at least as high as the highest stable rate available in DeFi today by depositing unused funds into existing defi protocols. TrueFi will only make loans when the proposed rate exceeds the rate offered by the selected defi protocol.

### Credit Prediction Market

How can loans with no collateral be built on an anonymous platform such as Ethereum? TrueFi uses a prediction market to rate the credit worthiness of borrowers. Participants are rewarded for choosing borrowers that pay back their loans with high interest. Incorrectly predicting the outcome of a loan will cause funds to be lost.

The prediction market operates independently of the TrueFi pools. The only goal of this market is to try to accurately assess the likelihood a LoanToken will be paid back in full. It is up to third parties or TrueFi pools to decide to fund the LoanTokens. The TrueFi Credit Prediction Market will help expand credit to wide set of borrowers as its systems for assessing and managing risk become more sophisticated.

### TrustToken (TRU)

What is the purpose of TrustTokens in the context of TrueFi? TrustToken holders ultimately have say over who is a credible borrower in the prediction market. TRU gives holder the ability to rate credit for third parties. Currently the United States relies on a very small set of rating agencies, which are notoriously lack transparency and security. Through TRU credit rating, a permissionless system of credit can be built which operates purely through incentives.

TrueFi will use TrustToken incentive rewards to bootstrap growth. Early adopters and liquidity providers are distributed TRU for participating in TrueFi. These participants can then use TRU in the credit prediction market, and have part ownership in building a new credit system.

# Philosophy - Progressive Decentralization

TrustLab's mission is to make financial freedom as accessible as the internet and open access to financial opportunities. 

In line with this philosophy, TrueFi will be built on the concept of progressive decentralization. Initially, the focus of TrueFi development will be to bootstrap the protocol and distribute TRU to the community of users and developers who participate in the protocol. The second phase will consist of automation and decentralization. Therefore the initial specification is rigid, with conservative parameters such as minimum/maximum APY and high TRU participation factor. It's important to remove these stops as the system grows.

The long term goal of TrueFi is to become a market-driven, automated credit rating and lending system. Uncollateralized loans are extremely high risk, and building a permissionless solution to create these kinds of loans is very difficult. Therefore TrueFi will be launched with strict parameters and place the majority of the risk on TrustLabs and early adopters. 

# Technical Details

## LoanToken

### LoanToken Parameters
- rate (APY)
- term
- amount
- borrower

### LoanToken Lifecycle

#### I. Fundraising
    - Mint/Burn Loan Tokens is enabled
    - Depositors can send deposit tokens to contract
        -> mint LoanTokens according to discount rate model
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

## Credit Prediction Market

TrueFi uses use a prediction market to signal how risky a loan is. The Credit Prediction Market estimates the likelihood of a loan defaulting. Any TRU holder can vote YES or NO and stake TRU as collateral on their vote. If a loan is funded, TRU is locked into the market until. Locking TRU into the prediction market allows voters to earn and claim incentive TRU throughout the course of the loan. After the loan's term, if the voter is correct, they earn a TRU reward plus a portion of the losing side's vote. Lenders can use the credit prediction market as an indicator as to how safe or risky a given loan is.

### Voting Lifecycle
- Borrowers can apply for loans at any time by deploying a LoanToken
- LoanTokens are registered with the prediction market contract
- Once registered, TRU holders can vote at any time
- If a loan is funded, TRU is locked for the term of the loan
- At the end of the term, the amount paid back is recorded, and payouts are determined based on the outcome

### Applying for A Credit Rating

To receive a credit rating on a loan, a borrower deploys a LoanToken with parameters and calls a function on the credit market contract. Once applied, TRU holders can vote on loans. The ratio of YES to NO voters is a signal

### Loan Outcomes

A borrower can pay back their loan at any time before the end of the term. Borrowers are expected to pay back principal + (principal * interest). If they return the full amount including interest, TrustToken voters that voted YES on the proposal receive a reward. If they fail to return the full amount including interest, TrustToken voters that voted YES on the proposal will have some of their TrustTokens burned.

### Staking Rewards and Payouts

Two ways you get TRU by voting:
- Reward over time for participation
- Payout of prediction market

Creating good staking payouts is difficult as creating too much of a reward for voting YES or NO could cause participants to always vote YES or NO. Creating too much of a punishment for losing could also cause nobody to participate in the rating system. For this reason, all participation is rewarded equally through TRU incentive distribution. In addition, a few adjustable factors are set up to adjust the punishment from losing the vote.

#### Rewards

Rewards are paid out proportionally to all participants, favoring participation in ranking higher interest loans by weighting rewards based on the total interest that would be paid over the course of a loan. Current reward can be claimed at any time so that there is more value to staking (rather than having to wait until the end of a loan). 

`chi = (TRU remaining in distributor) / (Total TRU allocated for distribution)` 

`interest = (loan APY * term * principal)` 

`R = Total Reward = (interest * chi)`  

- R is distributed to voters based on their proportion of votes/total_votes
- Voters can claim their proportion based on time.

`Claimable reward = R * (current time / total time) * (account TRU staked / total TRU staked) - (amount claimed)`

#### Payouts

After the expiry, a portion of the loser's TRU is paid out to the winner, and a portion of the loser's TRU is paid out to the loser. A few factor in the prediction market contract help determine the payout:  

**lose factor:** % of TRU paid from the losing side to the winning side  
**burn factor:** % of lost TRU burned  

Initial factors will be set as follows:

**lose factor:** 0.25  
**burn factor:** 0.25  

## TrueFi Lending Pool

The Lending Pool is a TrueFi pool which exposes depositors to un-collateralized loans. A select set of institutional borrowers will be chosen for this pool in order to keep the pool initially secure. Borrower addresses will be whitelisted and will be the only addresses which can be approved for loans.  The pool will purchase LoanTokens based on a set of parameters. A function can be called passing in a LoanToken address, and if the LoanToken meets the pool criteria, the loan is approved and the pool funds are used to fund the loan.

Capital that is not actively being loaned out will be used to provide liquidity in audited, high-interest rate defi smart contracts (such as Aave or curve.fi).

#### Lending Pool Parameters: 
- minLoan: Minimum Loan Size
- maxLoan: Maximum Loan Size
- minRate: Minimum Loan APY
- maxRate: Maximum Loan APY
- minTerm: Minimum Term Length
- maxTerm: Maximum Term Length
- riskAversion: Risk Aversion Factor Based on Expected Value
- participationFactor: How Many Votes Required to Approve

**riskAversion:** Measures how much worse it is to lose $1 than it is to gain $1.  
**participationFactor:** Ratio of # of TRU staked on loan to size of loan.  

### Lending Pool Behavior
- Whitelist borrowers
- Borrowers register loan tokens with prediction market
- Window of time where borrowers
- Window of time where TRU holders can vote
- Loans not approved refunds TRU
- Proposals can be revoked by borrower

#### Default Parameters

The Lending Pool will have several initial parameters:

- Risk Aversion: 1.0
- participation: 1.0
- Minimum loan size: 1M TUSD
- Maximum loan size: 10M TUSD
- Minimum % APR: 10%
- Maximum % APR: 30%
- Minimum Term: 1 day
- Maximum Term: 30 days
- Assets: TUSD only
- Idle Funds usage: 100% CRV

## TRU Distribution

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
| Ratio  | 0.25            | 0.3         | 0.45          |
| Time   | 1-12 Months     | 4 years     | 4 years       |
| Total  | 141,375,000     | 169,650,000 | 254,475,000   |
|        |                 |             |               |

These values were chosen in order to distribute TRU to early adopters of the protocol, and incentivise providing liquidity for TRU pairs on DEXs such as Uniswap and Balancer in the short term. We also want to reward long term users of the protocol by providing the majority of incentive distribution to users of TrueFi.

There will be two types of distributions: one for providing liquidity on external market making protocols, and one for providing liquidity on TrueFi. These will be handled with similar but slightly different mechanisms.

To farm TRU by providing liquidity outside of TrueFi, LP tokens need to be staked in exchange for farm tokens (see below). Holders of farm tokens can claim rewards in TRU as incentive for providing liquidity on external markets. These markets are:

- Uniswap ETH/TRU
- Uniswap TrueFi-LP / TUSD
- Balancer 95% BAL / 5% TRU

#### Balancer 95% BAL / 5% TRU
- Meant to be the highest yield pool for the shortest amount of time. BAL holders can use BAL to farm TRU.

#### Uniswap ETH/TRU
- Incentivise TRU liquidity versus ETH. ETH holders can farm TRU using ETH with upside to both.

#### UNI TUSD/TFI-LP
- Provide liquidity for swapping pool tokens and TUSD. Useful for small investors that need TUSD liquidity going in/out of the pool.

- Also the pool that gives the most TRU for 1 year due to stacked inventives.

#### TrueFi LP (TFI-LP)
- Incentivise depositing TUSD into the TrueFi Pool. Lowest yield but longest distribution of 4 years.

#### TRU Staking
- Variable reward incentivises voting on loans and rating credit.

- Distributes the highest total amount of TRU.

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
