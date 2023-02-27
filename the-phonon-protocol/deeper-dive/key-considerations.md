# Key Considerations

### Payments

A user can use the phonon protocol and the applications built on top of it to send a specified set of phonons (a phonon transfer packet) to a counterparty. The user selects these phonons through the user interface, inputs the recipient’s address, and the transfer is executed.&#x20;

Each party in the exchange relies only on their own client and hardware’s behavior to validate the transaction, the counterparty client is untrusted and the counterparty is only trusted to maintain the specific guarantees provided by the phonon applet.

### Encryption

The private data within the phonon transfer packet must remain securely encrypted from applet to applet (card to card,or secure hardware to secure hardware) in order to prevent a man in the middle from stealing phonon private keys to double spend assets.&#x20;

The public phonon data and the phonon transfer packet as a whole may be encrypted for additional redundant privacy guarantees or for convenience, but it is not strictly necessary.

### Idempotency

Phonon transfers must be receivable once and only once. We strive to make it verifiably impossible for a phonon transfer packet to be received twice, by the same or different applet instances. This is essential as otherwise attacks replaying a phonon transfer could result in doublespends within the protocol.

### Self-Sovereignty

The transfer process should minimize dependency on external systems outside of the communicating phonon clients and secure applets. Ideally, a secure transfer could be conducted solely between two phonon applets connected to an offline client. Dependency on decentralized systems is highly preferred to centralized ones.&#x20;

Additionally, every component of the system outside of the user’s personal card and client must be assumed to be potentially adversarial, with trust extended only with verification.&#x20;

> For instance, a user’s client should not trust their counterparty’s phonon client or card without establishing a clear chain of trust through the counterparty’s secure applet CA signed certificate.&#x20;

This property makes the system more difficult to design than relying on guarantees of centralized services, but it also serves as a key differentiator of the system. If built with truly minimal dependencies, phonon will extend the decentralization guarantees of blockchain systems and enable low cost transactions with a much lower attack surface for fraud and other costly issues than current payment networks such as credit cards.

### Recoverability

Since a phonon typically serves as the sole owner of the private key controlling a digital asset, it can be treated as an exclusive physical bearer instrument. If it is lost, it (like cash, or bearer bonds) is gone for good, so a system for transferring them must be 100% reliable. We can’t just reverse the transaction on the backend like the credit card companies.

Transfers need to be re-sendable from the sending card in the event the recipient does not receive it for some intransigent issue in the middle of a transfer of phonons, such as an electrical failure in a card reader, or a network disruption. The packet can be kept in a sendable state as long as the phonons within it remain:

1. Unavailable to the user of the phonon applet for other purposes (such as redeeming or sending)
2. Exclusive to the original recipient. The phonons in this packet, when replayed, must be possible to receive only by the original recipient.
