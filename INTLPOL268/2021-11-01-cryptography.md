# Cryptography

## Definitions

* **Cryptography**: the study of secret or secure communications
* **Cryptology**: the study of math behind cryptography
* **Cryptanalysis**: code-breaking
* **Plaintext**: an unencrypted input/output of a cryptosystem
* **Cryptotext**: the encrypted output of a cryptosystem

Goals of cryptosystems:

* **Confidentiality**: keeping information private or secret
* **Integrity**: ensuring that information has not been modified
* **Authentication**: proving somebody is who they say they are
* **Non-repudiation**: proving that a message was sent by a specific actor

## Modern cryptography definitions

### Key primitives

* **Key exchange**: allows you to agree on a secret number
* **Symmetric cipher**: encrypts data using a shared key
    - Block cipher: input fixed message size (for DES, 64 bits)
    - Stream cipher: encrypts data one bit at a time
* **Asymmetric cipher**: encrypts/decrypts data with different keys
    - Diffie-Hellman (DH): generate shared key from individual public/private keys
    - Rivest-Shamir-Adleman (RSA): encrypt message using public key/private key
* **Hash algorithm**: generates a fingerprint for data
    - Function that creates a "fingerprint" of an arbitrary input that is deterministic, fixed length, and very difficult to reverse
* **Digital signature**: proves that data was sent by holder of private key

### Building blocks of modern cryptography

* **Digital signatures**: proof of identity of users/server
* **Key exchange protocol**: share secret key between users/user and server
* **Symmetric cipher**: use the secret key to encrypt/decrypt messages
* **Hashes and signatures**: make sure the message hasn't been tampered with

### Uses of encryption
* Transport encryption: create a secure tunnel (e.g., HTTPS)
    - TLS - Transport Layer Security
* Message encryption: protect messages
    - End-to-End encryption: used in iMessage, WhatsApp, Signal
* At-rest encryption: encrypt data while it's being stored
    - Keys can be stored in hardware
