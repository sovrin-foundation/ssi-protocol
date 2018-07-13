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

*recipient*
A list of corresponding people that can decrypt the message and the key's they should be using in order to perform decryption of the message. This is _SHOULD_ be used to support efficient encryption of a message to multiple parties.


## Header JSON Fields
*alg*

This _SHOULD_ include the value *nacl_box* indicating that the nacl's implementation of crypto_box was used to encrypt the symmetrical key contained in the encrypted_key field.

*enc*

This field must contain the encryption algorithm of the scheme used to encrypt the ciphertext. It _MUST_ be a Authenticated encryption scheme which is defined within the JWM spec.

Current proposed encryption schemes are:
    1. ChaCha20Poly1305
    2. XChaCha20Poly1305
    3. Salsa20Poly1305
    4. XSalsa20Poly1305

*kid*

This is a reference to the specific public key that was taken from the did-doc by the sender to encrypt the symmetrical key used to encrypt the ciphertext.

*jwk*

This field is used to specify which public key was used by the sender to encrypt the message. It _MUST_ be used when the DID_Doc of the receiver contains multiple messages to identify which public key was used by the sender to encrypt the message. It also _MUST_ be used when an ephemeral key is used to send the message, such as in SealedBox (AnonCrypt). The format of this _SHOULD_ be the public key base58 encoded.

*encrypted_key*

This is the key which has been encrypted using NaCl's crypto_box to encrypt the symmetrical key used to encrypt the ciphertext.

# Encoding format

Once the JWM has been properly filled out, the JSON message should be base64url encoded with each section being separated by a period. Examples of the encoding are provided below.

# Example:
[example]: #example

### Compact Serialization Example 
```json
{
    "header" : { "alg" : "NaCl_box", 
                 "enc" : <AEAD algorithm name>,
                 "kid" : <reference to the receiver public key as a DID>,
                 "jwk" : <reference to the sender's public key>,
                 "encrypted_key" : <output of a cryptobox function which is the secret key used to encrypt ciphertext>
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

### JSON Serialization Example
```json
{
    "recipient" : [
        "header" : { "alg" : "NaCl_box", 
                 "enc" : <AEAD algorithm name>,
                 "kid" : <reference to the receiver public key as a DID>,
                 "jwk" : <reference to the sender's public key>,
                 "encrypted_key" : <output of a cryptobox function which is the secret key used to encrypt ciphertext>
                },
        "header" : { "alg" : "NaCl_box", 
                 "enc" : <AEAD algorithm name>,
                 "kid" : <reference to the receiver public key as a DID>,
                 "jwk" : <reference to the sender's public key>,
                 "encrypted_key" : <output of a cryptobox function which is the secret key used to encrypt ciphertext>
                },
    ],
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