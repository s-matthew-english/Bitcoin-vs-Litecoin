# Bitcoin vs. Litecoin: `chainparams.cpp` 

The text and code samples in this repository highlight the salient differences between Bitcoin and Litecoin. 
As you will see, large parts of the codebase as identical. 
We are interested in the components that are *not* identical. 
Because we are on a mission to create a new altcoin, here we go…

---

### Litecoin:

    genesis.vtx.push_back(txNew);

### Bitcoin:

    genesis.vtx.push_back(MakeTransactionRef(std::move(txNew)));

What is this function `MakeTransactionRef()`?

What is the function `std::move()`?

Why does Bitcoin have them while Litecoin does not? 

Can they be safely be ignored in my altcoin? 

---

### Litecoin:

    const char* pszTimestamp = “NY Times 05/Oct/2011 Steve Jobs, Apple’s Visionary, Dies at 56”;
    const CScript genesisOutputScript = CScript() << ParseHex(“040184710fa689ad5023690c80f3a49c8f13f8d45b8c857fbcbc8bc4a8e4d3eb4b10f4d4604fa08dce601aaf0f470216fe1b51850b4acf21b179c45070ac7b03a9”) << OP_CHECKSIG;

### Bitcoin:

    const char* pszTimestamp = “The Times 03/Jan/2009 Chancellor on brink of second bailout for banks”;
    const CScript genesisOutputScript = CScript() << ParseHex(“04678afdb0fe5548271967f1a67130b7105cd6a828e03909a67962e0ea1f61deb649f6bc3f4cef38c4f35504e51ec112de5c384df7ba0b8d578a4c702b6bf11d5f”) << OP_CHECKSIG;

This is the famous *political statement* included in Bitcoin. 
As we can see Litecoin wanted to use this space to demonstrate their allegiance to the cult of Steve Jobs- nice.
I suppose that all that we need to do here is include some fancy text of our own. 
However- I don’t know what that complicated alpha-numeric string represents, is it some kind of hash of the text?

---

### Bitcoin: 

    void CChainParams::UpdateBIP9Parameters(Consensus::DeploymentPos d, int64_t nStartTime, int64_t nTimeout)
    {
        consensus.vDeployments[d].nStartTime = nStartTime;
        consensus.vDeployments[d].nTimeout = nTimeout;
    }

In Bitcoin they have this function right after `CreateGenesisBlock()`, need to find out what `BIP9` is and why it may be important.

---


## Considering the function `CMainParams()`

Until otherwise noted, all the code below is drawn from this function. 

---

### Bitcoin:

    consensus.nSubsidyHalvingInterval = 210000;

### Litecoin: 

    consensus.nSubsidyHalvingInterval = 840000;

Pretty self-explanitory. 

---

### Litecoin: 

    consensus.nMajorityEnforceBlockUpgrade = 750;
    consensus.nMajorityRejectBlockOutdated = 950;
    consensus.nMajorityWindow = 1000;

These exist in Litecoin but not in Bitcoin, what do they do? 

---

### Bitcoin: 

    consensus.nPowTargetTimespan = 14 * 24 * 60 * 60; // two weeks
    consensus.nPowTargetSpacing = 10 * 60;

### Litecoin: 

    consensus.nPowTargetTimespan = 3.5 * 24 * 60 * 60; // 3.5 days
    consensus.nPowTargetSpacing = 2.5 * 60;

This is for recalculating the difficulty I presume. 

---

### Bitcoin: 

    consensus.nRuleChangeActivationThreshold = 1916; // 95% of 2016
    consensus.nMinerConfirmationWindow = 2016; // nPowTargetTimespan / nPowTargetSpacing
    
### Litecoin: 

    consensus.nRuleChangeActivationThreshold = 6048; // 75% of 8064
    consensus.nMinerConfirmationWindow = 8064; // nPowTargetTimespan / nPowTargetSpacing * 4
    
Not exactly sure what this does. 

---

### Bitcoin: 

    // The best chain should have at least this much work.
    consensus.nMinimumChainWork = uint256S("0x0000000000000000000000000000000000000000003f94d1ad391682fe038bf5");

    // By default assume that the signatures in ancestors of this block are valid.
    consensus.defaultAssumeValid = uint256S("0x00000000000000000013176bf8d7dfeab4e1db31dc93bc311b436e82ab226b90"); //453354
    
### Litecoin: 

    // The best chain should have at least this much work.
    consensus.nMinimumChainWork = uint256S("0x000000000000000000000000000000000000000000000005c13f99f6d0b1a908");

When the network is new and starting out should I have these at all? 

---

### Bitcoin: 

    /**
     * The message start string is designed to be unlikely to occur in normal data.
     * The characters are rarely used upper ASCII, not valid as UTF-8, and produce
     * a large 32-bit integer with any alignment.
     */
    pchMessageStart[0] = 0xf9;
    pchMessageStart[1] = 0xbe;
    pchMessageStart[2] = 0xb4;
    pchMessageStart[3] = 0xd9;
    nDefaultPort = 8333;
    nPruneAfterHeight = 100000;
    
### Litecoin: 

    /**
     * The message start string is designed to be unlikely to occur in normal data.
     * The characters are rarely used upper ASCII, not valid as UTF-8, and produce
     * a large 32-bit integer with any alignment.
     */
    pchMessageStart[0] = 0xfb;
    pchMessageStart[1] = 0xc0;
    pchMessageStart[2] = 0xb6;
    pchMessageStart[3] = 0xdb;
    nDefaultPort = 9333;
    nPruneAfterHeight = 100000;

What are these exactly and why are they different in Bitcoin and Litecoin? 
As I recall this code was precisely mentioned in a guide describing how to create an altcoin. 

---

### Bitcoin: 

    genesis = CreateGenesisBlock(1231006505, 2083236893, 0x1d00ffff, 1, 50 * COIN);
    consensus.hashGenesisBlock = genesis.GetHash();
    assert(consensus.hashGenesisBlock == uint256S("0x000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f"));
    assert(genesis.hashMerkleRoot == uint256S("0x4a5e1e4baab89f3a32518a88c31bc87f618f76673e2cc77ab2127b7afdeda33b"));

### Litecoin: 

    genesis = CreateGenesisBlock(1317972665, 2084524493, 0x1e0ffff0, 1, 50 * COIN);
    consensus.hashGenesisBlock = genesis.GetHash();
    assert(consensus.hashGenesisBlock == uint256S("0x12a765e31ffd4059bada1e25190f6e98c99d9714d334efa41a195a7e7e04bfe2"));
    assert(genesis.hashMerkleRoot == uint256S("0x97ddfbbae6be97fd6cdf3e7ca13232a3afff2353e29badfab7f73011edd4ced9"));

Here the values are different but the function are the same. 
    
---

### Bitcoin: 

    // Note that of those with the service bits flag, most only support a subset of possible options
    vSeeds.push_back(CDNSSeedData("bitcoin.sipa.be", "seed.bitcoin.sipa.be", true)); // Pieter Wuille, only supports x1, x5, x9, and xd
    vSeeds.push_back(CDNSSeedData("bluematt.me", "dnsseed.bluematt.me", true)); // Matt Corallo, only supports x9
    vSeeds.push_back(CDNSSeedData("dashjr.org", "dnsseed.bitcoin.dashjr.org")); // Luke Dashjr
    vSeeds.push_back(CDNSSeedData("bitcoinstats.com", "seed.bitcoinstats.com", true)); // Christian Decker, supports x1 - xf
    vSeeds.push_back(CDNSSeedData("bitcoin.jonasschnelli.ch", "seed.bitcoin.jonasschnelli.ch", true)); // Jonas Schnelli, only supports x1, x5, x9, and xd
    vSeeds.push_back(CDNSSeedData("petertodd.org", "seed.btc.petertodd.org", true)); // Peter Todd, only supports x1, x5, x9, and xd

### Litecoin: 

    // Note that of those with the service bits flag, most only support a subset of possible options
    vSeeds.push_back(CDNSSeedData("loshan.co.uk", "seed-a.litecoin.loshan.co.uk", true));
    vSeeds.push_back(CDNSSeedData("thrasher.io", "dnsseed.thrasher.io", true));
    vSeeds.push_back(CDNSSeedData("litecointools.com", "dnsseed.litecointools.com"));
    vSeeds.push_back(CDNSSeedData("litecoinpool.org", "dnsseed.litecoinpool.org"));
    vSeeds.push_back(CDNSSeedData("koin-project.com", "dnsseed.koin-project.com"));

These are the seednodes, so far as I know at this point they are quite important when the network is small and lacks many peers. 
How to create one of these seednodes, is there a reference implementation somewhere? 

---

### Bitcoin: 

    base58Prefixes[PUBKEY_ADDRESS] = std::vector<unsigned char>(1,0);
    base58Prefixes[SCRIPT_ADDRESS] = std::vector<unsigned char>(1,5);
    base58Prefixes[SECRET_KEY] =     std::vector<unsigned char>(1,128);
    
### Litecoin:

    base58Prefixes[PUBKEY_ADDRESS] = std::vector<unsigned char>(1,48);
    base58Prefixes[SCRIPT_ADDRESS] = std::vector<unsigned char>(1,5);
    base58Prefixes[SCRIPT_ADDRESS2] = std::vector<unsigned char>(1,50);
    base58Prefixes[SECRET_KEY] =     std::vector<unsigned char>(1,176);
    
    
Slightly different values here in `base58Prefixes[PUBKEY_ADDRESS]`, `base58Prefixes[SECRET_KEY]`, and `base58Prefixes[SCRIPT_ADDRESS2]`.
Why is that? What is `base58Prefixes[SCRIPT_ADDRESS2]` in Litecoin? 

---

### Bitcoin: 

    fDefaultConsistencyChecks = false;
    fRequireStandard = true;
    fMineBlocksOnDemand = false;

### Litecoin: 

    fMiningRequiresPeers = true;
    fDefaultConsistencyChecks = false;
    fRequireStandard = true;
    fMineBlocksOnDemand = false;
    fTestnetToBeDeprecatedFieldRPC = false;

Here Litecoin has the extra flag variables `fMiningRequiresPeers`, and `fTestnetToBeDeprecatedFieldRPC`. What are these?

---

### Bitcoin: 

    checkpointData = (CCheckpointData) {
        boost::assign::map_list_of
        ( 11111, uint256S("0x0000000069e244f73d78e8fd29ba2fd2ed618bd6fa2ee92559f542fdb26e7c1d"))
        ( 33333, uint256S("0x000000002dd5588a74784eaa7ab0507a18ad16a236e7b1ce69f00d7ddfb5d0a6"))
        ( 74000, uint256S("0x0000000000573993a3c9e41ce34471c079dcf5f52a0e824a81e7f953b8661a20"))
        (105000, uint256S("0x00000000000291ce28027faea320c8d2b054b2e0fe44a773f3eefb151d6bdc97"))
        (134444, uint256S("0x00000000000005b12ffd4cd315cd34ffd4a594f430ac814c91184a0d42d2b0fe"))
        (168000, uint256S("0x000000000000099e61ea72015e79632f216fe6cb33d7899acb35b75c8303b763"))
        (193000, uint256S("0x000000000000059f452a5f7340de6682a977387c17010ff6e6c3bd83ca8b1317"))
        (210000, uint256S("0x000000000000048b95347e83192f69cf0366076336c639f9b7228e9ba171342e"))
        (216116, uint256S("0x00000000000001b4f4b433e81ee46494af945cf96014816a4e2370f11b23df4e"))
        (225430, uint256S("0x00000000000001c108384350f74090433e7fcf79a606b8e797f065b130575932"))
        (250000, uint256S("0x000000000000003887df1f29024b06fc2200b55f8af8f35453d7be294df2d214"))
        (279000, uint256S("0x0000000000000001ae8c72a0b0c301f67e3afca10e819efa9041e458e9bd7e40"))
        (295000, uint256S("0x00000000000000004d9b4ef50f0f9d686fd69db2e03af35a100370c64632a983"))
    };

### Litecoin: 

	checkpointData = (CCheckpointData) {
		boost::assign::map_list_of
			(  1500, uint256S("0x841a2965955dd288cfa707a755d05a54e45f8bd476835ec9af4402a2b59a2967"))
			(  4032, uint256S("0x9ce90e427198fc0ef05e5905ce3503725b80e26afd35a987965fd7e3d9cf0846"))
			(  8064, uint256S("0xeb984353fc5190f210651f150c40b8a4bab9eeeff0b729fcb3987da694430d70"))
			( 16128, uint256S("0x602edf1859b7f9a6af809f1d9b0e6cb66fdc1d4d9dcd7a4bec03e12a1ccd153d"))
			( 23420, uint256S("0xd80fdf9ca81afd0bd2b2a90ac3a9fe547da58f2530ec874e978fce0b5101b507"))
			( 50000, uint256S("0x69dc37eb029b68f075a5012dcc0419c127672adb4f3a32882b2b3e71d07a20a6"))
			( 80000, uint256S("0x4fcb7c02f676a300503f49c764a89955a8f920b46a8cbecb4867182ecdb2e90a"))
			(120000, uint256S("0xbd9d26924f05f6daa7f0155f32828ec89e8e29cee9e7121b026a7a3552ac6131"))
			(161500, uint256S("0xdbe89880474f4bb4f75c227c77ba1cdc024991123b28b8418dbbf7798471ff43"))
			(179620, uint256S("0x2ad9c65c990ac00426d18e446e0fd7be2ffa69e9a7dcb28358a50b2b78b9f709"))
			(240000, uint256S("0x7140d1c4b4c2157ca217ee7636f24c9c73db39c4590c4e6eab2e3ea1555088aa"))
			(383640, uint256S("0x2b6809f094a9215bafc65eb3f110a35127a34be94b7d0590a096c3f126c6f364"))
			(409004, uint256S("0x487518d663d9f1fa08611d9395ad74d982b667fbdc0e77e9cf39b4f1355908a3"))
			(456000, uint256S("0xbf34f71cc6366cd487930d06be22f897e34ca6a40501ac7d401be32456372004"))
			(638902, uint256S("0x15238656e8ec63d28de29a8c75fcf3a5819afc953dcd9cc45cecc53baec74f38"))
			(721000, uint256S("0x198a7b4de1df9478e2463bd99d75b714eab235a2e63e741641dc8a759a9840e5")),
			1422681363, // * UNIX timestamp of last checkpoint block
			5502192,   // * total number of transactions between genesis and last checkpoint
			//   (the tx=... number in the SetBestChain debug.log lines)
			5500.0     // * estimated number of transactions per day after checkpoint
	};

Seems that these are more or less equivalent, just with different values. Is it so? 

---

### Bitcoin 

    chainTxData = ChainTxData{
        // Data as of block 00000000000000000166d612d5595e2b1cd88d71d695fc580af64d8da8658c23 (height 446482).
        1483472411, // * UNIX timestamp of last known number of transactions
        184495391,  // * total number of transactions between genesis and that timestamp
                    //   (the tx=... number in the SetBestChain debug.log lines)
        3.2         // * estimated number of transactions per second after that timestamp
    };

Bitcoin has this extra function, what does it do? 

---

### Litecoin

    static CMainParams mainParams;

Litecoin finishes this section with this code snippt, maybe it's something to do with cpp? 

---

After this it comes to settings for the *Testnet*, for I'll ignore *Testnet* and revisit it later. 


