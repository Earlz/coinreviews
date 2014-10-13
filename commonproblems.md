# Common Problems

This is a list and explanation of some of the more common problems with coins, with possible attack vectors and such.

## Genesis seed is not a news event

It is a good practice to make the "pszTimestamp", or the string that is the initial seed into the genesis block, a recent news event. 
This ensures that it is provably impossible for a coin developer to mine a coin before this point. 

Code usually responsible:

    const char* pszTimestamp = "Firecoin The First X15 PoW PoS Hybrid Cryptocurrency";

## Uses floating point numbers in critical code

Floating point numbers are not guaranteed to be consistent across all platforms for most operations. This means that on some computers
or compilers, it may make a version of the wallet that can fork off of the main network inadvertedly. 

This is not actually exploitable in most cases, but is a good practice to prevent forks

## Stake modifier checkpointing is disabled

Stake modifiers are used to ensure that PoS blocks can not be precomputed. They are changed on a schedule, and are checkpointed on most good coins.
Not checkpointing stake modifiers could probably lead to forks. 

Because the first stake checkpoint also isn't checked, it might be possible to exploit something to precompute PoS blocks, but this has not been proven, 
and I'm not completely sure about the significance. 

## Time drift checking (past or future) is disabled or allows more than +- 2 hours of drift

Time drift checking ensures that a block's timestamp is fairly accurate (by a varying amount, depending on the coin). For bitcoin, this value is set to around 2 hours. For many coins it's 10 or 20 minutes.
Setting this value too high allows for more time drift attacks. Setting this value too low might cause forks or people's wallets to get stuck on computers with slightly inaccurate clocks.

Disabling it completely for either past or future is a really bad idea, since it allows for completely uninhibited time drift attacks. 

Time drift attacks could lead to an attacker with a moderate amount of hashing power having a significant amount of control over the set difficulty of the network. Difficulty can be made lower by using time stamps from the far future. This throws off difficulty calculations. Time stamps could also be set to the far past, causing a blockchain to get stuck with a super high difficulty. 

Code usually responsible: 

    inline int64_t PastDrift(int64_t nTime)   { return nTime - 10 * 60; } // up to 10 minutes from the past
    inline int64_t FutureDrift(int64_t nTime) { return nTime + 10 * 60; } // up to 10 minutes from the future