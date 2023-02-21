# Phonon Roadmap
## v0.0.0
- proof of concept of phonon creation, transfer, and redemption

## v0.0.1 pre-alpha
- phonon dev kit release

## v0.1.0 alpha (test-net)
- phonon desktop client release
- JumpBox based internet transfer

# WE ARE HERE
## v1.0.0 stable beta
- one way, single message transfer topology spec
A pared down protocol spec that allows for one-way transfers without the previous secure channel implementation. This is meant to simplify the programming (and therefore surface area) of the backend phonon devices
- updated desktop client
A newer, more thought out design than the previous one, allowing for a more straightforward interface that is more intuitive for the end-user
## v1.1.0 open release
- fixes for any issues that come up in the stable beta
- NFC mobile client
A client written for mobile devices to allow for phonon usage on-the-go utilizing the NFC interface instead of requiring a bulky card reader to handle phonon transactions and transfers
- pure peer-to-peer transfer
Updated transfer methods that don't require the use of an intermediate service to handle sending and receiving phonons. Likely will be Bluetooth, with a backup based on encoding transfers into qr codes to be scanned by client devices
- phonon inbox service
A service that acts as an intermediary between clients to allow for sending phonons to a user that is not online at the moment

## v2.0.0 advanced features beta
- two-way atomic swap
An additional transfer topology that allows for trading some phonons for others while minimizing counterparty risk and keeping with the security requirements
## v2.1.0 advanced features release
- fixes for any issues that come up in stable beta

