# Phonon Secure Backend Specification
This document specifies interactions required by the phonon secure backend from a high level. This is primarily meant for developers seeking to implement the phonon protocol on secure hardware devices and curious technical people. 

## General

### Terminology
**phonon sce** Stands for phonon Secure Compute Environment. A device implementing the phonon secure backend, meant to be used in conjunction with the phonon terminal

**terminal** A device used to communicate with the phonon sce as well as allow the user a simple interface to do transactions and asset management

**phonon**  For the purposes of defining interactions, a phonon is defined as a data structure containing the private key generated within the phonon sce, and the descriptor

**descriptor** A portion of the phonon describing the asset/assets associated to the private key that it shares a phonon with

**bob** A fictional phonon user generally wishing to send phonons to alice

**alice** A fictional phonon user generally wishing to receive phonons from bob

**phonon transfer packet** the encrypted data containing a number of phonons while in transit between phonon sce units


### General Constraints
all phonon interactions must adhere to the following Constraints
1. A phonon's private key may not be accessible by more than a single entity at one time
1. A phonon's private key MUST remain secret unless the phonon is destroyed at or before the time of the phonon being viewable by an outside system or entity
1. A phonon private key may not be transferred to a phonon sce that has not been verified by the sending phonon sce
1. A phonon private key may not be received by a phonon sce that has not verified the sender

### Encoding
For the purposes of this document, any data being described will be in JSON format.  
Data sent between the terminal and the phonon sce will be implementation dependent.  
Data sent between the phonon sce and another phonon sce will be in CBOR format.

## Interaction descriptions
### Certificate Authority loading
The manufacturer of the phonon sce, to indicate that the phonon sce is legitimate, will instruct the phonon sce to store a <CERTIFICATE_AUTHORITY_CERTIFICATE_TYPE> certificate. The purpose of this certificate is to verify with other phonon sces that they are also legitimate. 

1. Manufacturer runs LOAD_CA command 
#### Constraints
1. Once set, this may not be changed. 

### Certificate signing
1. GET_PUB_KEY command is run
1. Public key of phonon sce is signed by the manufacturer with the certificate authority private key, and a certificate is generated
1. LOAD_CERT command is run with the newly generated certificate

### Personal security setup
The phonon sce SHOULD be secured against unauthroized access by some sort of identification. For personal use, a pin is likely sufficient, though for corporate use, Key authentication may be used or other forms of protection
1. ESTABLISH_SECURITY command is run
1. user verifies log in to the system with a login challenge

#### Constraints
1. The owner MUST set this up before phonon creation and transfers can occur

### Phonon Creation
Phonon creation connsists of the generation of a public/private keypair within the phonon sce, the expression of the public key to the client application, A likely transaction on a blockchain to encumber assets to the phonon's associated wallet, and a call back into the phonon sce to set the descriptor of the phonon
1. GENERATE_PHONON command is run
1. The private key and relevant addressing id is returned to the client
1. the client handles generating relevant crypto wallet address and encumbering assets to it
1. SET_DESCRIPTOR command is run

### Phonon Listing
There must be a way to obtain a list of existing phonons for further use by the user
1. LIST_PHONONS command is run

### Remote Connection (depricated)
A connection on the client level must be established in order for the secure connection to be established between phonon sce units. There are many ways this can be done, and will be ommitted from this document

### Secure Connection (depricated)
this is complicated enough that it's not worth the effort to get it right in this document due to it's depricated status. 
### Phonon Transfer (depricated)
A transfer of phonons between bob and alice assumes the secure connction between both bob and alice's phonon sces are engaged in a secure connection
1. bob initiates SEND_PHONONS command through the clint
2. the resulting phonon transfer packet is sent over the remote connection to alice's client
3. Alice's client runs the RECEIVE_PHONONS command with the phonon transfer packet payload against her phonon sce

### Phonon Transfer
A transfer of phonons from Bob to Alice
1. Bob retrieves Alice's Phonon sce certificate
2. Bob initiates SEND_PHONONS command through the client
3. The resulting transfer packet is sent to Alice's client
4. Alice's client rust the RECEIVE_PHONONS command with the phonon transfer payload against her phonon sce

### Phonon Redemption
a user retrieves the private key of a phonon and utilizes it to do transactions on a blockchain
1. Bob initiates the DESTROY_PHONON command
2. The resulting private key is used in an asset dependent way to move the asset previously encumbered in the phonon to a permanent wallet

# Command Glossary
## LOAD_CA
This is to load the phonon certificate authority Public Key. Phonon sce devices to be transferred to will have their certificate signatures checked against this key to verify the authenticity of the phonon device.
### Inputs
	```json
	{"Certificate":[binary data]}
	```

### Outputs
	```json
	{}
	```

## INFORMATION
the phonon sce will return certificate information. Only the identity public key will be returned upon first run, and all the specified information will be returned after LOAD_CA has been run
### Inputs
	```
	none
	```
### Outputs (after certificate loading)
	```json
	{
		"pubkey": "binary",
		"cert": "binary",
		"name": "string",
		"nonce": "binary",
		"counter", "integer or binary?",
	}
### Outputs (pre certificate loading)
	```json
	{
		"pubkey": "binary",
	}
## LOAD_CERT
The phonon sce will store its identity certificate. the identity certificate will be stored to be returned in the INFORMATION command
### Inputs
	```json
	{
   	"pub": "public key point as byte string",
   	"iat": "uint32",
   	"exp": "uint32",
   	"ver": "int, device firmware version"
	}
	```
### Outputs
	```
	none
	```

## ESTABLSIH_SECURITY
This command is to setup the security mechanism on the phonon sce. This is implementation dependent but SHOULD appropriately lock out unauthorized users from utilizing the phonon sce it is set to. 
### Inputs
	```json
	[implementation dependent]
	```

### Outputs:
	```json
	[implementation dependent]
	```
## UNLOCK
This comand is to unlock the phonon sce for use. Before this command is successfully run the phonon sce should not respond to calls to other commands
### Inputs
	```json
	[implementation dependent]
	```

### Outputs
	```json
	[implementation dependent]
	```
## GENERATE_PHONON
A phonon keypair will be generated on the phonon sce, and it's public portion will be returned
### Inputs
	```json
	{"type":[curve type])
	```
note: curve type is a string and currently the only option is "secp256k1"
### Outputs
	```json
	{"pubkey":"binary data"}
	```
## SET_DESCRIPTOR
### Inputs
	```json
	{
	"arbitrary keys":"arbitrary values"
	}
	```

### Outputs
	```json
	```
## LIST_PHONONS
### Inputs
	```json
	[optional]
	```json
### Outputs

	```json
	[
	{
		"header": {
			"curve": "secp256k1",
			"pub": "public key bytes"
			},
		"metadata":{
			"xyz": "abc",
			"something else":"another value",
		}
	]
	```
note: some implemntations will allow for filter queries to be input to cut down on the amount of phonons returned in a single request
## DESTROY_PHONON
### Inputs
	```json
	todo
	```

### Outputs
	```json
	```
