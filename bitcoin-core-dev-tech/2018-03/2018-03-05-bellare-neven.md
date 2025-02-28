---
title: Bellare Neven 
transcript_by: Bryan Bishop
categories: ['core-dev-tech']
tags: ['multisig']
date: 2018-03-05
aliases: ['/bitcoin-core-dev-tech/2018-03-05-bellare-neven/']
---

See also <http://diyhpl.us/wiki/transcripts/bitcoin-core-dev-tech/2017-09-06-signature-aggregation/>

# Bellare-Neven

It's been published, it's been around for a decade, and it's widely cited. In Bellare-Neven, it's itself, it's a multi-signature scheme which means multiple pubkeys and one message. You should treat the individual authorizations to spend inputs, as individual messages. What we need is an interactive aggregate signature scheme. Bellare-Neven's paper suggests a trivial way of building an aggregate signature scheme out of a multisig scheme where interactively everyone signs everyone's message. We found a vulnerability in this scheme, it's very subtle.

Russell O'Connor came up with this attack. Say you have two outputs with the same key, already a problem, but we can't prevent that. You want to participate in the coinjoin with me where you're just trying to spend one of them and not the other. So we have three UTXOs, and we'll also talk about the messages for each of those UTXOs, messages that authorize the spending of those outputs. Assume these are single input things. And we're trying to do a coinjoin for two of the messages, and I'm trying to steal this output from you. It's explained in the musig paper actually. In short, I am going to participate in the multisignature protocol, so the Bellare-neven lifted to an interactive aggregate signature protocol where I prented my key, where I pretend to have your key and your message, and I will eat the nonce you come up with. I just repeat this, act as a mirror, you say your key is this, and I say my key says this, and you say your nonce and I say a nonce; ther'es a more advanced version where I can hide it actually. I assume you don't validate the message I am trying to sign- that's the key point. In aggregate signature scheme, everyone has their own message, and you should not care which message someone is trying to sign; it should be impossible for anyone to sign with the other key..... the messages will be the same, the two hashes will be the same, between what you think I am going to sign and what I'm actually signing-- I am not saying which of the two we are. With the index, it's secure. Without the index, it's insecure. Bellare-neven has iactually specified this way- in the hash is the list of all the keys, and then your key yourself, but it doesn't say hwat index in that it list it is. The problem is that if those keys are the same, I can use your hash for the first one, and repeat it, and if p1 and p2 are the same, if these two things are the same, while if I'm signing p1 and p2, and ... this doesn't work. We're going to sign a multisignature with 2 times the key value.. if I'm not using the index, then the two things are exactly the same hash, and as a result your partial signature and double it, and it's now a valid signature for both. So the solution is to use an index, and not the public keys. You have to force people to put the indexes.

The advantage of not having indices is that, you're ordering, your local ordering. You just say, this, you do a lexicographic sort on the pubkeys, but you see this as the serialization of your keys, you don't need to have an idea of the ordering of the keys, and in Bellare-Neven paper... all the indices are local indices that don't have to correspond to the other parties indices, and this results in Russell's attacks. It's a very theoretical advantage to have only local indices anyway... I would very much like to see a proof that this construction actually solves this problem. This aspect of converting a multisig scheme into an interactive aggregate signature scheme does not come with a proof in the paper.

You could also fix it by saying never permit reusing a partial signature if any of the other participants claim to have your pubkey. This is also a sufficient fix I think. But I don't trust implementations to do that correctly.

Say you have tuples of the keys and messages. You lexicographically sort those tuples. And then say L is the hash of that whole thing. And then you use the Bellare-Neven equation becomes... and now you don't even need to .... this ever separate the message from the pubkey. It's a signature scheme, only list of pubkey message pairs rather than sets of pubkey message pairs. Sets are much harder than lists.

I am looking for someone who can prove this, because I can't.
