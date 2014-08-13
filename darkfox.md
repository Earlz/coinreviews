darkfox https://bitcointalk.org/index.php?topic=730801.0

heritage: Watermarks of a "testcoin", libertycoin and bottlecaps ("ComputeMaxBits"), "TC"

Problems:
* MODERATE: "Total coins : 15,000,000" in the ANN is misleading and incorrect. (misunderstanding of MAX_MONEY)
** Actual PoW coins: 675,634 coins + 75,000 coins premine. 
* MAJOR: Huge premine of 10.2%. (with PoS, large amount of control over the network)
** They say they destroyed half of the premine, leaving a still huge 5.1%.
* MODERATE: Stake modifier checkpoints are disabled


Notes:

@@ -369,12 +365,12 @@ bool CheckCoinStakeTimestamp(int64_t nTimeBlock, int64_t nTimeTx)
 // Get stake modifier checksum
 unsigned int GetStakeModifierChecksum(const CBlockIndex* pindex)
 {
-    assert (pindex->pprev || pindex->GetBlockHash() == (!fTestNet ? hashGenesisBlock : hashGenesisBlockTestNet));
+    //assert (pindex->pprev || pindex->GetBlockHash() == (!fTestNet ? hashGenesisBlock : hashGenesisBlockTestNet));
     // Hash previous checksum with flags, hashProofOfStake and nStakeModifier
     CDataStream ss(SER_GETHASH, 0);
     if (pindex->pprev)
         ss << pindex->pprev->nStakeModifierChecksum;
-    ss << pindex->nFlags << pindex->hashProofOfStake << pindex->nStakeModifier;
+    ss << pindex->nFlags << (pindex->IsProofOfStake() ? pindex->hashProof : 0) << pindex->nStakeModifier;

genesis seed is news event

@@ -1980,8 +1981,8 @@ bool CBlock::AddToBlockIndex(unsigned int nFile, unsigned int nBlockPos, const u
         return error("AddToBlockIndex() : ComputeNextStakeModifier() failed");
     pindexNew->SetStakeModifier(nStakeModifier, fGeneratedStakeModifier);
     pindexNew->nStakeModifierChecksum = GetStakeModifierChecksum(pindexNew);
-    if (!CheckStakeModifierCheckpoints(pindexNew->nHeight, pindexNew->nStakeModifierChecksum))
-        return error("AddToBlockIndex() : Rejected by stake modifier checkpoint height=%d, modifier=0x%016"PRIx64, pindexNew->nHeight, nStakeModifier);
+    //if (!CheckStakeModifierCheckpoints(pindexNew->nHeight, pindexNew->nStakeModifierChecksum))
+        //return error("AddToBlockIndex() : Rejected by stake modifier checkpoint height=%d, modifier=0x%016"PRIx64, pindexNew->nHeight, nStakeModifier);


@@ -2121,9 +2125,6 @@ bool CBlock::AcceptBlock()
     if (IsProofOfWork() && nHeight > LAST_POW_BLOCK)
         return DoS(100, error("AcceptBlock() : reject proof-of-work at height %d", nHeight));
 
-    if (IsProofOfStake() && nHeight < MODIFIER_INTERVAL_SWITCH)
-        return DoS(100, error("AcceptBlock() : reject proof-of-stake at height %d", nHeight));
-