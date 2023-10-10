# Simple explanation of BitVM

(Bitcoin magazine - [The Big Deal With BitVM: Arbitrary Computation Now Possible on Bitcoin Without a Fork](https://bitcoinmagazine.com/technical/the-big-deal-with-bitvm-arbitrary-computation-now-possible-on-bitcoin-without-a-fork) and
LightningNetwork+ - [BitVM: Ushering in a New Era of Bitcoin Computations](https://lightningnetwork.plus/posts/450) did similar intro posts)

## How to convince people

Suppose there is some complicated open-source program that takes 30 mins to run and in the end outputs 42.
Peggy (the prover) could come up and say hey, I've taken the time to run this for all of you and 42 is the answer.

Why should anyone trust her? Therefore she says I am willing to bet 1 BTC.

But still In order to verify the answer you need to run the code on your machine. And thus the
fact Peggy did it before does not help. 

If you see 42 you can then start convincing people "wow she was right, trust our computation". But for an outsider
that is indistinguishable from a shill Peggy paid to confirm her false result. You might also start proclaiming I've got 43 on my terminal
Peggy now owes me 1 BTC. How can such a bet ever get resolved? Obviously another trusted third party can still be lying. Also the majority
of votes is not enough. To keep that 1 BTC Peggy might have created some fake forum posts as if 10 people confirmed her result (Sybil attack).
You could couple that with a PoS like scheme (based on money or reputation) but this would still be shitty.

## Simple addition

Now let's say instead of a long running computation the program is just 32+10 (but the majority of people still don't want to calculate this on their own). In this case bitcoin script could help. Peggy could create a "smart contract" in a sense after a 2 day timelock I can get my 1 BTC back but in the meantime anybody (first one) who comes up with a number X that is not 42 (`42 OP_EQUAL OP_NOT`) and the result of 32+10 (`32 10 OP_SUM`) can take my money. Now if she was lying and do the thing with 43 (trying to convince the world about the wrong result) two things could happen. Either nobody would notice or because there is 1 BTC for grabs Victor (the verifier) could come along, run the computation on his computer and be like oh cool I can just spend that 1 BTC of hers by providing 42 (the fraud proof). By claiming the money he makes everyone a favor since people are able to see Peggy was lying.

\* Actually in such a scheme a miner could take all the money, but oh well, let's ignore that here

## BitVM

The problem is just that bitcoin script is quite basic. It does not allow for constructs we are used from regular programming languages like loops. But this is by design.

BitVM is a way to "chop up" that open-source code everyone has access to into smaller parts and put those on the bitcoin blockchain.
Since tapscript is used you don't need to actually put this gigantic mess on-chain (only the root of that "tree"). Similar as a real computer that is built out of a huge number of logic gates, each part is a collection of boolean gates.

Now Victor can play a game with Peggy on the validity of a specific simple part of the computation.
(All parts need to be ok in order for everything to work out, but we are basically doing just random audits.)
If Victor finds out Peggy was lying similar as previously explained he can publish the fraud proof on-chain (this small part is simple enough so bitcoin can be the "enforcer", afterall bitcoin knows about `OP_AND`, `OP_OR` and `OP_NOT`). Also if Peggy is not cooperative Victor can get her 1 BTC. 

Basically during that X days Victor can do multiple checks (limited by the initial construction). In the end he can be convinced Peggy actually ran the code and got 42 or get 1 BTC richer. In order for Peggy to play along there could be some direct monetary benefit attached for her too.

The remarkable thing here is that bitcoin is able to be the trusted third party for an arbitrary turing complete computation despite
the fact bitcoin script is not able to actually execute it.

## Caveats

The BitVM protocol requires a lot of (off-chain) interactivity ("chatter") between Peggy and Victor. People think it is highly likely Peggy executed the code because there is always the possibility some honest Victor comes along and "exposes" Peggy in a verifiable way on-chain to collect the reward. However this is not a proof. Also the fact some Victor is doing a verification does not help, because Peggy could collude with him. In the end Victor is convinced beyond doubt but he still isn't able to prove Peggy was honest to everyone else.

People say this will enable the construction of Ethereum like smart-contracts on Bitcoin. But I believe this is quite cumbersome and likely not even the primary design goal. BitVM it is more like [TrueBit](https://truebit.io/) protocol for arbitrary computation. Because also on Ethereum despite the fact the language is Turing complete not all computation is possible (beside not being affordable due to high gas price). That is because every node on the network has to redo all the computation in order to check so you can't have something that takes 30 min for instance (unless miners are cheating and just trusting the stuff blindly).

## Lightning comparison

I just realized the stuff is a quite similar to lightning. When a new Victor comes along he basically opens a dual funded channel with Peggy (both contribute 1 BTC). They do a lot of computation up-front and then agree that after X days Peggy will part with 1.1 BTC and Victor with 0.9 BTC. Then if during that time Victor is able to prove Peggy was cheating he "force-closes" the channel and ends up with 2 BTC. But in a similar way Peggy can also flag an "impossible question" from Victor and get 2 BTC. Obviously this requires the monetary incentives to align perfectly or nobody will be willing to open "new channels".

Adam Back also compared the scheme to [ZKCP](https://bitcoincore.org/en/2016/02/26/zero-knowledge-contingent-payments-announcement/).

## More

Read more in the [BitVM whitepaper](https://bitvm.org/bitvm.pdf) or the [tapleaf circuits implementation](https://github.com/supertestnet/tapleaf-circuits/).
