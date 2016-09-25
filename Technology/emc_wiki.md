# EMC Wiki
<!-- toc -->
## Openssl Ciphers Research
### Basic Knowledge

**Public-key cryptography**

Public-key cryptography also known as asymmetric cryptography. It is computationally easy for a user to generate a public and private key-pair and to use it for encryption and decryption. The strength lies in the "impossibility" (computational impracticality) for a properly generated private key to be determined from its corresponding public key. Thus the public key may be published without compromising security. Security depends only on keeping the private key private. Public key algorithms, unlike symmetric key algorithms, do not require a secure channel for the initial exchange of one (or more) secret keys between the parties.

In an asymmetric key encryption scheme, anyone can encrypt messages using the public key, but only the holder of the paired private key can decrypt. Security depends on the secrecy of the private key.
In the Diffie–Hellman key exchange scheme, each party generates a public/private key pair and distributes the public key. After obtaining an authentic copy of each other's public keys, Alice and Bob can compute a shared secret offline. The shared secret can be used, for instance, as the key for a symmetric cipher.

Public-key algorithms are fundamental security ingredients in cryptosystems, applications and protocols. They underpin various Internet standards, such as Transport Layer Security (TLS), S/MIME, PGP, and GPG. Some public key algorithms provide key distribution and secrecy (e.g., Diffie–Hellman key exchange), some provide digital signatures (e.g., Digital Signature Algorithm), and some provide both (e.g., RSA).
The distinguishing technique used in public-key cryptography is the use of asymmetric key algorithms, where a key used by one party to perform either encryption or decryption is not the same as the key used by another in the counterpart operation. Each user has a pair of cryptographic keys – a public encryption key and a private decryption key. For example, a key pair used for digital signatures consists of a private signing key and a public verification key. The public key may be widely distributed, while the private key is known only to its proprietor. The keys are related mathematically, but the parameters are chosen so that calculating the private key from the public key is unfeasible.
In contrast, symmetric-key algorithms – variations of which have been used for thousands of years – use a single secret key, which must be shared and kept private by both the sender and the receiver, for example in both encryption and decryption. To use a symmetric encryption scheme, the sender and receiver must securely share a key in advance.

Because symmetric key algorithms are nearly always much less computationally intensive than asymmetric ones, it is common to exchange a key using a key-exchange algorithm, then transmit data using that key and a symmetric key algorithm. PGP(Pretty Good Privacy) and the SSL/TLS family of schemes use this procedure, and are thus called hybrid cryptosystems.
Asymmetric key techniques for varied purposes include: RSA, DH, DSS, Various elliptic curve techniques, Various password-authenticated key agreement techniques, ... 

Protocols using asymmetric key algorithms include: SSL/TLS, SSH, S/MIME, ...

**Symmetric-key algorithms**

Symmetric-key algorithms are algorithms for cryptography that use the same cryptographic keys for both encryption of plaintext and decryption of ciphertext. The keys may be identical or there may be a simple transformation to go between the two keys. The keys, in practice, represent a shared secret between two or more parties that can be used to maintain a private information link. This requirement that both parties have access to the secret key is one of the main drawbacks of symmetric key encryption, in comparison to public-key encryption.
Examples of popular symmetric algorithms include Twofish, Serpent, AES (Rijndael), Blowfish, CAST5, RC4, 3DES, Skipjack, Safer+/++ (Bluetooth), and IDEA

**Key exchange**

Key exchange (also known as "key establishment") is any method in cryptography by which cryptographic keys are exchanged between two parties, allowing use of a cryptographic algorithm.
If sender and receiver wish to exchange encrypted messages, each must be equipped to encrypt messages to be sent and decrypt messages received. The nature of the equipping they require depends on the encryption technique they might use.

If they use a code, both will require a copy of the same codebook. 
If they use a cipher, they will need appropriate keys. 
If the cipher is a symmetric key cipher, both will need a copy of the same key. 
If an asymmetric key cipher with the public/private key property, both will need the other's public key.

**Digital Signature**

Message authentication involves hashing the message to produce a "digest," and encrypting the digest with the private key to produce a digital signature. Thereafter anyone can verify this signature by (1) computing the hash of the message, (2) decrypting the signature with the signer's public key, and (3) comparing the computed digest with the decrypted digest. Equality between the digests confirms the message is unmodified since it was signed, and that the signer, and no one else, intentionally performed the signature operation — presuming the signer's private key has remained secret. 
The security of such procedure depends on a hash algorithm of such quality that it is computationally impossible to alter or find a substitute message that produces the same digest - but studies have shown that even with the MD5 and SHA-1 algorithms, producing an altered or substitute message is not impossible. The current hashing standard for encryption is SHA-2. The message itself can also be used in place of the digest.

**Public key certificate**

In cryptography, a public key certificate (also known as a digital certificate or identity certificate) is an electronic document used to prove ownership of a public key. The certificate includes information about the key, information about its owner's identity, and the digital signature of an entity that has verified the certificate's contents are correct. If the signature is valid, and the person examining the certificate trusts the signer, then they know they can use that key to communicate with its owner.

**Public key infrastructure(PKI)**

In cryptography, a PKI is an arrangement that binds public keys with respective user identities by means of a certificate authority (CA). The user identity must be unique within each CA domain. The third-party validation authority (VA) can provide this information on behalf of the CA. The binding is established through the registration and issuance process. Depending on the assurance level of the binding, this may be carried out by software at a CA or under human supervision. The PKI role that assures this binding is called the registration authority (RA). The RA is responsible for accepting requests for digital certificates and authenticating the person or organization making the request. In a Microsoft PKI, a registration authority is usually called a subordinate CA.

**Certificate Authority**

In cryptography, a CA is an entity that issues digital certificates. A digital certificate certifies the ownership of a public key by the named subject of the certificate. This allows others (relying parties) to rely upon signatures or on assertions made by the private key that corresponds to the certified public key. In this model of trust relationships, a CA is a trusted third party – trusted both by the subject (owner) of the certificate and by the party relying upon the certificate. Many public-key infrastructure (PKI) schemes feature CAs.

**X.509** 

In cryptography, X.509 is an ITU-T standard for a public key infrastructure (PKI) and Privilege Management Infrastructure (PMI). X.509 specifies, amongst other things, standard formats for public key certificates, certificate revocation lists, attribute certificates, and a certification path validation algorithm.
In the X.509 system, a certification authority issues a certificate binding a public key to a particular Distinguished Name#Directory structure|distinguished name in the X.500 tradition, or to an alternative name such as an e-mail address or a Domain Name System|DNS entry.
An organization's trusted root certificates can be distributed to all employees so that they can use the company PKI system. Browsers such as Internet Explorer, Firefox, Opera (web browser)|Opera, Safari (web browser)|Safari and Google Chrome|Chrome come with a predetermined set of root certificates pre-installed, so Secure Sockets Layer|SSL certificates from larger vendors will work instantly; in effect the browsers' developers determine which CAs are trusted third parties for the browsers' users.
X.509 also includes standards for certificate revocation list (CRL) implementations, an often neglected aspect of PKI systems. The IETF-approved way of checking a certificate's validity is the Online Certificate Status Protocol (OCSP). Firefox 3 enables OCSP checking by default along with versions of Windows including Vista and later.

### Openssl Functions

**Synopsis**

```
openssl command [ command_opts ] [ command_args ]
openssl [ list-standard-commands | list-message-digest-commands | list-cipher-commands | list-cipher-algorithms | list-message-digest-algorithms | list-public-key-algorithms]
openssl no-XXX [ arbitrary options ] # If no command named XXX exists, it returns 0 (success) and prints no-XXX ; otherwise it returns 1 and prints XXX .
```
**Description**

OpenSSL is a cryptography toolkit implementing the Secure Sockets Layer ( SSL v2/v3) and Transport Layer Security ( TLS v1) network protocols and related cryptography standards required by them.
The openssl program is a command line tool for using the various cryptography functions of OpenSSL's crypto library from the shell. It can be used for  
```
Creation and management of private keys, public keys and parameters
Public key cryptographic operations
Creation of X.509 certificates, CSRs and CRLs
Calculation of Message Digests
Encryption and Decryption with Ciphers
SSL/TLS Client and Server Tests
Handling of S/MIME signed or encrypted mail
Time Stamp requests, generation and verification
```
**Commands**
```
[leo@centOS ~]$ openssl help
openssl:Error: 'help' is an invalid command.

Standard commands
asn1parse         ca                ciphers           cms
crl              crl2pkcs7         dgst              dh
dhparam           dsa               dsaparam          ec
ecparam           enc               engine            errstr
gendh             gendsa            genpkey           genrsa
nseq              ocsp              passwd            pkcs12
pkcs7             pkcs8             pkey              pkeyparam
pkeyutl           prime             rand              req
rsa               rsautl            s_client          s_server
s_time            sess_id           smime             speed
spkac             ts                verify            version
x509

Message Digest commands (see the `dgst' command for more details)
md2               md4               md5               rmd160
sha               sha1

Cipher commands (see the `enc' command for more details)
aes-128-cbc       aes-128-ecb       aes-192-cbc       aes-192-ecb
aes-256-cbc       aes-256-ecb       base64            bf
bf-cbc            bf-cfb            bf-ecb            bf-ofb
camellia-128-cbc  camellia-128-ecb  camellia-192-cbc  camellia-192-ecb
camellia-256-cbc  camellia-256-ecb  cast              cast-cbc
cast5-cbc         cast5-cfb         cast5-ecb         cast5-ofb
des               des-cbc           des-cfb           des-ecb
des-ede           des-ede-cbc       des-ede-cfb       des-ede-ofb
des-ede3          des-ede3-cbc      des-ede3-cfb      des-ede3-ofb
des-ofb           des3              desx              idea
idea-cbc          idea-cfb          idea-ecb          idea-ofb
rc2               rc2-40-cbc        rc2-64-cbc        rc2-cbc
rc2-cfb           rc2-ecb           rc2-ofb           rc4
rc4-40            seed              seed-cbc          seed-cfb
seed-ecb          seed-ofb          zlib
```

**Common Standard Commands**

rsa RSA key management.
genrsa Generation of RSA Private Key. Superceded by genpkey.
genpkey Generation of Private Key or Parameters.

RSA is a public-key cryptosystems and is widely used for secure data transmission. 

## openssl genpkey 
```
openssl genrsa -des3 -out private.pem 2048 # generates a key pair, encrypts them with a password you provide
Generating RSA private key, 2048 bit long modulus

Enter pass phrase for private.pem:
Verifying - Enter pass phrase for private.pem:
openssl rsa -in private.pem -pubout -out public.pem
Enter pass phrase for private.pem:
writing RSA key

cat public.pem

-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAnx6whBdBG+u2lu18G8RJ
5RLliY6AyfGNQzBQ/fOTE/QjpVyOkQSe17BYYKYs+tKomti0VdlDgF2qnq/ECknv
OQg/CEcgG0jGADW9AxmoatrwIeupOZpV3oFiPIstPGwgTidmIPkiqiCdw4ciONjA
4kaQE91xl9lVDZrK7n5HUkRLzJAmxuMIUI2Q7CCmjfOoKzmlcwNzIldL4yH0LvmS
J4C7ujBBy1vkMtlvtFEPDmfilS0VWbdgk60PV4pnmqDjNvkBZGL/VA1qBcrBnX5L
IPoO3bFfpxGyJwS3NR4TUcWWDs8YNU4uzuAZ8tORAwLMZpDIPLtbg0nuVnNh0u/a
zQIDAQAB
-----END PUBLIC KEY-----

ciphers Cipher Suite Description Determination.
dgst Message Digest Calculation.
dsa DSA Data Management.
ec EC (Elliptic curve) key processing
ecparam EC parameter manipulation and generation
enc Encoding with Ciphers.

# cat a.txt

This a string for testing openssl.
openssl enc -aes128 -a -k 123 -in test.txt -out a.aes128.base64

cat test.aes128.base64

U2FsdGVkX18IIR5XPWHaxsZUlC+ChR2AWQomhqignzWlnEuo66Pi2WT/PvQ9TsDF
w84yYKodtABcUlsparDpLQ==

openssl enc -aes128 -a -d -k 123 -in a.aes128.base64

This a string for testing openssl.
passwd Generation of hashed passwords.
# openssl passwd password
w/jSAugKD.UAg

openssl passwd -crypt -salt xx password # Use the crypt algorithm (default) and specified salt
xxj31ZMTZzkVA.

openssl passwd -apr1 -salt xxxxxxxx password #  Use the apr1 algorithm and specified salt
$apr1$xxxxxxxx$dxHfLAsjHkDRmG83UXe8K0.

pkey Public and private key management.

pkeyparam Public key algorithm parameter management.

rand Generate pseudo-random bytes.

# openssl rand -hex 15 # show the 15 random bytes as a hex string 

65f4481c016cc65986af04e7722517


Message Digest

A message digest is a cryptographic hash function containing a string of digits created by a one-way hashing formula. 
Message digests are designed to protect the integrity of a piece of data or media to detect changes and alterations to any part of a message. They are a type of cryptography utilizing hash values that can warn the copyright owner of any modifications applied to their work.

# openssl list-message-digest-commands
md4
md5
sha
...

# openssl list-message-digest-algorithms | cut -d " " -f 3 | sort | uniq
DSA
DSA-SHA
...
SHA384
SHA512
```

**Key Words**

MD(Message Digest)

PEM DER

PEM, DER, all of them are format of certification.
DER (Distinguished Encoding Rules) is one of ASN.1 encoding rules defined in ITU-T X.690, 2002, specification. ASN.1 encoding rules can be used to encode any data object into a binary file.
The basic encoding rule of DER is that a data value of all data types shall be encoded as four components in the following order:Identifier octets,Length octets,Contents octets,End-of-contents octets
PEM or Privacy Enhanced Mail is a Base64 encoded DER certificate. PEM certificates are frequently used for web servers as they can easily be translated into readable data using a simple text editor. Generally when a PEM encoded file is opened in a text editor, it contains very distinct headers and footers. 


Above is the example of a CSR (certificate signing request) in PEM format. You can see that PEM has the characteristics of containing a header, the body (which consists mainly of code) and footer.
