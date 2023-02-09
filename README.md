---
description: New to phonon? Start here.
---

# Getting Started

The following explanations of the Phonon Protocol are written in two formats, simple and technical.  The goal is to better understand the protocol, no matter what your technical experience is.&#x20;

## What is a phonon?

{% tabs %}
{% tab title="Simple Explanation" %}
**A phonon is something that secures and holds a digital asset**, such as an Ether amount or an NFT. To elaborate on this a bit more, a phonon is like a safety deposit box, containing a single asset that can be transferred to another party, without history of the transaction, without the internet, in a true peer-to-peer transfer. In a similar fashion as physically handing over a safety deposit box to your friend, you can do this with phonon.  Phonon has many of the same properties of cash, digitally.  And much more.

Phonon has the potential to change the way we exchange assets, conduct commerce and ultimately have true ownership of our assets. The Phonon Protocol is reshaping what digital cash could be, with the agnosticism of the underlying asset. It's a mission to Mars, but in the same sense, monumentally achievable.
{% endtab %}

{% tab title="Technical Explanation" %}
**A phonon is a representation of a public/private key pair and ancillary information describing the encumbered digital asset** (e.g. a Bitcoin UTXO or ERC20 token) to that key pair. In this way, exchanging phonons is a secure mechanism of exchanging private keys, and consequently exchanging ownership of a digital asset without ever broadcasting a transaction to the asset's associated network/blockchain.

Traditionally, exchanging private keys would expose a user to significant counterparty risk; the recipient has no way of knowing that the sender didn't keep a copy of the private key to steal the assets back. **The Phonon Protocol solves this problem, by ensuring private keys are never known publicly, and are only transferred between cards which can be trusted** to atomically delete its copy of a phonon after it has been transferred. In this way, when transferring between Phonon Cards, the receiver can be confident that the received private key is only known to his card, and therefore the digital assets encumbered to his phonon's key are solely his. To ensure phonons are not duplicated, cards will only transact with other valid Phonon Cards, which can be trusted to abide by the network rules. Accordingly, each card first checks if the other card possesses a certificate signed by a valid issuer. Communication between cards is encrypted using the cards' unique keys, to prevent transactions from being broadcast to multiple cards.

This functionality relies on the following key principles:

* A phonon private key must be known only to a trusted Phonon Card (until withdrawal from the network).
* After sending or withdrawing a phonon from a card, that card will destroy its record of the phonon.

Learn more by <mark style="color:red;">reading the Phonon Protocol</mark>. The Phonon protocol software is open sourced and <mark style="color:red;">available here</mark>.
{% endtab %}
{% endtabs %}

## What is a Phonon Card?

{% tabs %}
{% tab title="Simple Explanation" %}
**A Phonon Card is like your own personal bank** which holds your phonons (think safety deposit boxes).  The card is secured by a PIN you can set. When you unlock the card, you can see the list of phonons held on the card.  You can also create new phonons, transfer phonons (to another Phonon Card) and redeem phonons.

<figure><img src=".gitbook/assets/phonon-card-small.png" alt=""><figcaption><p>Example of a Phonon Card</p></figcaption></figure>

**A Phonon Card has a similar look and feel as a modern credit card** which uses the same chip technology as your Mastercard or Visa card.  The Phonon Card has a chip exclusively. There is no magnetic strip on the back.  When the card is inserted into a card reader that interacts with a device with phonon software, you can perform the actions mentioned above.
{% endtab %}

{% tab title="Technical Explanation" %}
A Phonon Card leverages the hardware security of secure-elements/HSMs to allow the secure storage and transfer of phonons. When a new card is issued, the card's security module is flashed with the Phonon Protocol applet and a certificate for creating future phonons.  The certificate authority is owned and managed by the Phonon DAO <mark style="color:red;">(??)</mark>.&#x20;

**Orchestrating a secure phonon transfer** requires a specific set of steps to be accomplished in a specific manner, in order to properly leverage the security guarantees provided by multiple systems, such as the Phonon Card secure applet, the phonon client, the underlying phonon asset’s original blockchain, as well as the same stack on the counterparty’s side.

> As we know, a phonon is essentially a data structure containing the requisite information needed to control a digital asset.

**Typically**, this is a private key along with metadata describing how to locate the asset on its origin blockchain. Phonons have private data, typically the private key, which remains hidden from all, including the owner of the phonon, and public data, typically everything else such as the originating chain name, the description of the asset, and its on chain value.

While the **Phonon Card applet itself guarantees the secrecy and security of the asset private keys**, it is still necessary that care be taken when transferring phonons between counterparties to ensure that each party is sending and receiving the digital assets they intend.&#x20;

> **For example**, a recipient of a phonon needs to check that the phonon they are receiving actually represents the digital assets it claims to. Since Phonon Cards inherently only possess information that their users have given them, the public metadata (aka phonon descriptor) for each phonon is entered by the user without any verification. The consequence of this is that phonon descriptor information is inherently insecure, it must be checked by the receiving counterparty in order to ensure its veracity.

Essentially, the only security guarantee about a phonon that the secure applet can make is that it does in fact possess the private key corresponding to a certain public key, and that that private key has remained hidden inside of a phonon during its entire lifecycle and will continue to do so until it is redeemed. **This is the core property that prevents double spends**, but it means that the client will have to take on the additional responsibility of validating that phonon assets intended for transfer are legitimate blockchain assets.

To interact with the Phonon Card applet,&#x20;

There are multiple interfaces that are available to interact with the Phonon Card.  You can <mark style="color:red;">learn more about these interfaces here</mark>.

The Phonon Protocol applet software is open sourced and <mark style="color:red;">available here</mark>.
{% endtab %}
{% endtabs %}

## How does Phonon work?

{% tabs %}
{% tab title="Simple Explanation" %}
There is essentially 3 core functions behind how phonon works.

1. Creating a phonon
2. Transferring a phonon
3. Redeeming a phonon

Here's an overview of how each works:

#### Creating a phonon

A phonon is created by first securing a digital asset onto a blockchain and then secured onto a Phonon Card. This can be done in numerous ways. For example, you could use the Phonon Manager to create a phonon from an asset you have stored in a wallet such as MetaMask. The creation of the phonon will hold the keys on the Phonon Card, much like how a wallet such as MetaMask held the keys to that asset. You're essentially transferring the keys into a phonon. This means you can only access that asset encumbered within a phonon by having access to the Phonon Card itself. The Phonon Protocol software has the ability to do things such as transfer or redeem said phonon.

&#x20;&#x20;

#### Transferring a phonon

A phonon is transferred by sending someone the phonon. In short, the phonon is sent to a recipient, in which only the recipient's card is eligible to accept the transfer.  The phonon is sent in an enclosed message and can be send using common methods including email, SMS, and WhatsApp.  When the user receives the phonon transfer, they can choose to accept or deny the transfer. If denied, then it is returned to the sender. The send would need to re-add it to their card by accepting the return transfer.  Otherwise the recipient can accept the transfer and it will be added to the intended recipients Phonon Card.



#### Redeeming a phonon

A phonon can be redeemed for it's encumbered asset by making a request using software such as Phonon Manager. Redeeming an asset will require the user to determine where it's being redeemed to, such as a MetaMask wallet. Redeeming will transfer the asset to the requested destination, destroying the originating phonon in the process.
{% endtab %}

{% tab title="Technical Explanation" %}
There is essentially 3 core functions behind how phonon works.

1. Creating a phonon
2. Transferring a phonon
3. Redeeming a phonon

Here's how each works, technically:

#### Creating a phonon

The private key for each phonon must only be known to the card. As such, to create a phonon, a user must ask the card for a public key to a new phonon. The user can then use that public key to commit assets on a blockchain to that phonon (e.g. create Bitcoin address from public key and send some BTC to that address). The user must also provide the card some information about the asset that the phonon represents. When it comes time to withdraw a phonon, this information will give the user enough information to redeem the associated digital asset (e.g. which blockchain & address format this phonon asset exists on).

The creation process goes as follows:

1. User requests a new phonon public key generated from the card.
2. User deposits digital assets on a blockchain to an address corresponding to this public key.
3. User finalizes phonon creation, by providing info about the associated digital asset (blockchain network, address format, etc).

####

#### Transferring a phonon

Phonons may only be transferred to/from another authentic Phonon Card. This prevents exchanging with malicious cards that may duplicate or leak phonon private keys. This is the key to preventing double-spending on the network. To achieve this, each Phonon Card is provided with a certificate signed by a valid issuer. Before transferring phonons, a card must first check that the other party has a valid signed certificate, and possesses the private key that was certified. Then, a transaction can be built and encrypted using a shared secret only known to those two cards (via ECDH key exchange). When the sender's card emits the transaction, the sender's card automatically deletes the phonon(s) internally, so that it will not be able to spend it twice. At this point, the encrypted transaction should now be treated as a collection of phonon(s). The receiver's card (and only that card!) can import and receive that phonon transaction. Consequently, if the transaction is lost or the receiver's card is lost, the phonon(s) spent in that transaction will also be lost.

There is one more detail necessary to prevent replay attacks. As described up to this point, a phonon transaction could theoretically be replayed `N` times to the receiver's card, tricking the card into receiving the phonons `N` times. To prevent this, we require that each transaction correspond to an invoice ID from the receiver. This ensures that replayed transactions will be detected and discarded, as they all refer to the same invoice ID. In practice, this adds negligible complexity, because this extra invoice ID can be sent with the receiver certificate at the beginning of the transaction. This invoice ID is simply a unique nonce, and requires no information about the details of the transaction.

Lastly, to prevent certain attacks where an attacker may wish to DoS your card with bogus phonons, the card will provide the ability to let a terminal build a whitelist of phonons that may be accepted by the card. With this approach, a terminal will negotiate the details of a transfer between two cards (and check the sender's phonon public keys on chain for validity, etc). Once the transfer details are confirmed, the terminal can provide a whitelist of phonons to accept. This ensures the card will only accept phonons the terminal has already deemed valid, thus preventing the need for a lengthy interactive process with the card to detect and remove bogus phonons.

####

#### Redeeming a phonon

Eventually a user may wish to unlock & redeem the digital assets associated with a phonon. In this case, the user can specify a number of phonons to be exported from the card. The card will respond by exporting each requested phonon (private key and asset data), so that the user can build the appropriate blockchain transactions to redeem the associated digital assets. Importantly, the act of exporting a phonon's private key _destroys_ that phonon. That phonon will be deleted from the card, and no longer transferable on the Phonon Network.
{% endtab %}
{% endtabs %}



## How can I try phonon?

{% tabs %}
{% tab title="As a user" %}
Currently, the Phonon Protocol is geared more towards tech savvy enthusiasts and developers. Future software releases, including the release of the upcoming Phonon Manager will make it easier to use phonon as an everyday user.

If you'd still like to try phonon, check out the "_As a developer_" tab.
{% endtab %}

{% tab title="As a developer" %}
You'll need to have a testnet kit. If you don't have one, unfortunately, they are no longer available.  However, you can still download the software and test it with a mock card. &#x20;

[Download pre-release Phonon Client](https://github.com/GridPlus/phonon-client/releases/tag/latest)

**Note:** A mock card is a simulated, virtual card that allows to to test the protocol without real assets.  Please note that mock cards get destroyed when you close the software.  Proceed at your own risk.

When the beta version of the Phonon Protocol is released, we will announce more in Discord.  Make sure to [join our Discord](https://discord.com/invite/NkcSBGfG).
{% endtab %}
{% endtabs %}

## How can I contribute to Phonon?

{% tabs %}
{% tab title="I'm a technical/developer/cryptographer" %}
If you'd like to technically contribute to the Phonon Protocol, here's a few places to get started:

* [Discord](https://discord.gg/NkcSBGfG) - This is where the core conversations happened, check out the group of channels under TECHNICAL DISCUSSIONS. Feel free to introduce yourself and join the discussions.
* [Documentation](the-phonon-protocol/) - Get familiar with what phonon is, how the protocol works, and how to get started with development.
* [Github Project](https://github.com/orgs/GridPlus/projects/18) - This is where we keep track of the progress of work done on the protocol.
{% endtab %}

{% tab title="I'm NOT technical, but want to contribute" %}
If you'd like to  contribute to the Phonon Protocol, but don't have the technical background, here's where to get started:

* [Discord](https://discord.gg/NkcSBGfG) - This is where the core conversations happened, check out the group of channels under WORKING GROUPS. Feel free to introduce yourself and join the discussions.
* [Documentation](the-phonon-protocol/) - Get familiar with what phonon is, how the protocol works, and how to use phonon.
* [Github Project](https://github.com/orgs/PhononDAO/projects/1/views/1) - This is where we keep track of non-development progress for things related to phonon. If you'd like to contribute artistically (illustrations, animations, etc.), you'll see what we're looking to complete here.  If you have other ideas, we'd love to hear them! Connect with us in Discord.
{% endtab %}
{% endtabs %}
