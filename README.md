# Simple explanation of BitVM

(Bitcoin magazine tried to do a simple explanation too - [THE BIG DEAL WITH BITVM: ARBITRARY COMPUTATION NOW POSSIBLE ON BITCOIN WITHOUT A FORK](https://bitcoinmagazine.com/technical/the-big-deal-with-bitvm-arbitrary-computation-now-possible-on-bitcoin-without-a-fork))

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

Now let's say instead of a long running computation the program is just 32+10 (but the majority of people still don't want to calculate this on their own). In this case bitcoin script could help. Peggy could create a "smart contract" in a sense after a 2 day timelock I can get my 1 BTC back but in the meantime anybody (first one) who comes up with a number X that is not 42 (`42 OP_EQUAL OP_NOT`) and the result of 32+10 (`32 10 OP_SUM`) can take my money. Now if she was lying and do the thing with 43 (trying to convince the world about the wrong result) two things could happen. Either nobody would notice or because there is 1 BTC for grabs Victor (the verifier) could come along, run the computation on his computer and be like oh cool I can just spend that 1 BTC of hers by providing 42 (the fraud proof).

## BitVM

The problem now is just that bitcoin script is quite basic.

BitVM is a way to "chop up" that open-source code everyone has access to into smaller parts and put those on the bitcoin blockchain.
Since tapscript is used you don't need to actually put this gigantic mess on-chain (only the root of that "tree"). Similar as a real computer
that is built out of a huge number of logic gates, each part is a collection of boolean gates.

Now Victor can play a game with Peggy on the validity of a specific simple part of the computation.
(All parts need to be ok in order for everything to work out, but we are basically doing just random audits.)
If Victor finds out Peggy was lying he can publish the fraud proof on-chain (this small part is simple enough so bitcoin can be the "enforcer", afterall bitcoin knows about `OP_AND`, `OP_OR` and `OP_NOT`). Also if Peggy is not cooperative Victor can get her 1 BTC. 

Basically during that X days Victor can do multiple checks. In the end he can be convinced Peggy actually ran the code and got 42 or get 1 BTC richer. In order for Peggy to play along there could be some direct monetary benefit attached for her too.

The remarkable thing here is that bitcoin is able to be the trusted third party for an arbitrary turing complete computation despite
the fact bitcoin script is not able to actually execute it.

## More

Read more in the [BitVM whitepaper](https://bitvm.org/bitvm.pdf) or the [tapleaf circuits implementation](https://github.com/supertestnet/tapleaf-circuits/).
