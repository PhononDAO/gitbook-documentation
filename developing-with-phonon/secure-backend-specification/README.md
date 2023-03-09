# Phonon Secure Backend Specification
This document specifies interactions required by the phonon secure backend from a high level. This is primarily meant for developers seeking to implement the phonon protocol on secure hardware devices and curious technical people. 

## General

### Terminology
**phonon sce** Stands for phonon Secure Compute Environment. A device implementing the phonon secure backend, meant to be used in conjunction with the phonon terminal

**terminal** A device used to communicate with the phonon sce as well as allow the user a simple interface to do transactions and asset management

**phonon**  For the purposes of defining interactions, a phonon is defined as a data structure containing the private key generated within the phonon sce, and the descriptor

**descriptor** A portion of the phonon describing the asset/assets associated to the private key that it shares a phonon with

**alice** A fictional phonon user generally wishing to send phonons to bob

**bob** A fictional phonon user generally wishing to receive phonons from alice

**phonon transfer packet** the encrypted data containing a number of phonons while in transit between phonon sce units


### General Constraints
all phonon interactions must adhere to the following Constraints
1. A phonon's private key may not be accessible by more than a single entity at one time
1. A phonon's private key MUST remain secret unless the phonon is destroyed at or before the time of the phonon being viewable by an outside system or entity
1. A phonon private key MUST NOT be received by a phonon sce that has not verified the sender

### Encoding
For the purposes of this document, any data being described will be in JSON format.  
Data sent between the terminal and the phonon sce will be implementation dependent.  
Phonon transfer packets must be encoded in [CBOR](https://www.rfc-editor.org/rfc/rfc8949.html) format

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
1. SET_METADATA command is run

### Phonon Listing
There must be a way to obtain a list of existing phonons for further use by the user
1. LIST_PHONONS command is run

### Phonon Transfer
A transfer of phonons from Bob to Alice
1. Bob retrieves Alice's Phonon sce certificate
2. Bob initiates SEND_PHONONS command through the client
3. The resulting transfer packet is sent to Alice's client
4. Alice's client runs the RECEIVE_PHONONS command with the phonon transfer payload against her phonon sce

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
		"certificate": "binary",
		"name": "string",
		"nonce": "binary",
		"counter", "integer or binary?",
	}
```
### Outputs (pre certificate loading)
```json
	{
		"pubkey": "binary",
	}
```
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

## ESTABLISH_SECURITY
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
## SET_METADATA
the descriptor of a phonon contains arbitrary key value pairs and is to be attached to phonons when this command is run. the identifier of the phonon is implementation dependent and may be a representation of the pubkey or an index or other identifier stored on the sce.
### Inputs
```json
	{
		{
			phonon:4
		}
		"data":{
		"arbitrary keys":"arbitrary values"
		}
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
## SEND_PHONONS
The sce is given the receiver identity certificate, validates it with the certificate authority, and generates a phonon transfer packet. It is imperative that the phonon is deleted from the sce when this packet is returned to the client. If the receiver certificate cannot be validated, the packet cannot be created. 
### Encryption
the output of this command includes encrypted private keys. 
$ENCRYPTION_SCHEME_DEFINITION (todo)
### Inputs
```json
	{
		"receiver":"[bytes of full identity certificate of receiver]",
		"receiver_counter":56383927,
		"phonons":[1,3,26]
		
	}
```
### Outputs
```json
	{
		{
			"signature":"[signature of the remaining packet signed with identity certificate]",
			"certificate":"[bytes of full identity certificate of sender]",
			"counter":56383927,
			"phonons":[
				{
					"header":{
						"privKey":"[private key bytes encrypted]"
						"curve": "secp256k1",
					},
					"metadata":{
						"abc":"xyz",
						"def":"uvw"
					}
				}
			]
		}
	}
```
## RECEIVE_PHONONS
receive phonons is run with input of a transfer packet that was output from a sending card's SEND_PHONONS command, When this command is run, the receiving card must verify the legitimacy of the sender card's certificate prior to storage of the phonon. If the verification fails, the packet must not be ingested. The phonon receive counter is then increased to the value specified in the packet.
### Counter
the counter is a security measure to prevent a single sce from receiving the same phonon transfer packet more than once. Upon initialization of the program, the counter must be set to 0, and increases to the value of the most recently received phonon upon ingestion. If a transfer packet comes in with the same or lower counter, the packet must be ignored by the sce. 
todo: add documentation on active/passive counter and a way to differentiate between the two. 
### Encryption
todo
### Inputs
```json
	{
		{
			"signature":"[signature of the remaining packet signed with identity certificate]",
			"certificate":"[bytes of full identity certificate of sender]",
			"counter":56383927,
			"phonons":[
				{
					"header":{
						"privKey":"[private key bytes encrypted]"
						"curve": "secp256k1",
					},
					"metadata":{
						"abc":"xyz",
						"def":"uvw"
					}
				}
			]
		}
	}
	```

### Outputs
```json
	[none]

```
## DESTROY_PHONON
todo
### Inputs
```json
	todo
```

### Outputs
```json
```
