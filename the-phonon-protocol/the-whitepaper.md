# The Whitepaper

## ABSTRACT

A purely peer-to-peer version of electronic cash would allow for both offline and online payments to be sent directly between one party to another without going through a financial institution. Existing blockchains are part of the solution, but to create a digital-payment system that is truly analogous to cash, there must be a way to transact digital assets without the need to publicly broadcast transactions to a common database. We propose a protocol that utilizes secure hardware based on Physical Unclonable Functions (PUFs) and signed certificates to secure a data structures containing private keys. That private key cannot be accessed without destroying the structure itself. Any value associated with that key can now be transacted in a truly peer-to-peer fashion, between certified hardware running an applet that prohibits double spends, and only interacts with other certified applets. By securing the protocol within certified applets instead of through a centralized-consensus mechanism, we create a payment platform that has no fees, scales linearly with the number of users and has no need to broadcast transactions. All transactions are private by default, and the system is antifragile. It does not incur scaling limitations that hamper the development of current generation payment systems.

### 1. Introduction

_A protocol that securely sends a private key via a simple data transfer can effectively make payments that are blockchain-secured._

Instead of using a private key to authorize a transaction of _n_ value on _x_ blockchain, a private key is created in a secure enclave, where the public-key address and, innately, the funds are derived from a normal, on-chain blockchain transaction. Creation and exchange of this data structure requires a certified firmware applet that prohibits double spends. The responsibility to ensure a transaction’s security shifts from a blockchain to the device manufacturer who certifies the Java applet on Phonon-compatible hardware (e.g., eSIM or smart card). Using crypto goes from a multi-step process that requires an internet connection and an elaborate consensus mechanism, to a simple peer-to-peer transaction, in line with Satoshi’s original vision of digital cash.

#### **1.1 Overview - The Phonon Protocol**

The Phonon Protocol is a system for the secure storage and exchange of digital assets encumbered by private keys, allowing for remote transfers over a network such as the internet as well as local operation without requiring an internet connection. peer-to-peer payment channel independent of the internet. It leverages the Physically Unclonable Function ([PUF](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6679326/#sec2-sensors-19-03208)) of an eSIM or Smart Card to create a Trusted Execution Environment (TEE) and run an applet which allows for the secure storage and transfer of digital assets. Transactions are fully contained between the two participating applets.

Protocol specifications and code have been developed into a working system that is stored across the following repositories:

1. The Phonon Applet: [https://github.com/GridPlus/phonon-card](https://github.com/GridPlus/phonon-card)
2. The Phonon Client: [https://github.com/GridPlus/phonon-client](https://github.com/GridPlus/phonon-client)
3. The Phonon Wallet: [https://github.com/GridPlus/phonon-app](https://github.com/GridPlus/phonon-app)

_Note:_ These are only reference implementations of the protocol, not the only possible implementations.

<figure><img src="https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh7oS0A9GOuASk7F5RapF%2Fuploads%2FQitDuN8BMPmDSBSHnzqU%2Fspaces_j92rA3iiOZk1c2d2vnc9_uploads_7KOYfNkhcYR9X39sG4rT_0.png?alt=media&#x26;token=c397d062-b4d2-4565-826b-83a34e5d3dca" alt=""><figcaption></figcaption></figure>

### 2. ENCUMBERED ASSETS ON THE PHONON PROTOCOL

<figure><img src="https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh7oS0A9GOuASk7F5RapF%2Fuploads%2Fit5WvaIdupSV8ZrU8aPC%2Fspaces_j92rA3iiOZk1c2d2vnc9_uploads_iKranWXXbVx9n8Z81A6x_1.png?alt=media&#x26;token=7a21fdfc-b4b3-40e9-8656-aa6a7d4cc26c" alt=""><figcaption></figcaption></figure>

An asset on the Phonon Protocol is a representation of a public-private key pair and information that describes a digital asset encumbered to that key pair (e.g. a Bitcoin UTXO, or ERC20 token on Ethereum). In this way, exchanging assets is a secure mechanism of exchanging private keys and consequently exchanging ownership of the encumbered digital asset without ever broadcasting a transaction to the asset's associated network / blockchain.

Traditionally, exchanging private keys would expose the recipient to significant counterparty risk as they have no way to confirm the sender did not keep a copy of the private key. This opens the possibility of the sender stealing the assets back to attempt a double-spend action. The Phonon Protocol solves this problem by guaranteeing private keys are never known outside of the secure applet and are only transferred between Phonon Smart Cards (PSMs) which can be trusted to atomically delete its copy of the key after it has been transferred (i.e., a card running the Phonon applet). In this way, when transferring digital assets between PSMs, the receiver can be confident that the received private key is only known to his card, and therefore the encumbered digital assets are solely his to control. To ensure keys are not duplicated, PSMs may only transact with other valid PSMs, which can be trusted to abide by the Protocol. Accordingly, each card first checks if the other card possesses a certificate signed by a valid issuer via an authentication process prior to any transfer being initiated. Communication between PSMs is encrypted using the Cards' unique keys (generated from entropy during PUF creation) which prevents transactions from being broadcast to multiple PSMs.

This functionality relies on the following key principles:

1. A Phonon asset’s private key must be known only to a single trusted Phonon card.
2. When an asset leaves a card, that card will destroy its record of the asset.

Interaction in the Phonon Protocol involves two key technologies:

1. Phonon Card - A secure environment that executes secure atomic phonon transfers.
2. Phonon Terminal - A card terminal to provide a user interface and facilitate transfers between Phonon Smart Modules.

### 3. PHONON CARD

A Phonon card is a secure environment to store and transfer assets on the Phonon Protocol. There are three primary ways to interact with the Protocol:

1. Create encumbered assets by locking digital assets to a Phonon key (e.g. on a blockchain);
2. Transfer Phonon assets between PSMs; and
3. Destroy encumbered assets (export Phonon private key to unlock its encumbered asset(s)).

Each of these three interactions are described in further detail below. A detailed description of the card-command interface may be found in the card specifications and documentation.

#### 3.1 Creating encumbered assets.

The private key for each encumbered asset must be known only to the card. As such, to create an encumbered asset, a user must ask the card to generate a new keypair and to display only the associated public key. The user can then use that public key to commit assets on a blockchain to an address derived from that public key (e.g., create a Bitcoin address from pubkey and send some BTC to that address). The user must also provide the card some information about the asset that is encumbered. In the future, when it comes time to withdraw the value, this data will give the user enough information to redeem the associated digital asset (e.g., which blockchain & address format this asset exists on).

The creation process goes as follows:

1. User requests a new Phonon public key from the card.
2. User deposits digital assets on a blockchain to an address corresponding to this public key.
3. User finalizes encumbrance, by storing metadata about the associated digital asset that exists at the public address from step II (blockchain network, address format, etc) to the card.

#### 3.2 **Transferring assets.**

Phonon assets may only be transferred to and from authentic Phonon cards. This prevents exchanging with malicious devices that may duplicate or leak encumbered private keys. This is necessary to provide guarantees around the prevention of double-spends within the protocol. To achieve this, each Phonon card is provided with a certificate signed by a valid issuer\[1]. Before transferring assets, a card must check that the other party has a validly signed certificate to prove that it is running an authentic Phonon applet. Then, a transaction can be built and encrypted using a shared secret only known to those two PSMs, via ECDH key exchange. When the sender's card emits the transaction, the sender's card automatically deletes the asset internally, so that the private key will not exist in more than one location.. At this point, the encrypted transaction should now be treated as a collection of assets. The receiver's card (and only that card!) can import and receive the transaction. Consequently, if the transaction is lost or the receiver's card is lost, the assets in that transaction will be unretrievable.

There is an additional protection required to prevent replay attacks. As described up to this point, a transaction could theoretically be replayed _n_ times to the receiver's card, tricking the card into receiving the asset _n_ times. To prevent this, we require that each transaction correspond to an invoice ID from the receiver. This ensures that replayed transactions will be detected and discarded, as they all refer to the same invoice ID. In practice, this adds negligible complexity, because this extra invoice ID can be sent with the receiver certificate at the beginning of the transaction. This invoice ID is simply a unique nonce, and requires no information about the details of the transaction.

Lastly, to prevent certain attacks where an attacker may wish to DoS your card with bogus assets, the card will provide the ability to let a terminal build an allow-list of assets that may be accepted by the card. With this approach, a terminal will negotiate the details of a transfer between two Phonon Smart Cards (and check the sender's asset public keys and metadata via an API call to the associated blockchain for validity. Once the transfer details are confirmed, the terminal can provide its allow-list. This ensures the card will only accept assets the terminal has already deemed valid, thus preventing the need for a lengthy interactive process with the card to detect and remove bogus assets.

#### **3.3 Dissolving assets.**

Eventually a user may wish to unlock and redeem the digital assets associated with an encumbered asset. In this case, the user can specify a number of assets to be exported from the card. The card will respond by exporting each requested asset (i.e., its private key and descriptive data), so that the user can build the appropriate blockchain transactions to redeem the associated digital assets on-chain. It is important to note that the act of exporting an encumbered asset’s private key and data subsequently destroys the asset. The phonon-bound asset will be obliterated from the card, no longer transferable on the Phonon Protocol. The user will now have the private key and the ability to move the on-chain value to a wallet of their choosing. This is a one-way and irreversible decision. Once a private key is emitted from a Phonon card it can never be added back to the card.

### 4. PHONON TERMINAL

A Phonon terminal provides a physical interface to a PSM (smart card or eSIM), as well as communication backend(s) between PSMs to facilitate asset transfers. The Grid+ Lattice1 is one such example, though transfers may be facilitated via any number of communication backends between two PSMs. See _Transfer Backends_ for a list of supported backends.

When receiving an asset, it is important for the receiver to check that the asset actually has value locked up on the appropriate blockchain at the specified location (address). For this reason, a terminal must also provide the ability to read the assets on a card and check the value of those assets on the appropriate network. See _Supported Assets_ for more information about supported asset types.

See the terminal specifications and documentation for more detail about the functional design requirements of a Phonon terminal.

#### **4.1 Transfer backends.**

At this time, we specify the following transfer backends a terminal may choose to implement. More backends may be added in the future:

1. Local transfer between one external card and one card embedded internally to the terminal.
2. Remote transfer between a local card and a remote card, via network connection to a remote terminal.

A Phonon terminal must have the ability to transfer assets between Phonon Smart Cards via at least one of the specified backends.

#### **4.2 Asset verification.**

An encumbered asset stores the private key with an associated public-private keypair. It is intended that this keypair is used to hold assets on an external network / blockchain. A descriptor is then programmed to the asset that describes which assets are encumbered and how. This allows an encumbered asset to be transferred without broadcasting a transaction to the blockchain with every transfer.

This encumbrance, however, depends entirely on the consensus of that external network. As such, **it is up to the terminal to check that the encumbrance exists as expected**. For example, a user may create an asset and encumber BTC, ETH, or any other digital value (including assets on hard forks like those of ETC or BCH) with the asset’s public key. It is incumbent on the terminal to read the public key and descriptor of each asset and check the appropriate network to confirm the assets are indeed encumbered to the asset’s on-chain, public address.

#### **4.3 Supported assets.**

An encumbered asset may hold many different types of value. To create, verify, migrate or dissolve an encumbered asset, a terminal must know how to interpret the type of value encumbered on that asset. That work is left to the terminal to implement. The Phonon Protocol at present supports any crypto-asset that uses the Secp256k1 signing curve.

Signing curve. Secp256k1 K curve Elliptic Curve Cryptography

_See_: [Card specifications](https://github.com/GridPlus/phonon-network/blob/master/card-specification.md) for a detailed specification of the asset structure, data formats, and card commands to create/transfer/dissolve an encumbered asset

#### **4.4 Viewing card balance.**

A terminal should provide the option to view a card's balance. This is a two-step process. First, the terminal should request asset descriptors from the card to discover every asset possessed by the card. Second, the terminal should check the appropriate blockchain(s) for validity of each encumbered asset, as described in each asset’s descriptor.

_Caution: The information in an asset descriptor must be externally validated on the appropriate blockchain(s)! The card has no knowledge of external networks, and thus cannot guarantee validity of encumbered assets._

#### **4.5 Transaction building.**

A terminal provides the ability for two PSMs to transact with each other via one of the transfer backends described above. The user may interact directly with the terminal to initiate a transaction, or the terminal may attempt to automatically initiate a transaction (e.g. in a point of sale scenario). We will leave these details to the terminal implementation, and focus solely on the construction of a transaction.

_Note:_ A transaction may involve only one terminal (i.e. a local transfer) or two terminals (i.e. a remote transfer). Remote transfers are an extension of local transfers, in which the transaction structure is identical, but the terminals require an extra step(s) to negotiate the details of the transaction via a remote connection. The details of a remote connection are application dependent, and we wish to focus solely on the construction of a transaction. Thus, this section will be described in the context of a local transfer only.

**To build a transaction, the terminal must perform the following procedure:**

1. Discover relevant assets on the sender's card.
2. Discover relevant assets on the receiver's card.
3. Check all encumbered assets for validity on the appropriate blockchain(s).
4. Select the assets the sender should send.
5. (If applicable) Select assets the receiver should send back as change, or for an agreed upon swap.
6. Instruct the sender's card to build a transaction to send the selected assets.
7. (If applicable) Instruct the receiver's card to build a transaction to send the selected change assets.
8. Provide the receiver's card with the encrypted transaction from the sender.
9. (If applicable) Provide the sender's card with the encrypted change transaction from the receiver.

_Caution: This transaction process may expose one or both parties to counterparty risk (e.g. if the receiver removes his card before transmitting the change transfer). At minimum, users should be made aware of their counterparty risk. A smart terminal may even take steps to reduce this risk, by breaking the transaction into smaller pieces, or requiring both PSMs to send their half of the transaction before receiving the other half (although, this has a different set of tradeoffs to be considered). These decisions are application dependent, and thus we leave these details to the terminal implementers._

<figure><img src="https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fh7oS0A9GOuASk7F5RapF%2Fuploads%2FrWeZ0g2o7cizmbqLUvGD%2FScreenshot%202022-11-23%20at%207.04.21%20AM.png?alt=media&#x26;token=46e253b2-1861-4039-b57a-b5b7c35aa469" alt=""><figcaption></figcaption></figure>

## Conclusion

We have proposed a system for digital asset transactions without relying on an ongoing, centralized consensus mechanism.Within the framework of existing blockchains which use private keys, we propose an open source protocol that allows trustless, peer-to-peer transactions of the keys themselves. The ability to store on-chain values as a simple data structure grants scaling, privacy, and interoperability to any blockchain. On top of such a protocol layer, utilities such as true cross-chain exchanges, off-chain micropayments, fully private transfers and more can be built._Definition._ Encumbered Asset(s): A private key that has been generated in secret within a Phonon Applet, and whatever value is associated with the key’s public address._Definition._ Physically Unclonable Function (PUF): a way to use intrinsic characteristics of silicon chips to uniquely identify a device.\[1] _Valid Issuer_: A valid issuer is a verified, smart-card manufacturer that has been fully vetted by Phonon DAO and bonded to manufacture secure, PUF-enabled Phonon Smart Cards. The bond is held in accordance to manufacturing standards set forth by the DAO to ensure no malfeasance occurs in the partnership.
