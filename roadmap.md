# The TrueFi Protocol Roadmap


# Vision for TrueFi

**The goal of TrueFi is to become the most widely adopted and most trusted protocol for uncollateralized lending on blockchain.** TrueFi’s first borrowers are crypto-native trading firms and will gradually become a diverse set of companies and individuals, letting TrueFi act as a more general financing tool. 

The focus of the protocol needs to be on **providing an attractive risk-adjusted return over the long term**. Compared to other successful DeFi projects, TrueFi will target a relatively higher-risk, higher-yield segment of the market. Achieving this safely will require bridging the gap between centralized information (off-chain credit data, KYC/AML, legal agreements) and blockchains - a unique and valuable capability.

The protocol will use community incentives (in the form of TRU) to accelerate growth and decentralize governance, but will be less and less dependent on incentives to boost yields over time.

This document, (i) discusses the key changes necessary for TrueFi to become the foundational protocol for uncollateralized lending and (ii), proposes a roadmap with specific milestones to get there.


# Phases

Over the first half of 2021, TRU holders will be called upon to make material upgrades to the TrueFi protocol in order to improve the efficiency of TRU usage, migrate to and improve on-chain governance, and optimize the borrower approval process. 

TRU tokens are the native asset of the TrueFi protocol. In addition to community incentives (~40% of total token supply), [TRU are being distributed](https://blog.trusttoken.com/introducing-truefi-the-defi-protocol-for-uncollateralized-lending-9bfd6594a48) to private sale participants, the core team and management, and to future team members with quarterly unlocks over the next two years. As such, the protocol roadmap is oriented around each unlock period. As there is still much to be improved, each of the first few unlock periods are tied to 2-3 ambitious development goals. Crypto moves fast and these goals could change, but as of this writing, the deliverables described below seem like the highest value improvements to make for the long-term growth of TrueFi.


## Phase 1 - Nov 21st 2020 [Completed]

1. Launch Version 1 (“V1”) of the TrueFi Protocol
2. Issue TRU Tokens


On November 21, V1 of the TrueFi protocol and the TRU tokens were shipped. In V1, institutional borrowers are onboard by completing a KYC/AML review, signing a legally-enforceable loan agreement with TrustToken, Inc. as the counterparty, and letting TRU holders assess their reputation with a [New Borrower Request](https://forum.truefi.io/c/Borrower-Requests/5) on the TrueFi forum before seeking TRU holders to stake on each of their individual loans for ultimate approval. New borrowers apply for smaller loans (usually $1-2mm) and then, after establishing some record of repayment with TrueFi, are allowed to apply for larger amounts.

V1 has proven to be a substantial and fully-functioning protocol, accounting for $40 million+ of fixed-term lending activity from respected borrowers like Alameda Research, Wintermute Trading, Grapefruit Trading, and Invictus Capital. V1 has already generated $110,000+ in [returns](https://etherscan.io/address/0xa1e72267084192db7387c8cc1328fade470e4149#readProxyContract) to TUSD lenders (this does not include TRU incentives for lenders). Additionally, borrower demand has begun to outstrip TUSD supply for the first time, demonstrating growing interest from borrowers. 

This fixed-term model that went live in V1 has drawbacks however, especially in terms of TRU utilization. Because staking on an individual loan locks the staked TRU, that same TRU cannot be used for governance voting or providing liquidity. These aspects of the protocol will be improved in phases 2-4 over the coming months


## Phase 2 - By Feb 19th 2021 

1. Add Liquid Exit
2. Improve staking model
3. Launch fully on-chain governance 

**Liquid Exit**: This upgrade addresses a key community request: the ability to exit the TrueFi Lending Pool directly into the asset that the lender used to enter the pool (for now, just TUSD). This process currently relies on Uniswap. With Liquid Exit, the Lending Pool will act as a liquidity provider and will buy TFI-LP tokens in exchange for TUSD. You can find more detail on Liquid Exit [here](https://docs.google.com/document/d/1YtDUkocD5OoYJi4PRALFihCUY6uut55pa2mvNx4mgeQ/edit?usp=sharing). 

**Improved Staking Model:** A [new staking model](https://github.com/trusttoken/truefi-spec/blob/master/TrueFi2.0.md#staking) will be introduced that will provide much clearer utility for TRU as well as an improved staker experience. Stakers will stake on the overall protocol rather than being forced to stake on individual loans. 

Additionally, **stakers will benefit two-fold from staking**: (1) earning fees paid by borrowers on loan origination (denominated in TFI-LP), and (2) accruing TRU incentives for staking

Protocol stakers will not be _required_ to rate individual borrowers. However, since protocol stakers are exposed to all loans on the platform, it is in stakers’ best interest to vote against loans that they deem to be too risky for the pool. Stakers stand to have a percentage of their stake (initially, 10% per default) reclaimed by the protocol in the event of a loan default. Reclaimed TRU is transferred to the lending pool that took the loss. TRU holders may decide if stakers will be rewarded with TRU for voting. 

**On-chain governance:** A fully on-chain [governance model](https://github.com/trusttoken/truefi-spec/blob/master/TrueFi2.0.md#staking) will bring TrueFi governance on par with other fully decentralized protocols where there is **no centralized actor** with special privileges over the protocol. This will further strengthen TrueFi and set the stage for future potential changes, such as allowing Loan Tokens to be freely tradable. 

These improvements mean that staking is directly correlated with the growth of the protocol rather than any individual loan. More opportunities for stakers and token holders to vote on creditworthiness, participate in protocol governance, and earn fees related to the growth of the protocol. 


## Phase 3 - By May 20, 2021

1. Add additional USD-backed stablecoins, specifically USDC, to the first TFI-LP pool
2. Add Lines-of-Credit
3. Implement a more robust credit model for onboarding borrowers
4. Allow Loan Tokens and Line-of-Credit Tokens to be tradable 

**Additional stablecoins**: USDC is the largest fully collateralized USD-backed stablecoin and has widespread adoption throughout DeFi. With a market cap of around $5bn, it’s ~12x the size of TUSD. Adding support for USDC will open up a larger set of depositors into the TrueFi pool. 

**Lines-of-credit**: Open-term loans are common among some of the centralized lending/borrowing markets and actively requested by some TrueFi borrowers. While key questions - such as do borrowers pay interest periodically, and does the loan compound indefinitely - remain to be worked out by the TrueFi community, a [basic spec](https://drive.google.com/file/d/1PjLmFCt3P1Kiz_guv-uYsH72H6D1LDVI/view?usp=sharing) of this proposal is available today. 

**Improved credit model**: TrueFi needs to become world-class at assessing risk. This requires reliably bringing off-chain information on-chain and combining that with existing protocol data such as repayment history. Credit will be assessed using a multivariable model that includes things such as legal jurisdiction, AUM, leverage, options exposure, and more.

It’s crucial that the protocol can assess all of this data without revealing all of it publicly. The information for the credit model can be crunched off-chain in a [secure enclave](https://en.wikipedia.org/wiki/Trusted_execution_environment) and then a proof-of-computation can be uploaded to the blockchain and checked by the smart contract. This allows the smart contract to verify that the credit model was computed correctly, without certain private details (such as the amount of leverage a borrower is using) being exposed publicly. We expect the credit model to take into account more and more factors over time.

**Tradeable Loan Tokens:** One of the financial primitives of TrueFi is that each loan that is taken from the platform produces a new Loan Token representing that debt.


Right now, Loan Tokens are not individually tradeable. However in the future, TRU holders may implement a change so that they can be. Tradable Loan Tokens may open up the possibilities for other developers, protocols, and platforms to use them creatively in their own applications. A few examples:

*   Allowing investors to buy up all the debt from a particular borrower they trust
*   Protocols that work like closed ended funds (TrueFi’s pools are open-ended)
*   Protocols that accept loan tokens as collateral for loans
*   Many more!

If TrueFi is able to execute on these proposed milestones, TrueFi has the potential to become the **foundation of uncollateralized on-chain lending across multiple protocols**. This will grant TrueFi participants access to a larger, more diverse set of borrowers and lenders, more staking opportunities, and more opportunities to exercise governance rights.


## Phase 4 - By August 18, 2021


1. Add support for arbitrary ERC20 tokens (e.g. WBTC pool, WETH pool, etc.) 
2. Allow lending pools to provide liquidity on all Loan Tokens and_ line of credit _tokens
3. Allow other protocols to participate in Loan Token and Line-of-Credit primary fundraising

By Phase 4, TrueFi should be widely used and proven out as a source of capital. At this point and going forward, many protocol improvements will be focused on improving interoperability with other assets and protocols. 

**Allow other protocols to participate in Loan Token and Line-of-Credit primary fundraising:** Today, a borrower can create a loan token which represents their request for $10mm from the TrueFi protocol. In the future, as TrueFi expands to become the basis for uncollateralized lending on-chain, a borrower could post a loan token representing their request to raise _$100mm_, some from TrueFi, but also from _any other protocol that wants to participate_. 

You can think of this as like **Kickstarter for debt:** anyone can participate and get a slice of the pie. The same will apply for lines-of-credit: a borrower looking to _increase_ their outstanding line-of-credit will automatically draw additional capital from any protocol (or individual) which has approved them for additional credit.


## Other Considerations



1. **L2 scaling solutions:** TrueFi likely won’t be a trailblazer here. TRU holders may wait and see what solutions work for other comparable projects and implement as needed. 
2. **Protocol-to-protocol lending**:- A big and interesting market, but requires a different approach than lending to institutions. This is being explored, but is not yet firm enough to be added to the roadmap.
