# TrueFi

TrueFi is a protocol for creating interest bearing pools with a high APR for liquidity providers. TrueFi includes a staking mechanism using TrustTokens (TRU) and rewards stakers for keeping stable, high APR. Under-collateralized loans and smart contract pools are utilized in order to achieve high interest rates for pools.

## Setup
```
yarn install
waffle
```

## Terminology

**token** - an ERC20 token running on the Ethereum blockchain.  
**stablecoin** - a token which is pegged to another asset.  
**defi** - decentralized finance using smart contracts.  
**pool** - a smart contract which allows accounts to pool tokens with the goal of earning interest.  
**deposit tokens** - tokens deposited into a pool which are used to earn interest through lending or arbitrage opportunities.  
**pool tokens** - tokens issued by a pool to represent share in the pool's deposit tokens.  
**TrustToken** - an ERC20 token used to provide utility in the TrueFi ecosystem.  
**TrueUSD** - a stablecoin used frequently in TrueFi to provide liquidity.

## TrueFiPool 

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

## LoanToken 

A LoanToken is an ERC20 contract which represents lender share of an under-collateralized loan. Creating a loan requires a borrower address, principal loan amount, loan length, and interest rate.

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