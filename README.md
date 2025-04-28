# Trampoline payments
Interesting facts about rendezvous routing

# 1. Trampoline payments improve privacy on the lightning network

On the lightning network, you typically receive money like this: the sender asks the recipient for an invoice, which contains info about how much money to send and where to send it. In a normal lightning payment (before the bolt12 upgrade), "where to send it" meant telling the sender information about your node -- namely, a routing node that you were connected to. Someone who sent you money could log that information and provide it to authorities later, who could then possibly subpoena the routing node for information about the recipient, and trace the payment to its ultimate destination. But in 2018, someone proposed a [solution](https://github.com/lightning/bolts/wiki/Rendez-vous-mechanism-on-top-of-Sphinx): trampoline payments, aka rendezvous routing.

A trampoline payment does *not* reveal a routing node that you're connected to. Instead, it uses a trick that is also used in the tor network: it forwards an encrypted version of your payment to a node who serves as a "rendezvous node" or trampoline. The recipient informs that rendesvous node what to do next -- he might say "send it to this routing node (who I am connected to)" or "send it to the *next* trampoline node," for example.

Eventually, the encrypted payment makes its way to a routing node where the recipient can finally get it, decrypt it, and finalize the payment. Importantly, the payment is encrypted at each "bounce" and the trampoline nodes cannot run off with the money -- they can't even decrypt it to see who the ultimate recipient is, they can just follow their instructions to get it to the next bounce.

# 2. They are growing in popularity

- Trampoline payments are on by default in bolt12
- They are an optional feature in bolt11
- They are on in phoenix wallet by default*
- They are on in electrum wallet by default**
- They are on in voltage LSP by default
- They are on in robosats by default
- They are an optional feature in zeus wallet
- Anyone can make a trampoline payment manually at https://lnproxy.org

*for sending only, unless you're using bolt12  
**for sending only -- you can't create an invoice that uses

# 3. You can make them undetectable

I like it when a privacy technology can be used in an undetectable way. For example, I would like to create a lightning invoice that makes the sender forward the payment to a rendezvous node without him knowing he did so, because if he doesn't even know he did it, he has less information about what happened to the money. But if you create a lightning invoice using bolt12 or the "blinded paths" toggle in Zeus wallet, the sender will see that invoice and *know* he's making a trampoline payment because that information is encoded in the invoice -- it's detectable.

But there's good news: three services -- namely, lnproxy.org, robosats, and voltage -- do trampoline payments in an *un*detectable way. The sender's wallet sees a lightning invoice that looks *ordinary* -- indistinguishable from a "regular" lightning invoice. Those services replace the recipient's pubkey in the invoice with one belonging to the rendezvous node, so the sender thinks he's paying the rendezvous node, not the real recipient. But the rendezvous node needs information from the "real" recipient in order to *settle* that payment, and cannot get it without forwarding the payment. I really like this model of trampoline payments.

# 4. They can fool analysts who try to trace LN payments

Some Lightning Service Providers (LSPs) claim they are able to trace lightning payments to their destination if the sender sends the money using the LSP's wallet. For example, Phoenix Wallet has an FAQ on their website that says: "The current version of Phoenix offers no advantage regarding privacy... We (ACINQ) know the final destination and amount of payments." [source](https://phoenix.acinq.co/faq)

But trampoline payments can be done in an undetectable way, so Phoenix is wrong: they *can't* know the final destination. If the recipient uses an invoice with a rendezvous node, Acinq wouldn't know that. Acinq would think the final destination is the rendezvous node when it's actually whoever set up the trampoline payment. So you can use trampoline payments to break "layer two analysis" heuristics that some companies use to identify the sender or recipient of a lightning payment. You can fool them into identifying the wrong person as the recipient.

# 5. They even help people who don't use them

More trampoline payments means more times analysis heuristics are wrong. And when analysts are frequently wrong in their attempts to trace a payment, they stop being able to rely on their assumptions. Consequently, if lots of people start using trampoline payments, analysts won't be able to assume they can identify the recipient of a lightning payment. Even if most people never end up using trampoline payments, analysts can't know who is using them and who isn't, because they can be done undetectably. Their accuracy doesn't just drop for some, it drops for all. So it is really good for lightning privacy if more wallets adopt the technology that makes trampoline payments work. If more wallets do that, the privacy of the lightning network may greatly improve.
