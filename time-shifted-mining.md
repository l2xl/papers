# Time-Shifted Mining

Disclamer: This write-up creation is caused by private discussion of several people. Authorship of this idea belongs to a wider group of people then just the committer of this paper.

## Intro

Well known Proof-of-Work (PoW) block-chain consensus mechanism is volnurable to a number of well known attacks. One of most known is the 51% attack.
While this does not mainly affect largest block-chain projects like Bitcoin, it is not true for minor block chain projects of just boot strapped blockchains.
Described here additional mechanisms to classical PoW should allow to inscrease resistance to most well known attacks on block-chain consensus.

## Common

Main idea of time shifted mining is to split in person and in time two functions which happen at once with classical PoW: block creation and block solution mining.

If we achieve target of independency of these two functions then miner looses an ability to rewrite block-chain secquence even in case it successfully controls 51% of the network.

## Spec

### Time Shift

According to concensus rules, miners who compete to create block solution hash cannot create block themselves:
instead they need to communicate with a pool of block creators to obtain a signed block template.
The block solution winner adds to the block chain a new block based on the received block template.
He authorises his right to create a new block template later by adding a public key to the mined block.
Anyone, who is able to create signature which is validated by the public key from one of the block mined not earlier/later then concensus approved timeshift (T),
is a valid candidate to create a new block template. Such miners form the block creators pool.
At the time of winning a solution for the new block, a miner does not receive any reward.
To receive the mining reward the miner has to use the right to create new block template with a coinbase transaction after a consensus controlled time shift.

According to scheme above, a block of transactions additionaly to usual data should contain the next new fields:

* Public key of a current block solution winner to authorise his right of creation of a new block after a mining time shift.
* Signature of the block template verifiable by the public key of the block mined before the time of the minig time-shift.
* Blocks may also contain some additional fields needed to optimize mining time-shift rules such as reference (as block height of as block id) from one block, which contains a signature, to another, which contains a miner public key.
 
Note: public key/signature used for block template creation authorization may be replaced by a hash/preimage pair which is much more performance efficient.

Such mining scheme benefits comparing to common PoW because it makes classical types of attack on PoW consensus much more expensive.

### Mining Difficulty Adjustment

The time-shifter PoW scheme as described above is not balanced enought.
Imagine if some miners who wins a block solution will not come with new block template at the time when expected.
This may happen at a regular basis.
The same effect may happen in case if miners will ignore block templates from some miners and benefit block templates from anothers.

To eliminate an economical intention to prefer out of order block templates time-shiftet PoW block chain need additional rules which adjusts current block mining difficulty:

$d = D \cdot \frac {\sum S_i} {(T-N/2) \cdot N}$, where

$d$ - current block difficulty.

$D$ - some base block difficulty (calculated as usual).

$T$ - maximum alowed mining time shift. If a miner does not create new block template during T blocks back from the current block chain height then it looses his right to make it.

$N$ - a "normal" size of the block creators pool. May be equal to the size of time-shift mining window if minig scheme allows it. 

$S_i$ - distance in blocks between exact available block template and current block chain height

This current block difficulty adjustments creates an economical intention for miner to get most "old" block template to create a new block.
In another case the difficulty increases which (in case of hash powed remains unchanged) will lead in increasing of searching time for a block solution.
This will either 
* balance disappearence of miner from the pool of block creators. Until the hole is disappeared the mining will be slower.
* Decrease miners reward per time period in case they favor some block templates
