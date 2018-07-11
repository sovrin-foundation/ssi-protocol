# Summary
[summary]: #summary

JSON Web Messages (JWMs) will spec out how encryption, encoding, and format of messages will be followed in order to gain interoperability for a protocol.

# Motivation

JWTs have gained traction when transfering claims between parties. We are seeking to make a message encoding with similar properties, but focused on messaging, not claims or credentials. We are proposing that JWMs be derived from JWEs with stricter guidelines about key types and other to be determined (through this proposal) aspects. JWMs are JWEs designed for agent to agent protocol communications. They can also be expanded to other messaging formats, but until someone presents a usecase where it's necessary we'll stick with this design goal.

# Format
[basics]: #basics
This involves Base64URL encoded sections, separated by a dot (.) The sections are in order:

JWE JOSE Header, 
JWE Encrypted Key,
JWE initialization vector,
JWE Ciphertext,
JWE Authentication Tag

We expect to add some additional constraints on the JWE spec for our use as JWMs, including valid cryptographic methods, etc.

## Flat JSON fields

*header*
This is a field which _MUST_ includes a json object that describes the optional fields as described below in section 4.2 below

*iv* 
This is a field that is an nonce that is passed into the sealed_box and secret_box functions. It should be noted that this nonce should be sufficiently large enough that nonce reuse isn't likely. Nonce reusage introduces security vulnerabilities which should be avoided.

*ciphertext* 
This is the ciphertext of the message.

*tag*
This is the Authentication tag. It _SHOULD_ be a HMAC and likely will be Poly1305 to adhere to using the NaCl library.


## Header JSON Fields
*alg*

For a SecretBox (AuthCrypt) message the alg, would be _DID_ which tells a person to reference the DID-Doc which _MUST_ be resolved (e.g. from the ledger, universal resolver, or local storage) to get the public key of the sender of the message.

For a SealedBox (AnonCrypt) message the alg, would be _ECDH-ES_ which tells the person to reference the *eku* field.

*jwk*

This field is used to specify which public key was used by the sender to encrypt the message. It _MUST_ be used when the DID_Doc of the receiver contains multiple messages to identify which public key was used by the sender to encrypt the message. The format of this _SHOULD_ be the public key base58 encoded.

*enc*

This field must contain the encryption algorithm of the scheme used to encrypt the ciphertext. It _MUST_ be a Authenticated encryption scheme which is defined within the JWM spec.

Current proposed encryption schemes are:
    1. ChaCha20Poly1305
    2. XChaCha20Poly1305
    3. Salsa20Poly1305
    4. XSalsa20Poly1305

*encrypted_key* This has become an optional field to be used when sealed_box (AnonCrypt) messages have been sent. It will include the public key of the

# Encoding format

Once the JWM has been properly filled out, the JSON message should be base64url encoded with each section being separated by a period. For example, 

# Example:
[example]: #example

### SecretBox (AuthCrypt) Example 
using compact serialization:
```json
{
    "header" : { "alg" : "DID", 
                 "enc" : <AEAD algorithm name>,
                 "kid" : <reference to specific key in the Sender's DID Doc>
                },
	"iv" : <Nonce>,
    "ciphertext" : <message ciphertext>,
    "tag" : <Authentication Tag from NaCl>
}
```

### Encodes to:
Base64URLEncode(header).
Base64URLEncode(iv).
Base64URLEncode(ciphertext).
Base64URLEncode(tag)

### SealedBox (AnonCrypt) Example 
using JWE compact serialization:
```json
{
    "header" : { "alg" : "ECDH-ES", 
                 "enc" : <AEAD algorithm name>, 
                 "kid" : <reference to specific key in the Sender's DID Doc>,
                 "jwk" : <Sender's verkey Base58 encoded>
                },
	"iv" : <Nonce used in authCrypt>,
    "ciphertext" : <message ciphertext>,
    "tag" : <Authentication Tag from NaCl>
}
```

### Encodes to:
Base64URLEncode(header).
Base64URLEncode(iv).
Base64URLEncode(ciphertext).
Base64URLEncode(tag)