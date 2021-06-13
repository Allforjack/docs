# Attack Examples

## Front-Running

Front-running is the process by which an adversary observes transactions on the network layer and then acts upon this information by, for instance, issuing a competing transaction, with the hope that this transaction is mined before a victim transaction e.g. Transaction A is broadcasted with a higher gas price than an already pending transaction B so that A gets mined before B.

## Sandwich Attacks

Alice wants to buy a Token A on a Decentralised Exchange (DEX) that uses an automated market maker (AMM) model. An adversary which sees Alice’s transaction can create two of its own transactions which it inserts before and after Alice’s transaction (sandwiching it). The adversary’s first transaction buys Token A, which pushes up the price for Alice’s transaction, and then the third transaction is the adversary’s transaction to sell Token A (now at a higher price) at a profit.

## Back-Running

Back-running occurs when a transaction sender wishes to have their transaction ordered immediately after some unconfirmed "target transaction". 

Example: A back-running bot that back-runs new token listings. Bot monitors the Ethereum mempool for new pairs being created on Uniswap. If it finds a new pair the bot places a buy transaction immediately behind the initial liquidity. The bot swoops in and buys as many tokens as possible (but not all of them as there needs to be an opportunity for others to buy tokens as well).The bot then waits for the price to go up as other traders buy the token from Uniswap and proceeds to sell back the tokens at a higher price. The key in this strategy is to be the first to buy tokens, but only after the token has been launched.[^1]

In order to maximise their chances of being mined immediately after their target, a typical backrunner will send many identical transactions, with gas price identical to that of the target transaction, sometimes from different accounts, in order to increase the chances that one of their transactions is ordered after the target but before any competitor.

## Liquidations 

Back-running strategies also apply to liquidations whereby a transaction sender wishes to be the first to liquidate a loan right after a price oracle update (which will allow liquidation to be triggered).

* Fixed spread liquidation used by Compound, Aave, and dYdX allows a liquidator to purchase collateral at a fixed discount when repaying debt. 

    === "Strategy 1"

        A detects a liquidation opportunity at block B (i.e., after the execution of B). A then issues a liquidation transaction T, which is expected to be mined in the next block B +1. A attempts to destructively front-run other competing liquidators by setting high transaction fees for his liquidation transaction T.

    === "Strategy 2"

        A observes a transaction T, which will create a liquidation opportunity (e.g., an oracle price update transaction which will render a collateralized debt liquidatable). A then back-runs T with a liquidation transaction T~A~ to avoid the transaction fee bidding competition.


* The auction liquidation allows a liquidator to start an auction that lasts for a pre-configured period (e.g., 6 hours). Competing liquidators can engage and bid on the collateral price.


## Time-Bandit Attacks

Time-bandit attacks are attacks where miners rewrite blockchain history to steal funds allocated by smart contracts in the past. If block rewards are small enough compared to MEV, it can be rational for miners to destabilize consensus.

Imagine there are two miners, Sam and Dan, who are paid a $100 reward for each block they find. Sam has found 3 blocks, the first of which contained a $10,000 arbitrage opportunity.

Now Dan has a choice: he can either mine on top of Sam’s 3 blocks, or he can attempt to re-mine the first block in order to take the Uniswap arbitrage for himself. The $10,000 is much more lucrative than the $100 block reward, and Dan is more rational than honest, so he decides to re-mine the first block. While Dan’s at it, since the current longest chain is height 3, he also re-mines the second and third blocks (and captures any MEV that was in those, too). After the re-organization, Dan owns the longest chain and he and Sam can progress from the third block.

## The Uncle Bandit Attack

[:fontawesome-brands-twitter:](https://twitter.com/bertcmiller/status/1382673587715342339?s=20){target=_blank}

Bundles are groups of transactions Flashbots users submit. Those transactions must be included in the order submitted, and either the whole bundle is included, or nothing is. A bundle should never be split up.

Robert Miller found that for a specific bundle, only the "Buy" part of a sandwich bundle submitted had landed on-chain, and right after that Buy someone else had inserted a 7 gas transaction that arbitraged it.

How?

In Ethereum occasionally two blocks are mined at roughly the same time, and only one block can be added to the chain. The other gets "uncled" or orphaned. Anyone can access transactions in an uncled block and some of the transactions may not have ended up in the non-uncled block. In a way some transactions end up in a sort of mempool like state: they are now public as a part of the uncled block and perhaps still valid too.

A Sandwicher's bundle was included in an uncled block. An attacker saw this, grabbed only the Buy part of the Sandwich, threw away the rest, and added an arbitrage after. The attacker then submitted that as a bundle, which was then mined. 

Instead of seeing something late in time and rewinding it (time-bandit attack), the uncle bandit attack is when an attacker sees something in an uncle and brings it forward. This also shows that attacks extend beyond the mempool and into uncled blocks as well. 



[^1]: <https://amanusk.medium.com/the-fastest-draw-on-the-blockchain-bzrx-example-6bd19fabdbe1>{target=_blank}
