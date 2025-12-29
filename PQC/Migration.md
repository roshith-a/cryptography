Tactical: 
- TLS Endpoints with PQC cipher suits (ML-KEM using core libs)
	- Also SSH , IPSec (n/w layer)  and MAC sec (physical layer) 
- Digital Signature ML-DSA for private PKI and code signing
Ref:  https://aws.amazon.com/blogs/security/aws-post-quantum-cryptography-migration-plan/

- Strategic: PQC TLS certificates. Authentication function doesn't require long lived protections compared to confidentiality function. CAB and ANSI yet to finalise interoperable quantum certificates. 
- Plan: Start a campaign, choose pilot services, automate upgrade where possible, run canaries, load test for performance, monitoring tools
##### Conundrum: Plan for new algos. 
 - Use case that require quantum safe root of trust have to choose Stateful  (LMS, XMSS) or wait for Stateless Hash Based signatures. Eg for firmware upgrade verification in chips ROMs. Stateful need to manage state associated with private key; big overhead in large distributed systems. 
 - Stateless: SPHINCS. Vs Dilithium Vs Falcon

Hybrid Key Exchange: 

Latency:  
Ref: https://www.amazon.science/blog/delays-from-post-quantum-cryptography-may-not-be-so-bad

#### Risks
- PQC protocols standardisation not finalised and can change - Eg: PQC TLS
- Large PQC key and cipher text can packet fragmentation handling issue in legacy n/w devices
### Standards
- [Hybrid key exchange in TLS 1.3](https://datatracker.ietf.org/doc/html/draft-ietf-tls-hybrid-design), by Douglas Stebila, Scott Fluhrer, and Shay Gueron. IETF Internet-Draft, 2020.
- [Hybrid Post-Quantum Key Encapsulation Methods (PQ KEM) for Transport Layer Security 1.2 (TLS)](https://tools.ietf.org/html/draft-campagna-tls-bike-sike-hybrid-06)
- [Post-quantum public key algorithms for the Secure Shell (SSH) protocol](https://tools.ietf.org/html/draft-kampanakis-curdle-pq-ssh-00)

### Research Papers
- [A lean BIKE KEM design for ephemeral key agreement](https://www.amazon.science/publications/a-lean-bike-kem-design-for-ephemeral-key-agreement) by Dusan Kostic, Shay Gueron, Nir Drucker. NIST PQC Standardization conference 2024.
- [Speeding up post-quantum TLS handshakes by suppressing intermediate CA certificates](https://www.amazon.science/publications/speeding-up-post-quantum-tls-handshakes-by-suppressing-intermediate-ca-certificates)  by Panos Kampanakis, Michael Kallitsis. ICMC 2021, August 2021.
- [Prototyping post-quantum and hybrid key exchange and authentication in TLS and SSH](https://s3.amazonaws.com/files.douglas.stebila.ca/files/research/papers/NISTPQC-CroPaqSte19.pdf), by Eric Crockett, Christian Paquin, and Douglas Stebila. NIST 2nd Post-Quantum Cryptography Standardization Conference, August, 2019.
- [Security of Hybrid Key Encapsulation](https://eprint.iacr.org/2020/1364), by Matthew Campagna and Adam Petcher. Cryptology ePrint Archive: Report 2020/1364.
- [On constant-time QC-MDPC decoding with negligible failure rate](https://eprint.iacr.org/2019/1289), by Shay Gueron, Dusan Kostic, and Nir Drucker, Code-Based Cryptography Workshop, May 2020.
- [QC-MDPC decoders with several shades of gray](https://eprint.iacr.org/2019/1423), by Shay Gueron, Dusan Kostic, and Nir Drucker, International Conference on Post-Quantum Cryptography, April 2020.
- [Fast polynomial inversion for post quantum QC-MDPC cryptography](https://eprint.iacr.org/2020/298), by Shay Gueron, Dusan Kostic, and Nir Drucker, International Symposium on Cyber Security Cryptography and Machine Learning, July 2020.
- [On the applicability of the Fujisaki-Okamoto transformation to the BIKE KEM](https://eprint.iacr.org/2020/510), by Shay Gueron, Dusan Kostic, Nir Drucker, and Edoardo Persichetti, International Journal of Computer Mathematics: Computer Systems Theory, October 2021
- NIST Round 3 report NISTIR 8413, July 2022. https://csrc.nist.gov/publications/detail/nistir/8413/final
- Stebila, D., et al., “Hybrid key exchange in TLS 1.3,” IETF, January 2022 : https://datatracker.ietf.org/doc/html/draft-ietf-tls-hybrid-design-04
- Campagna, M., Crockett, E., “Hybrid Post-Quantum Key Encapsulation Methods (PQ KEM) for Transport Layer Security 1.2 (TLS),” IETF, September 2021, https://datatracker.ietf.org/doc/html/draft-campagna-tls-bike-sike-hybrid-07
- Kampanakis, P., et al., “Post-quantum public key algorithms for the Secure Shell (SSH) protocol,” IETF, October 2020: https://datatracker.ietf.org/doc/html/draft-kampanakis-curdle-pq-ssh-00
- ] Fluhrer, S., et al., “Mixing Preshared Keys in the Internet Key Exchange Protocol Version 2,” IETF, June 2020: https://datatracker.ietf.org/doc/html/rfc8784.
- Turner, S., “Algorithm Identifiers for NIST's PQC Algorithms for Use in the Internet X.509 Public Key Infrastructure IETF, March 2022, https://datatracker.ietf.org/doc/html/draft-turner-lamps-nist-pqc-kem-certificates-01
- “CYBER; Quantum-safe Key Exchanges,” ETSI TS 103 744 V1.1.1 (2020-12), ETSI TC CYBER QSC: https://www.etsi.org/deliver/etsi_ts/103700_103799/103744/01.01.01_60/ts_103744v010101p.pdf.
- Recommendations for Stateful Hash-Based Signature Schemes,” NIST SP 800-208, October 2020: https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-208.pdf.
- “Post-quantum Hybrid Key Exchange in SSH”, github April 2022: https://github.com/csosto-pk/pq-ssh/blob/master/draft-kampanakis-ssh-pq-ke.txt
- Post-Quantum Cryptography FAQ, NIST January 2017, https://csrc.nist.gov/Projects/Post-Quantum-Cryptography/faqs
- Suppressing CA Certificates in TLS 1.3, IETF March 2022, https://datatracker.ietf.org/doc/html/draft-kampanakis-tls-scas-latest
- Use of Post-Quantum KEM in the Cryptographic Message Syntax (CMS), IETF March 2022: https://datatracker.ietf.org/doc/html/draft-perret-prat-lamps-cms-pq-kem
- Introducing s2n-quic, a new open-source QUIC protocol implementation in Rust, Febuary 2022: https://aws.amazon.com/blogs/security/introducing-s2n-quic-open-source-protocol-rust/
- Composite Signatures For Use In Internet PKI, IETF, February 2022, https://datatracker.ietf.org/doc/html/draft-ounsworth-pq-composite-sigs
- Non-Composite Hybrid Authentication in PKIX and Applications to Internet Protocols, IETF, March 2022: https://datatracker.ietf.org/doc/draft-becker-guthrie-noncomposite-hybrid-auth/
- Algorithms and Identifiers for Post-Quantum Algorithms in the Internet X.509 Public Key Infrastructure, github April 2022, https://github.com/jakemas/draft-massimo-pq-pkix-00
- NIST Nation Cybersecurity Center of Excellence Migration to Post-Quantum Cryptography, NIST: https://www.nccoe.nist.gov/crypto-agility-considerations-migrating-post-quantum-cryptographic-algorithms
- AWS s2n-tls, https://github.com/aws/s2n-tls
- AWS Labs aws-lc, https://github.com/awslabs/aws-lc
- NIST Recommendation for Stateful Hash-Based Signature Schemes, Special Publication (NIST SP) - 800-208, NIST: https://www.nist.gov/publications/recommendation-stateful-hash-based-signature-schemes
- Gilles Brassard, Peter Høyer, Alain Tapp, Quantum cryptanalysis of hash and clawfree functions
- Cost analysis of hash collisions: Will quantum computers make SHARCS obsolete? Daniel J. Bernstein: https://cr.yp.to/hash/collisioncost-20090517.pdf
- Panos Kampanakis and Scott Fluhrer, LMS vs XMSS: Comparion of two Hash-Based Signature Standards, 2017: https://eprint.iacr.org/2017/349
- Campos, F., Kohlstadt, T., Reith, S., Stöttinger, M. (2020). LMS vs XMSS: Comparison of Stateful Hash-Based Signature Schemes on ARM Cortex-M4. In: Nitaj, A., Youssef, A. (eds) Progress in Cryptology - AFRICACRYPT 2020. AFRICACRYPT 2020 Lecture Notes in Computer Science, vol 12174. Springer, Cham. https://doi.org/10.1007/978-3-030-51938-4_13
- Joppe W. Bos, Joost Renes, and Daan Sprenkels. Dilithium for Memory Constrained Devices: https://eprint.iacr.org/2022/323.pdf
- Kannwischer, M.J., Rijneveld, J., Schwabe, P., Stoffelen. K.: PQM4: Post-quantum crypto library for the ARM Cortex-M4: https://github.com/mupq/pqm4
- Panos Kampanakis, Peter Panburana, Michael Curcio, Chirag Shroff, and Md Mahbub Alam. Post-Quantum LMS and SPHINCS+ Hash-Based Signatures for UEFI Secure Boot. https://eprint.iacr.org/2021/041
- Fabio Campos, Tim Kohlstadt, Steffen Reith, and Marc Stoettinger. LMS vs XMSS: Comparison of Stateful Hash-Based Signature Schemes on ARM Cortex-M4. https://eprint.iacr.org/2020/470.pdf
- Whitehouse NSM : https://www.whitehouse.gov/briefing-room/statements-releases/2022/05/04/national-security-memorandum-on-promoting-united-states-leadership-in-quantum-computing-while-mitigating-risks-to-vulnerable-cryptographic-systems/
- National Security Agency – Cybersecurity Advisory, Commercial National Security Algorithms (CNSA) Suite 2.0: https://media.defense.gov/2022/Sep/07/2003071834/-1/-1/0/CSA_CNSA_2.0_ALGORITHMS_.PDF
- Kampanakis, P., Volanis, A., Ravago, G., Hansen, T., “Post-quantum hybrid SFTP file transfers using AWS Transfer Family,” https://aws.amazon.com/blogs/security/post-quantum-hybrid-sftp-file-transfers-using-aws-transfer-family/
- NSA, Quantum Key Distribution (QKD) and Quantum Cryptography (QC) : https://www.nsa.gov/Cybersecurity/Quantum-Key-Distribution-QKD-and-Quantum-Cryptography-QC/
- NCSC, Quantum security technologies https://www.ncsc.gov.uk/whitepaper/quantum-security-technologies
- Yuen, H.P., “Security of Quantum Key Distribution,” February 2016, https://arxiv.org/pdf/1602.07602.pdf
- Lo, H.K, Curty, M., Tamaki, K., “Secure quantum key distribution,” July 2014: https://www.nature.com/articles/nphoton.2014.149
- Sun, S., Huang, A., “A Review of Security Evaluations of Practical Quantum Key Distribution Systems,” February 2022: https://www.mdpi.com/1099-4300/24/2/260
- Position Paper on Quantum Key Distribution,“ January 2024: https://www.bsi.bund.de/SharedDocs/Downloads/EN/BSI/Crypto/Quantum_Positionspapier.pdf
- FIPS 203, Module-Lattice-Based Key Encapsulation Mechanism Standard,” National Institute of Standards and Technology, August 2024, https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.203.pdf
- FIPS 204, Module-Lattice-Based Digital Signature Algorithm Standard,” National Institute of Standards and Technology, August 2024, https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.204.pdf
- FIPS 205, Stateless Hash-Based Digital Signature Algorithm Standard,” National Institute of Standards and Technology August 2024, https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.205.pdf