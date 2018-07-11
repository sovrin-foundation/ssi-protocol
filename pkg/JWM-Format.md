# Summary
[summary]: #summary

JSON Web Messages (JWMs) will spec out how encryption, encoding, and format of messages will be followed in order to gain interoperability for a protocol.

# Motivation

JWTs have gained traction when transfering claims between parties. We are seeking to make a message encoding with similar properties, but focused on messaging, not claims or credentials. We are proposing that JWMs be derived from JWEs with stricter guidelines about key types and other to be determined (through this proposal) aspects.

# Format
[basics]: #basics
JWMs are JWEs in a compact serialization. This involves Base 64 encoded sections, separated by a dot (.) The sections are in order:

JWE JOSE Header, 
JWE Encrypted Key, (public key in anoncrypt?)
JWE initialization vector, (Nonce)
JWE Ciphertext and  (AuthCrypt ciphertext)
JWE Authentication Tag. (AuthCrypt authentication tag in Detached Mode)

We expect to add some additional constraints on the JWE spec for our use as JWMs, including valid cryptographic methods, etc.

# Example:
[example]: #example

## Flat JSON fields

*header*
This is a field which _MUST_ includes a json object that describes the optional fields as described below in section 4.2 below

*iv* 
This is a field that 

## Header JSON Fields
*alg*

For a SecretBox (AuthCrypt) message the alg, would be _DID_ which tells a person to reference the DID-Doc which _MUST_ be resolved (e.g. from the ledger, universal resolver, or local storage) to get the public key of the sender of the message.

For a SealedBox (AnonCrypt) message the alg, would be _ECDH-ES_ which tells the person to reference the *eku* field.

*jwk*

This field is used to specify which public key was used by the sender to encrypt the message. It _MUST_ be used when the DID_Doc of the receiver contains multiple messages.

*enc*

This field must contain the encryption algorithm of the scheme used to encrypt the ciphertext. It _MUST_ be a Authenticated encryption scheme which is defined within the JWM spec.

Current proposed encryption schemes are:
    1. ChaCha20Poly1305
    2. XChaCha20Poly1305
    3. Salsa20Poly1305
    4. XSalsa20Poly1305

*encrypted_key* This has become an option field to be used when sealed_box (AnonCrypt) messages have been sent. It will include the public key of the 
```json
{
	"header" : { "alg" : "DID", "enc" : "chacha20poly1305", "kid" : <reference to specific key in the Sender's DID Doc>},
	"iv" : <Nonce used in authCrypt>,
    "ciphertext" : <TODO: replace with encrypted Uint8 array message from above>,
    "tag" : <Authentication Tag from NaCl>
}

