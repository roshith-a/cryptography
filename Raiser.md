Responsible for ensuring cryptography is done right from design to implementation. It sets the standard for the use of cryptography at X and ensures the tools, services and features we deploy meet that standard when it comes to protecting customer data. It partners with Y and engineers to ensure teams deliver the necessary evidence their cryptographic designs meet the X crypto engineering bar.

- **Design Consultations**: schedule an informal whiteboard session for sanity checks of straightforward applications of cryptography and assistance in determining appropriate security requirements. Examples of appropriate design consultation are cookie format design or questions of the form “am I using a reasonable tool for this job?”. This is usually adequate to look at how data at rest encryption using a single layer of envelope encryption is being designed or whether a database encryption layer does what you expect.
- **Design Reviews**: similar to a principal review, but targeted at more complex use of cryptography. This is an in-depth review and we expect more complete design documentation. We can also help inform the penetration testing decision by determining how convinced we are about the correctness of a proposal. We recommend a design review for every customer-facing service or feature that uses cryptography and cannot be adjusted without customer impact.
- **Cryptographic Retrospective Reviews (CRRs)**: Similar to a typical crypto-br ticket, a CRR is focused on ensuring our designs and implementations meet our cryptographic security recommendations.  Ideally, the cryptographic review process happens during the early design of a system.  CRRs apply to already-fielded applications or services, and evaluate those services as built against current cryptographic security recommendations.  More details can be found on the CRR page
- **Published Guidance**: Crypto Bar Raisers publishes guidelines for use of particular algorithms. These will be reviewed by a larger quorum of Crypto Bar Raisers and form the requirements for cryptographic engineering at X. X-IT-Security and Crypto BR may choose to require closer examination of designs that fall outside of these guidelines.
- **Guidance regarding external reviews**: X Crypto Bar Raisers also provide guidance when something presented for its review would benefit from review outside of X and can work with X-IT-Security to coordinate appropriate external resources.

### Common guidance

#### Encryption At Rest 
Encryption at rest can be implemented “on the server side”—that is, by the service implementing the storage—or “on the client side”—that is, by the service generating and retrieving the stored data. In many cases, server-side encryption is sufficient, and is enabled by default, but in some cases, it’s unavailable—for example, on a local physical disk, or a service that does not offer it as an option—or less desirable—for highly sensitive data, for instance, which should not be exposed in plaintext to the storage service. In those cases, client-side encryption should be implemented. 
* Algorithm: top preferred algorithm, which combines encryption and integrity protection, is 256-bit AES-GCM
* Key management: The symmetric keys used to encrypt and integrity-protect at-rest data need to be stored somewhere, in a way that keeps them confidential but still available to the services that do the encryption and decryption of the data. We strongly recommend using XX for this purpose. The symmetric keys used to encrypt data should be periodically rotated, to guard against undetected key compromise, to ensure that a mechanism exists to replace the keys in the event of a detected one, and to prevent “key wear-out” (symmetric keys being used to encrypt so much data that certain brute-force cryptanalytic attacks become possible). We recommend re-encrypting data with the new key only when the data is accessed (read or written). This greatly reduces the cost of a key rotation compared to re-encrypting all data when a key is rotated, but it also requires preservation of previously used keys following a rotation, so that data not accessed since before one or more key rotations can still be decrypted.

#### Encryption In Transit

If the traffic is being transported by TCP or a similarly reliable transport protocol, then we strongly recommend that it be secured using TLS (version 1.2 or higher) if possible. If it’s being transported by UDP or a similar datagram protocol, then we recommend that it be secured using QUIC if possible, or else DTLS (version 1.2 or higher) if possible. Note that datagram protocols allow packets to be replayed, dropped or reordered, and the application will need to account for this network unreliability--use of DTLS alone will not address it. For this reason, use of TLS/TCP is recommended over QUIC/UDP wherever the former is feasible.
**Performance**: Cryptographic protocol selection and configuration can significantly affect performance metrics such as throughput and latency, particularly in specialized environments (say, high-speed or high-volume traffic and/or particular hardware characteristics). You should therefore take into account the performance impact of cryptography during your design, configuration, testing and update of cryptographic protocol and implementation choices.
**Identity/certificates**: In most settings, protocols such as TLS, DTLS and QUIC require that at least one endpoint have an identity certificate, used to authenticate the establishment of shared symmetric encryption keys
**FIPS compliance**: If your encryption-in-transit needs to be FIPS-compliant, then you should choose an approved TLS/DTLS/QUIC implementation that uses a FIPS-validated crypto library

#### Password Authentication
The traditional way to authenticate users over the internet is to use _password-over-TLS_, where a client sends plaintext credentials over a secure channel. Recommend machine-generated passwords over user-chosen ones, and minimum-length requirements over complexity requirementsAn alternate approach called _password-authenticated key exchange_ (PAKE) addresses some of the drawbacks of password-over-TLS.
PAKE: PAKE is a class of interactive protocols for establishing a shared secret based on knowledge of a password. The recommended PAKE scheme is Secure Remote Password (SRP)
The OpenSSL and BouncyCastle libraries both implement SRP. [This JavaScript library](https://github.com/LinusU/secure-remote-password) is the recommended SRP implementation in JavaScript.

#### Digital Certificates
Asymmetric public-private key pairs have multiple uses, including authenticated key exchange (in protocols such as TLS and DTLS, for instance) and digital signatures to attest to data origin and integrity (of code or documents, for instance)

**Certificate Expiration and lifetimes:** Digital certificates should have limited lifetimes, so that even if the associated private key has been compromised, the risk due to the compromise is time-limited. The appropriate lifetime for a certificate varies depending on the application, the way the private key is used, the cost of a potential compromise, compliance considerations, and many other factors. Public standards such as the CA/B forum’s guidelines for Extended Validation [SSL/TLS certificates](https://cabforum.org/extended-validation/) and [code-signing certificates](https://cabforum.org/ev-code-signing-certificate-guidelines/) can be used as guidelines for appropriate certificate lifetimes.
**Timestamping**: In some contexts, it’s useful for signatures on signed code to be time-stamped by a time-stamping service. Adding a timestamp allows verifiers to ensure that the code was in fact signed during the validity period of the signer’s certificate, so that if the signing key is compromised after the certificate’s expiration (or revocation), verifiers won’t accept forged signatures created as a result of the compromise.

#### FIPS
FIPS 140 defines four increasing qualitative levels. Level 1 applies to software-only cryptographic modules. Ref : https://github.com/aws/aws-lc
Services that need FedRAMP authorization probably need to provide FIPS-compliant endpoints or cryptographic modules that adhere to FIPS standards. FedRAMP itself does not mandate the use of FIPS-validated cryptography, it does require compliance with NIST standards like FIPS 140 as part of the security control baseline. In most cases, services seeking FedRAMP authorization need to ensure their cryptographic components meet the applicable FIPS requirements to satisfy FedRAMP security controls.
#### Randomness generation
Use randomness generation facilities in the cryptographic software library you are using. Examples of such facilities are: RAND_bytes() from LC or the SecureRandom class from ACCP
* Pitfalls: 
	* Your application must not duplicate randomness generator state or share output across execution contexts. For example, using a duplicated randomness generator state in multiple threads or processes. Typical violations occur if your application:
		- Snapshots a VM and resumes the snapshot multiple times.
		- Forks a process using cached randomness generator state or output. 
	- Don’t test the randomness generator or throw away a subset of output generated at run-time. This practice risks leaking information about the randomness generator state or the output that is being used by your application.
	- Randomness generation depends on a sufficient amount of entropy being available on the system. This assumption might be violated if you cold-boot a device and immediately starts requesting randomness. Modern entropy source interfaces automatically handles this (they simply block, in turn, blocking randomness generation).
- Random Number Generator (RNG) - general definition.
- Pseudo RNG (PRNG) - an RNG that accepts a seed and generates pseudo-random numbers by using some deterministic algorithm.
- True RNG (TRNG) - an RNG that is based on some unpredictable physical process (e.g., thermal noise).
- Cryptographic Secure RNG (CSRNG) - an RNG that is safe for cryptographic use.
- Cryptographic Secure PRNG (CSPRNG) - a PRNG that is safe for cryptographic use.
- Random Bit Generators (RBG) - same as RNG
- Non-deterministic RBG (NRBG)
- Deterministic Random Bit Generators (DRBG) - same as PRNG
Ref: 
1. [https://crypto.stackexchange.com/questions/48730/when-is-an-rng-a-csprng-a-csrng-or-a-trng/48735](https://crypto.stackexchange.com/questions/48730/when-is-an-rng-a-csprng-a-csrng-or-a-trng/48735)
2. [https://crypto.stackexchange.com/questions/48727/could-there-be-a-floating-point-csprng/48728#4872](https://crypto.stackexchange.com/questions/48727/could-there-be-a-floating-point-csprng/48728#48728)

#### SSH algos
SSH requires administrators to configure lists of cryptographic algorithms in both the client and server daemon cases. 
- A consistent bar of at least 128-bit theoretical security should be maintained for algorithms in the **Recommended** algorithm list.
- The **Compatibility** list should contain at least one choice that allows “legacy” client connectivity.
- Key Exchange:  Key exchange methods are used to securely establish a shared secret key between the SSH client and server. Using a weak key exchange method may allow an external party to learn the shared secret key or impersonate either the SSH client or SSH server.
	- ecdh-sha2-nistp256
	- ecdh-sha2-nistp384
	- sha2-nistp521
- Encryption: Encryption schemes are used to encrypt/decrypt data sent between the SSH client and SSH server
	- aes128-gcm@openssh.com
	- aes256-gcm@openssh.com
	- aes128-ctr
	- aes256-ctr
- MAC: MAC schemes are used to protect the integrity of data sent between the SSH client and SSH server. Some encryption schemes does not have build-in integrity protection of data (e.g. AES-CTR).
	- hmac-sha2-256-etm@openssh.com
	- hmac-sha2-512-etm@openssh.com
	- hmac-sha2-256
	- hmac-sha2-512
- Public Key algos: Pubic key algorithms specify which algorithms can be used for public key authentication. Both the SSH server and SSH client can authenticate using public keys.
	- ecdsa-sha2-nistp256
	- ssh-ed25519
	- ecdsa-sha2-nistp384
	- ecdsa-sha2-nistp521

#### Key usage and re-use
Cryptographic keys (secrets) should be created with a well-defined intended functionality and only be used for that purpose. In particular, no key should be provided as secret keying material in more than one cryptographic algorithm.
- Symmetric encryption keys should be restricted to a single algorithm and mode of operation. For instance, if the key is a 256-bit AES key, it should only be used for a single mode, like AES-256-GCM or AES-256-CTR, but NOT both
- For asymmetric key pairs used in elliptic curve cryptography or RSA, the private key should only be used for a digital signature algorithm or a key establishment method, NOT for both—e.g. ECDSA or ECDHE but not both, or RSA Signatures or RSA Public Key Encryption but not both.
- Key derivation functions can be used to take a designated key derivation key (or secret) and use it only to derive other keys, each for a specific cryptographic function
Reasoning: 
- A cryptographic scheme is proven/believed secure under some basic assumptions, one of which is that the secret key information is not used for any other purposes than the scheme being reasoned about. Security proofs in which secrets are used for multiple purposes are difficult, and often require additional assumptions. There are examples of such re-use leading to cryptographic weaknesses. (see, for example, this [IKE issue](https://www.usenix.org/system/files/conference/usenixsecurity18/sec18-felsch.pdf), and this [EMV Wedge Issue with other guidance on reuse](https://crypto.stanford.edu/RealWorldCrypto/slides/kenny.pdf)). A solution that re-uses a key will be brittle, and may be broken by seemingly harmless changes in the future. For example, we can make reasonable assumptions about the security of deriving a symmetric key from an RSA private key using SHA-512. But such a derivation will have a critical weakness if you substitute an Ed25519 private key in the same design.
- Many specifications prohibit the use of a key in multiple cryptographic mechanisms—for example, using a signing private key for any other cryptographic operation. The basic requirements document for FIPS 186-5, page ii, item 8 dealing with implementations: "digital signature key pairs shall not be used for other purposes." On page 11 in the same document. https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-5.pdf. 
- NIST SP 800-57, [https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-57pt1r5.pdf](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-57pt1r5.pdf), Section 5.2 (page 33): “In general, a single key shall be used for only one purpose (e.g., encryption, integrity authentication, key wrapping, random bit generation, or digital signatures).”
- The use of a private key associated with a certificate is bound by the CA’s subscriber agreement and certificate policy. Some subscriber agreements specify that using a private key in a manner not defined in the Key Usage (KU) or Extended Key Usage (EKU) in the issued certificate violates the agreement and is a cause for certificate revocation
- Good implementations of cryptography restrict the usage of a key to the purpose for which it was intended, and export it only under some other cryptographic protections. Programmatically, software should be written to protect a key from being used for a purpose other than that for which it was generated, or being exported in an unprotected manner.
- Keys used for different purposes have different key lifecycle events, are often used in different modules, and are subject to different risk factors. Using a key for multiple purposes may violate key lifecycle requirements for one or more of its uses. For example, signing keys should be destroyed immediately after expiration, but key transport keys may be retained for a longer period after expiration.