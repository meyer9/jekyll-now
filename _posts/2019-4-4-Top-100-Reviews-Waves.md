---
layout: post
title: Waves Technical Review - Top 100 Crypto Review
---

This is just a quick introduction to my blog. I'll be posting here technical articles once per week (every friday) about certain cryptocurrencies usually in the top 100.

You can subscribe to my newsletter [here](https://buttondown.email/meyer9) where I'll be sending out exclusive content along with new blog posts.

I decided to start with Waves because I haven't used the platform before, I only barely know what it's used for, and I don't know the tech behind it at all. I'll review the tech, security and trust implications, the coding standards, etc. This post is meant as a comprehensive technical review of the Waves platform.

![Waves Review](/images/waves-header.png)

At first glance, Waves' website is flashy and seems like something out of a design-focused product. The site seems stylistically glitchy and definitely not the best for conveying information. The two big products I see as I scroll down the homepage are: Vostok, a universal blockchain solution... designed for large enterprises, and Waves, the open-source public blockchain.

![Waves Homepage](/images/waves-design.png)

Their site focuses mainly on Web 3.0, which is a crypto buzzword meaning their blockchain runs dApps. Waves is proof-of-stake meaning that miners bet their coins on the next block instead of betting mining power. The types of proof-of-stake vary wildly between different platforms, so I'll definitely be looking into this first.

![Waves Features](/images/waves-features.png)


# Waves Proof-of-Stake (LPoS)

## Terminology and Conceptual Problems

Waves uses a system called "Leased Proof-of-Stake". One important claim they make that really stands out to me as I read this is the statement below:

> Usually, in the PoS system, there is no block reward, so, the miners take the transaction fees. That's why miners are often called block forgers or generators, instead, Figure 1.

This is a misconception of Proof-of-Stake. There is no reason that stakers can't receive a block reward. In almost every proof-of-stake system, block proposers are rewarded with coins. Waves had an ICO of $16 million, so the developers and leaders are incentivized to keep inflation low to keep their share high. This could be a security problem if transaction fees are too small.

Later, on the same page, they make this statement:

> The proof of stake avoids this ‘tragedy’ by making it disadvantageous for a miner with a 51% stake in a cryptocurrency to attack the network.

While this is true generally, the same applies to proof-of-work. Bitcoin miners don't want to attack the network because it will devalue the currency they mine and their equipment. With no "external" disincentive for miners to attack the network, a bribing attack is as simple as bribing miners more to attack the network than they would lose from the network being attacked.

Again, they state the same thing in different terms:

> a miner with 51% stake in the coin would not have it in his best interest to attack a network which he holds a majority share

However, they are not considering the possibility that the attacker doesn't need to own coins and could attack the network simply by bribing stakers and offering them extra income.

## LPoS is worse than PoS

Because of this fundamental misunderstanding of attacking models, Waves goes on to explain the real problem with their staking system:

> the user will have the ability to Lease Waves form the wallet to different contractors which can pay a percentage as a reward

**This is a BIG red flag.**

Let me illustrate why this is a bad idea with an example.

Let's say Alice and Bob are stakers in the Waves network. They each hold 25% of Waves. Alice runs a full-node because he really supports the mission, but Bob is just trying to make a quick buck. Eve wants to attack the network. Alice stakes her coins and Bob also pays Alice 5% to stake his coins using LPoS.

Bob and Alice know that if Eve attacks the network, their coins will devalue by 30%.

The cost for Eve to attack the network is: $$C = B/0.7 = 1.42B$$ where B is the value Bob's coins. This means that Eve needs to pay Bob only about 40% more than Bob's stake in the network ($$25\% \times 140\% = 35\%$$) to gain control of 50% of the network power.

Bob is happy because he's getting paid more than he would have by just staking his coins. Alice is happy because Bob is staking her coins and she doesn't have to run a full node. And Eve is happy because she can attack the network for less of a cost than 50%.

Delegating stake in proof-of-stake systems always lowers security.

## Encouraging users to decrease network security is really bad

Their system even strongly encourages users to do this:

> The balance of the node can be empty until there are enough people wishing to lease to it by reaching together the generating balance of 1000 Waves and create together a pool.

Small stake holders can't stake blocks. Only large pools can. This not only leads to a severe security decrease, but also strong centralization as bigger pools are more trusted and grow faster than smaller pools.

This is seen very clearly in their [block explorer](https://dev.pywaves.org/generators-monthly/) where the top 6 miners control 57% (as of writing) of the staking power.

# Waves Source Code

Waves source code is clean and uses good practices. The code is well-commented and easy-to-read. They use strong crypto libraries and functions. When in doubt, they stick to proven Bitcoin features.

## Scala is a good choice

Waves is written in a programming language called Scala which is a functional language built on top of the JVM (Java Virtual Machine). This lets them deploy versions of Waves on many platforms with very similar code. The JVM is highly optimized, but not great for low-level operations like cryptography.

Internally, Waves uses the [Scrypto](https://github.com/input-output-hk/scrypto) library for Ed25519 curve. Waves uses the Blake2b256 hash function for fast hashing and `Keccak(Blake2b256(m))` for secure hashing. Ed25519 is a strong choice due to its speed, security, and provable lack of backdoors. The parameters are carefully chosen to make inserting a backdoor tricky.

The code uses similar structures to Bitcoin.

Waves uses LevelDB as their block database which is the same as is used in Bitcoin.

The code overall seems very well-organized. They include over 800 tests, all passing on my computer which check RPC commands, DEX system, transfer tests, stake leasing, transactions, script, blockchain, activation code for new features, and state transition tests. All of these improve security of the network and the protocol.

## Documentation

Waves also includes a signficant amount of developer documentation providing information about the consensus sytem, RPC, smart contract development, oracles, etc. This is a really great sign because it means that developers will be encouraged to use Waves to develop dApps.

Each change to the Waves architecture is also documented within their developer docs. It gives specific information about how the change will work including a detailed specification.

Waves includes, in their proposals, information about an interesting feature called Sponsored Transactions which let users transact with the network without using Waves tokens and instead using an asset hosted on the Waves blockchain. This is a huge feature for ICOs who don't want to add extra friction in allowing users to transfer tokens and use their dApps.

## DEX

They launched a decentralized exchange for trading Waves/_TC pairs. This improves their ecosystem in two ways.

First, it gives miners an extra source of revenue from the trading fees. This is important because the security of the network is highly dependent on miner revenue and without a block reward, miners need transaction fees to secure the network.

Second, it increases adoption of Waves to transfer assets and use their asset management platform similar to ERC. More users of tokens built on Waves means a higher network value.

The interface looks nice and everything happens on-chain, so this is definitely a huge plus for Waves.

## Web 3

Waves fiercely advertises their Web 3 features focusing mostly on dApps.

Waves definitely excels in this area, providing a browser extension for accessing dApps and managing private keys through your browser. This is important as it gives users a smoother onboarding process.

They wrote their own smart contract language called "RIDE". RIDE is strongly-typed, "lazy", and expression-based. RIDE includes some functional features like pattern-matching. The final contracts, however strongly resembly Bitcoin script with a few extra features. Loops, function calls, and jumps are not supported so the language is **not Turing-complete**.

The language seems to be the only piece of the puzzle missing for real dApps to exist on Waves. Many complicated dApps will not be able to be built on Waves due to the restrictions by the smart contract language. The language seems focused on simplicity and correctness, but is insufficient for most "Web 3" applications as they advertise.

| Pros | Cons |
|-------|--------|
| Smart Contracts | Lack of Turing-completeness |
| On-chain DEX | Proof-of-Stake Leasing system |
| Simple Light Nodes | Incentivizes miner pools (centralization) |
| Asset Management | |
| Written in Scala | |
| Well Documented and Tested | |
| Simple, Tested Cryptography | |

# Conclusion

Overall, Waves is a strong cryptocurrency with developers and a team who know what they're talking about. The small security problems don't overshadow their larger goals. The smart contract language, while basic, will be more secure and less error-prone than something like solidity. The DEX is a great way to increase adoption and miner fees. It's implemented well and not centralized (everything is on-chain).

Waves is an interesting cryptocurrency, coded from scratch, and built on solid foundations. They make generally reasonable technical choices and focus on useful tech.
