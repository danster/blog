#WIKI
https://cig-wiki.isus.emc.com/mediawiki/index.php/LeoHuang%27s_Wiki#CDP

- [Openssl Ciphers Research](#Openssl)
- [CDP ssl weak ciphers](#CDP)
- [Docker Homepage](#Docker)

##<a id=Openssl>Openssl Ciphers Research</a> 
<div id="bodyContent">		
<table id="toc" class="toc" summary="Contents"><tbody><tr><td><div id="toctitle"><h2>Contents</h2> 
</div>
<ul>
<li class="toclevel-1"><a href="#Basic_Knowledge"><span class="tocnumber">1</span> <span class="toctext">Basic Knowledge</span></a>
<ul>
<li class="toclevel-2"><a href="#Public-key_cryptography"><span class="tocnumber">1.1</span> <span class="toctext">Public-key cryptography</span></a></li>
<li class="toclevel-2"><a href="#Symmetric-key_algorithms"><span class="tocnumber">1.2</span> <span class="toctext">Symmetric-key algorithms</span></a></li>
<li class="toclevel-2"><a href="#Key_exchange"><span class="tocnumber">1.3</span> <span class="toctext">Key exchange</span></a></li>
<li class="toclevel-2"><a href="#Digital_Signature"><span class="tocnumber">1.4</span> <span class="toctext">Digital Signature</span></a></li>
<li class="toclevel-2"><a href="#Public_key_certificate"><span class="tocnumber">1.5</span> <span class="toctext">Public key certificate</span></a></li>
<li class="toclevel-2"><a href="#Public_key_infrastructure.28PKI.29"><span class="tocnumber">1.6</span> <span class="toctext">Public key infrastructure(PKI)</span></a></li>
<li class="toclevel-2"><a href="#Certificate_Authority"><span class="tocnumber">1.7</span> <span class="toctext">Certificate Authority</span></a></li>
<li class="toclevel-2"><a href="#X.509"><span class="tocnumber">1.8</span> <span class="toctext">X.509</span></a></li>
</ul>
</li>
<li class="toclevel-1"><a href="#Openssl_Functions"><span class="tocnumber">2</span> <span class="toctext">Openssl Functions</span></a>
<ul>
<li class="toclevel-2"><a href="#Synopsis"><span class="tocnumber">2.1</span> <span class="toctext">Synopsis</span></a></li>
<li class="toclevel-2"><a href="#Description"><span class="tocnumber">2.2</span> <span class="toctext">Description</span></a></li>
<li class="toclevel-2"><a href="#Commands"><span class="tocnumber">2.3</span> <span class="toctext">Commands</span></a></li>
<li class="toclevel-2"><a href="#Common_Standard_Commands"><span class="tocnumber">2.4</span> <span class="toctext">Common Standard Commands</span></a></li>
</ul>
</li>
<li class="toclevel-1"><a href="#Message_Digest"><span class="tocnumber">3</span> <span class="toctext">Message Digest</span></a></li>
<li class="toclevel-1"><a href="#Key_Words"><span class="tocnumber">4</span> <span class="toctext">Key Words</span></a>
<ul>
<li class="toclevel-2"><a href="#MD.28Message_Digest.29"><span class="tocnumber">4.1</span> <span class="toctext">MD(Message Digest)</span></a></li>
<li class="toclevel-2"><a href="#PEM_DER"><span class="tocnumber">4.2</span> <span class="toctext">PEM DER</span></a></li>
</ul>
</li>
</ul>
</td></tr></tbody></table><script type="text/javascript"> if (window.showTocToggle) { var tocShowText = "show"; var tocHideText = "hide"; showTocToggle(); } </script>
<a name="Basic_Knowledge" id="Basic_Knowledge"></a><h2> <span class="mw-headline"> Basic Knowledge </span></h2>
<a name="Public-key_cryptography" id="Public-key_cryptography"></a><h3> <span class="mw-headline"> <a href="https://en.wikipedia.org/wiki/Public-key_cryptography" class="external text" title="https://en.wikipedia.org/wiki/Public-key_cryptography" rel="nofollow">Public-key cryptography</a> </span></h3>
<p>Public-key cryptography also known as asymmetric cryptography.  It is computationally easy for a user to generate a public and private key-pair and to use it for encryption and decryption. The strength lies in the "impossibility" (computational impracticality) for a properly generated private key to be determined from its corresponding public key. Thus the public key may be published without compromising security. Security depends only on keeping the private key private. Public key algorithms, unlike symmetric key algorithms, do not require a secure channel for the initial exchange of one (or more) secret keys between the parties.
</p>
<div class="thumb tleft"><div class="thumbinner" style="width:252px;"><a href="/mediawiki/index.php/File:Public_key_encryption.svg.png" class="image" title="In an asymmetric key encryption scheme, anyone can encrypt messages using the public key, but only the holder of the paired private key can decrypt. Security depends on the secrecy of the private key."></a>  <div class="thumbcaption">In an asymmetric key encryption scheme, anyone can encrypt messages using the public key, but only the holder of the paired private key can decrypt. Security depends on the secrecy of the private key.</div></div></div> <div class="center"><div class="thumb tnone"><div class="thumbinner" style="width:252px;"><a href="/mediawiki/index.php/File:Public_key_shared_secret.svg.png" class="image" title="In the Diffie–Hellman key exchange scheme, each party generates a public/private key pair and distributes the public key. After obtaining an authentic copy of each other's public keys, Alice and Bob can compute a shared secret offline. The shared secret can be used, for instance, as the key for a symmetric cipher."></a>  <div class="thumbcaption">In the Diffie–Hellman key exchange scheme, each party generates a public/private key pair and distributes the public key. After obtaining an authentic copy of each other's public keys, Alice and Bob can compute a shared secret offline. The shared secret can be used, for instance, as the key for a symmetric cipher.</div></div></div></div>
<p>Public-key algorithms are fundamental security ingredients in cryptosystems, applications and protocols. They underpin various Internet standards, such as Transport Layer Security (TLS), S/MIME, PGP, and GPG. Some public key algorithms provide key distribution and secrecy (e.g., Diffie–Hellman key exchange), some provide digital signatures (e.g., Digital Signature Algorithm), and some provide both (e.g., RSA).
</p><p>The distinguishing technique used in public-key cryptography is the use of asymmetric key algorithms, where a key used by one party to perform either encryption or decryption is not the same as the key used by another in the counterpart operation. Each user has a pair of <a href="https://en.wikipedia.org/wiki/Key_(cryptography)" class="external text" title="https://en.wikipedia.org/wiki/Key_(cryptography)" rel="nofollow">cryptographic keys</a> – a <b>public encryption key</b> and a <b>private decryption key</b>. For example, a key pair used for <a href="https://en.wikipedia.org/wiki/Digital_signature" class="external text" title="https://en.wikipedia.org/wiki/Digital_signature" rel="nofollow">digital signatures</a> consists of a <b>private signing key</b> and a <b>public verification key</b>. The public key may be widely distributed, while the private key is known only to its proprietor. The keys are related mathematically, but the parameters are chosen so that calculating the private key from the public key is unfeasible.
</p><p>In contrast, <a href="https://en.wikipedia.org/wiki/Symmetric-key_algorithm" class="external text" title="https://en.wikipedia.org/wiki/Symmetric-key_algorithm" rel="nofollow">symmetric-key algorithms</a> – variations of which have been used for thousands of years – use a <i>single</i> secret key, which must be shared and kept private by both the sender and the receiver, for example in both encryption and decryption. To use a symmetric encryption scheme, the sender and receiver must securely share a key in advance.
</p><p>Because symmetric key algorithms are nearly always much less computationally intensive than asymmetric ones, it is common to exchange a key using a <a href="https://en.wikipedia.org/wiki/Key_exchange" class="external text" title="https://en.wikipedia.org/wiki/Key_exchange" rel="nofollow">key-exchange algorithm</a>, then transmit data using that key and a symmetric key algorithm. <a href="https://en.wikipedia.org/wiki/Pretty_Good_Privacy" class="external text" title="https://en.wikipedia.org/wiki/Pretty_Good_Privacy" rel="nofollow">PGP(Pretty Good Privacy)</a> and the <a href="https://en.wikipedia.org/wiki/Transport_Layer_Security" class="external text" title="https://en.wikipedia.org/wiki/Transport_Layer_Security" rel="nofollow">SSL/TLS</a> family of schemes use this procedure, and are thus called <b><a href="https://en.wikipedia.org/wiki/Hybrid_cryptosystem" class="external text" title="https://en.wikipedia.org/wiki/Hybrid_cryptosystem" rel="nofollow">hybrid cryptosystems</a></b>.
</p><p>Asymmetric key techniques for varied purposes include: RSA, DH, DSS, Various elliptic curve techniques, Various password-authenticated key agreement techniques, ... <br>
Protocols using asymmetric key algorithms include: SSL/TLS, SSH, S/MIME, ...
</p>
<a name="Symmetric-key_algorithms" id="Symmetric-key_algorithms"></a><h3> <span class="mw-headline"> <a href="https://en.wikipedia.org/wiki/Symmetric-key_algorithm" class="external text" title="https://en.wikipedia.org/wiki/Symmetric-key_algorithm" rel="nofollow">Symmetric-key algorithms</a> </span></h3>
<p>Symmetric-key algorithms are algorithms for cryptography that use the same cryptographic keys for both encryption of plaintext and decryption of ciphertext. The keys may be identical or there may be a simple transformation to go between the two keys. The keys, in practice, represent a shared secret between two or more parties that can be used to maintain a private information link. This requirement that both parties have access to the secret key is one of the main drawbacks of symmetric key encryption, in comparison to public-key encryption.
</p><p>Examples of popular symmetric algorithms include Twofish, Serpent, AES (Rijndael), Blowfish, CAST5, RC4, 3DES, Skipjack, Safer+/++ (Bluetooth), and IDEA
</p>
<a name="Key_exchange" id="Key_exchange"></a><h3> <span class="mw-headline"> <a href="https://en.wikipedia.org/wiki/Key_exchange" class="external text" title="https://en.wikipedia.org/wiki/Key_exchange" rel="nofollow">Key exchange</a> </span></h3>
<p><b>Key exchange</b> (also known as "key establishment") is any method in cryptography by which cryptographic keys are exchanged between two parties, allowing use of a cryptographic algorithm.
</p><p>If sender and receiver wish to exchange encrypted messages, each must be equipped to encrypt messages to be sent and decrypt messages received. The nature of the equipping they require depends on the encryption technique they might use.
</p>
<ul><li> If they use a code, both will require a copy of the same codebook. 
</li><li> If they use a cipher, they will need appropriate keys. 
<ul><li> If the cipher is a symmetric key cipher, both will need a copy of the same key. 
</li><li> If an asymmetric key cipher with the public/private key property, both will need the other's public key.
</li></ul>
</li></ul>
<a name="Digital_Signature" id="Digital_Signature"></a><h3> <span class="mw-headline"> <a href="https://en.wikipedia.org/wiki/Digital_signature" class="external text" title="https://en.wikipedia.org/wiki/Digital_signature" rel="nofollow">Digital Signature</a> </span></h3>
<p>Message authentication involves hashing the message to produce a "digest," and encrypting the digest with the private key to produce a digital signature. Thereafter anyone can verify this signature by (1) computing the hash of the message, (2) decrypting the signature with the signer's public key, and (3) comparing the computed digest with the decrypted digest. Equality between the digests confirms the message is unmodified since it was signed, and that the signer, and no one else, intentionally performed the signature operation — presuming the signer's private key has remained secret. 
</p><p>The security of such procedure depends on a hash algorithm of such quality that it is computationally impossible to alter or find a substitute message that produces the same digest - but studies have shown that even with the MD5 and SHA-1 algorithms, producing an altered or substitute message is not impossible. The current hashing standard for encryption is SHA-2. The message itself can also be used in place of the digest.
</p>
<a name="Public_key_certificate" id="Public_key_certificate"></a><h3> <span class="mw-headline"> <a href="https://en.wikipedia.org/wiki/Public_key_certificate" class="external text" title="https://en.wikipedia.org/wiki/Public_key_certificate" rel="nofollow">Public key certificate</a> </span></h3>
<p>In <a href="https://en.wikipedia.org/wiki/cryptography" class="external text" title="https://en.wikipedia.org/wiki/cryptography" rel="nofollow">cryptography</a>, a <b>public key certificate</b> (also known as a <b>digital certificate</b> or <b>identity certificate</b>) is an electronic document used to prove ownership of a <a href="/mediawiki/index.php?title=Public_key&amp;action=edit&amp;redlink=1" class="new" title="Public key (page does not exist)">public key</a>. The certificate includes information about the key, information about its owner's identity, and the <a href="https://en.wikipedia.org/wiki/digital_signature" class="external text" title="https://en.wikipedia.org/wiki/digital_signature" rel="nofollow">digital signature</a> of an entity that has verified the certificate's contents are correct. If the signature is valid, and the person examining the certificate trusts the signer, then they know they can use that key to communicate with its owner.
</p>
<a name="Public_key_infrastructure.28PKI.29" id="Public_key_infrastructure.28PKI.29"></a><h3> <span class="mw-headline"> <a href="https://en.wikipedia.org/wiki/Public_key_infrastructure" class="external text" title="https://en.wikipedia.org/wiki/Public_key_infrastructure" rel="nofollow">Public key infrastructure(PKI)</a> </span></h3>
<div class="thumb tright"><div class="thumbinner" style="width:402px;"><a href="/mediawiki/index.php/File:Public-Key-Infrastructure.png" class="image" title="Diagram of a public key infrastructure"></a>  <div class="thumbcaption">Diagram of a public key infrastructure</div></div></div>
<p>In cryptography, a PKI is an arrangement that binds public keys with respective user identities by means of a certificate authority (CA). The user identity must be unique within each CA domain. The third-party validation authority (VA) can provide this information on behalf of the CA. The binding is established through the registration and issuance process. Depending on the assurance level of the binding, this may be carried out by software at a CA or under human supervision. The PKI role that assures this binding is called the registration authority (RA). The RA is responsible for accepting requests for digital certificates and authenticating the person or organization making the request. In a Microsoft PKI, a registration authority is usually called a subordinate CA.
</p>
<a name="Certificate_Authority" id="Certificate_Authority"></a><h3> <span class="mw-headline"> <a href="https://en.wikipedia.org/wiki/Certificate_authority" class="external text" title="https://en.wikipedia.org/wiki/Certificate_authority" rel="nofollow">Certificate Authority</a> </span></h3>
<p>In cryptography, a CA is an entity that issues digital certificates. A digital certificate certifies the ownership of a public key by the named subject of the certificate. This allows others (relying parties) to rely upon signatures or on assertions made by the private key that corresponds to the certified public key. In this model of trust relationships, a CA is a trusted third party – trusted both by the subject (owner) of the certificate and by the party relying upon the certificate. Many public-key infrastructure (PKI) schemes feature CAs.
</p>
<a name="X.509" id="X.509"></a><h3> <span class="mw-headline"> <a href="https://en.wikipedia.org/wiki/X.509" class="external text" title="https://en.wikipedia.org/wiki/X.509" rel="nofollow">X.509</a> </span></h3>
<p>In cryptography, <b>X.509</b> is an ITU-T standard for a public key infrastructure (PKI) and Privilege Management Infrastructure (PMI). X.509 specifies, amongst other things, standard formats for public key certificates, certificate revocation lists, attribute certificates, and a certification path validation algorithm.
</p><p>In the X.509 system, a certification authority issues a certificate binding a public key to a particular Distinguished Name#Directory structure|distinguished name in the X.500 tradition, or to an <i>alternative name</i> such as an e-mail address or a Domain Name System|DNS entry.
</p><p>An organization's trusted root certificates can be distributed to all employees so that they can use the company PKI system. Browsers such as Internet Explorer, Firefox, Opera (web browser)|Opera, Safari (web browser)|Safari and Google Chrome|Chrome come with a predetermined set of root certificates pre-installed, so Secure Sockets Layer|SSL certificates from larger vendors will work instantly; in effect the browsers' developers determine which CAs are trusted third parties for the browsers' users.
</p><p>X.509 also includes standards for certificate revocation list (CRL) implementations, an often neglected aspect of PKI systems. The IETF-approved way of checking a certificate's validity is the Online Certificate Status Protocol (OCSP). Firefox 3 enables OCSP checking by default along with versions of Windows including Vista and later.
</p>
<a name="Openssl_Functions" id="Openssl_Functions"></a><h2> <span class="mw-headline"> Openssl Functions </span></h2>
<a name="Synopsis" id="Synopsis"></a><h3> <span class="mw-headline"> Synopsis </span></h3>
<p>openssl command [ command_opts ] [ command_args ]
</p><p>openssl [ list-standard-commands | list-message-digest-commands | list-cipher-commands | list-cipher-algorithms | list-message-digest-algorithms | list-public-key-algorithms]
</p><p>openssl no-XXX [ arbitrary options ] # If no command named XXX exists, it returns 0 (success) and prints no-XXX&nbsp;; otherwise it returns 1 and prints XXX .
</p>
<a name="Description" id="Description"></a><h3> <span class="mw-headline"> Description </span></h3>
<p>OpenSSL is a cryptography toolkit implementing the Secure Sockets Layer ( SSL v2/v3) and Transport Layer Security ( TLS v1) network protocols and related cryptography standards required by them.
</p><p>The openssl program is a command line tool for using the various cryptography functions of OpenSSL's crypto library from the shell. It can be used for
</p>
<ul><li>  Creation and management of private keys, public keys and parameters
</li><li>  Public key cryptographic operations
</li><li>  Creation of X.509 certificates, CSRs and CRLs
</li><li>  Calculation of Message Digests
</li><li>  Encryption and Decryption with Ciphers
</li><li>  SSL/TLS Client and Server Tests
</li><li>  Handling of S/MIME signed or encrypted mail
</li><li>  Time Stamp requests, generation and verification
</li></ul>
<a name="Commands" id="Commands"></a><h3> <span class="mw-headline"> Commands </span></h3>
<pre>[leo@centOS ~]$ openssl help
openssl:Error: 'help' is an invalid command.

Standard commands
asn1parse         ca                ciphers           cms
crl               crl2pkcs7         dgst              dh
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
</pre>
<a name="Common_Standard_Commands" id="Common_Standard_Commands"></a><h3> <span class="mw-headline"> Common Standard Commands </span></h3>
<ul><li> <b>rsa</b>       RSA key management.
</li><li> <b>genrsa</b>    Generation of RSA Private Key. Superceded by genpkey.
</li><li> <b>genpkey</b>   Generation of Private Key or Parameters.
</li></ul>
<p>RSA is a public-key cryptosystems and is widely used for secure data transmission. 
</p>
<pre>## openssl genpkey 
# openssl genrsa -des3 -out private.pem 2048 # generates a key pair, encrypts them with a password you provide
Generating RSA private key, 2048 bit long modulus
.+++
.................................+++
e is 65537 (0x10001)
Enter pass phrase for private.pem:
Verifying - Enter pass phrase for private.pem:
#
# openssl rsa -in private.pem -pubout -out public.pem
Enter pass phrase for private.pem:
writing RSA key
# cat public.pem
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAnx6whBdBG+u2lu18G8RJ
5RLliY6AyfGNQzBQ/fOTE/QjpVyOkQSe17BYYKYs+tKomti0VdlDgF2qnq/ECknv
OQg/CEcgG0jGADW9AxmoatrwIeupOZpV3oFiPIstPGwgTidmIPkiqiCdw4ciONjA
4kaQE91xl9lVDZrK7n5HUkRLzJAmxuMIUI2Q7CCmjfOoKzmlcwNzIldL4yH0LvmS
J4C7ujBBy1vkMtlvtFEPDmfilS0VWbdgk60PV4pnmqDjNvkBZGL/VA1qBcrBnX5L
IPoO3bFfpxGyJwS3NR4TUcWWDs8YNU4uzuAZ8tORAwLMZpDIPLtbg0nuVnNh0u/a
zQIDAQAB
-----END PUBLIC KEY-----
</pre>
<ul><li> <b>ciphers</b>   Cipher Suite Description Determination.
</li><li> <b>dgst</b>      Message Digest Calculation.
</li><li> <b>dsa</b>       DSA Data Management.
</li><li> <b>ec</b>        EC (Elliptic curve) key processing
</li><li> <b>ecparam</b>   EC parameter manipulation and generation
</li><li> <b>enc</b>       Encoding with Ciphers.
</li></ul>
<pre># cat a.txt
This a string for testing openssl.
# openssl enc -aes128 -a -k 123 -in test.txt -out a.aes128.base64
# cat test.aes128.base64
U2FsdGVkX18IIR5XPWHaxsZUlC+ChR2AWQomhqignzWlnEuo66Pi2WT/PvQ9TsDF
w84yYKodtABcUlsparDpLQ==
# openssl enc -aes128 -a -d -k 123 -in a.aes128.base64
This a string for testing openssl.
</pre>
<ul><li> <b>passwd</b>    Generation of hashed passwords.
</li></ul>
<pre># openssl passwd password
w/jSAugKD.UAg
# openssl passwd -crypt -salt xx password # Use the crypt algorithm (default) and specified salt
xxj31ZMTZzkVA.
# openssl passwd -apr1 -salt xxxxxxxx password #  Use the apr1 algorithm and specified salt
$apr1$xxxxxxxx$dxHfLAsjHkDRmG83UXe8K0.
</pre>
<ul><li> <b>pkey</b>      Public and private key management.
</li></ul>
<ul><li> <b>pkeyparam</b> Public key algorithm parameter management.
</li></ul>
<ul><li> <b>rand</b>      Generate pseudo-random bytes.
</li></ul>
<pre># openssl rand -hex 15 # show the 15 random bytes as a hex string 
65f4481c016cc65986af04e7722517
</pre>
<a name="Message_Digest" id="Message_Digest"></a><h2> <span class="mw-headline"> Message Digest </span></h2>
<p>A message digest is a cryptographic hash function containing a string of digits created by a one-way hashing formula. 
</p><p>Message digests are designed to protect the integrity of a piece of data or media to detect changes and alterations to any part of a message. They are a type of cryptography utilizing hash values that can warn the copyright owner of any modifications applied to their work.
</p>
<pre># openssl list-message-digest-commands
md2
md4
md5
rmd160
sha
sha1
#
# openssl list-message-digest-algorithms | cut -d " " -f 3 | sort | uniq
DSA
DSA-SHA
DSA-SHA1
ecdsa-with-SHA1
MD4
MD5
RIPEMD160
RSA-SHA1
SHA
SHA1
SHA224
SHA256
SHA384
SHA512
whirlpool
</pre>
<p><br>
</p>
<a name="Key_Words" id="Key_Words"></a><h2> <span class="mw-headline"> Key Words </span></h2>
<a name="MD.28Message_Digest.29" id="MD.28Message_Digest.29"></a><h3> <span class="mw-headline"> MD(Message Digest) </span></h3>
<a name="PEM_DER" id="PEM_DER"></a><h3> <span class="mw-headline"> <a href="https://support.quovadisglobal.com/kb/a37/what-is-pem-format.aspx" class="external text" title="https://support.quovadisglobal.com/kb/a37/what-is-pem-format.aspx" rel="nofollow">PEM</a> <a href="http://www.herongyang.com/Cryptography/Certificate-Format-DER-Distinguished-Encoding-Rules.html" class="external text" title="http://www.herongyang.com/Cryptography/Certificate-Format-DER-Distinguished-Encoding-Rules.html" rel="nofollow">DER</a> </span></h3>
<p>PEM, DER, all of them are format of certification.
DER (Distinguished Encoding Rules) is one of ASN.1 encoding rules defined in ITU-T X.690, 2002, specification. ASN.1 encoding rules can be used to encode any data object into a binary file.
</p><p>The basic encoding rule of DER is that a data value of all data types shall be encoded as four components in the following order:Identifier octets,Length octets,Contents octets,End-of-contents octets
</p><p>PEM or Privacy Enhanced Mail is a Base64 encoded DER certificate.  PEM certificates are frequently used for web servers as they can easily be translated into readable data using a simple text editor.  Generally when a PEM encoded file is opened in a text editor, it contains very distinct headers and footers. 
</p>
<pre>-----BEGIN CERTIFICATE REQUEST-----
MIIB9TCCAWACAQAwgbgxGTAXBgNVBAoMEFF1b1ZhZGlzIExpbWl0ZWQxHDAaBgNV
...
98TwDIK/39WEB/V607As+KoYazQG8drorw==
-----END CERTIFICATE REQUEST-----
</pre>
<p>Above is the example of a CSR (certificate signing request) in PEM format.  You can see that PEM has the characteristics of containing a header, the body (which consists mainly of code) and footer.
</p>

## <a id=CDP>CDP ssl weak ciphers</a>
<div id="bodyContent">	
<table id="toc" class="toc" summary="Contents"><tbody><tr><td><div id="toctitle"><h2>Contents</h2> 
</div>
<ul>
<li class="toclevel-1"><a href="#Vulnerability_Description"><span class="tocnumber">1</span> <span class="toctext">Vulnerability Description</span></a>
<ul>
<li class="toclevel-2"><a href="#Case_Info"><span class="tocnumber">1.1</span> <span class="toctext">Case Info</span></a></li>
<li class="toclevel-2"><a href="#Affected_Nodes"><span class="tocnumber">1.2</span> <span class="toctext">Affected Nodes</span></a></li>
</ul>
</li>
<li class="toclevel-1"><a href="#Background_Knowledge"><span class="tocnumber">2</span> <span class="toctext">Background Knowledge</span></a>
<ul>
<li class="toclevel-2"><a href="#SSL.2FTSL_Protocol"><span class="tocnumber">2.1</span> <span class="toctext">SSL/TSL Protocol</span></a></li>
<li class="toclevel-2"><a href="#SSL.2FTLS_Development"><span class="tocnumber">2.2</span> <span class="toctext">SSL/TLS Development</span></a></li>
<li class="toclevel-2"><a href="#Cipher_Suits"><span class="tocnumber">2.3</span> <span class="toctext">Cipher Suits</span></a></li>
<li class="toclevel-2"><a href="#SSL.2FTLS_configurations"><span class="tocnumber">2.4</span> <span class="toctext">SSL/TLS configurations</span></a>
<ul>
<li class="toclevel-3"><a href="#Modern_compatibility"><span class="tocnumber">2.4.1</span> <span class="toctext">Modern compatibility</span></a></li>
<li class="toclevel-3"><a href="#Intermediate_compatibility_.28default.29"><span class="tocnumber">2.4.2</span> <span class="toctext">Intermediate compatibility (default)</span></a></li>
<li class="toclevel-3"><a href="#Old_backward_compatibility"><span class="tocnumber">2.4.3</span> <span class="toctext">Old backward compatibility</span></a></li>
</ul>
</li>
</ul>
</li>
<li class="toclevel-1"><a href="#Vulnerability_Solution"><span class="tocnumber">3</span> <span class="toctext">Vulnerability Solution</span></a>
<ul>
<li class="toclevel-2"><a href="#Step_1._Confirm_the_versions_of_SSL.2FTLS"><span class="tocnumber">3.1</span> <span class="toctext">Step 1. Confirm the versions of SSL/TLS</span></a></li>
<li class="toclevel-2"><a href="#Step_2._Confirm_the_ciphersuites"><span class="tocnumber">3.2</span> <span class="toctext">Step 2. Confirm the ciphersuites</span></a>
<ul>
<li class="toclevel-3"><a href="#Common_Linux_System"><span class="tocnumber">3.2.1</span> <span class="toctext">Common Linux System</span></a></li>
<li class="toclevel-3"><a href="#ACDP_System"><span class="tocnumber">3.2.2</span> <span class="toctext">ACDP System</span></a></li>
</ul>
</li>
<li class="toclevel-2"><a href="#Step_3._Configure_your_servers"><span class="tocnumber">3.3</span> <span class="toctext">Step 3. Configure your servers</span></a>
<ul>
<li class="toclevel-3"><a href="#Microsoft_IIS_web_server"><span class="tocnumber">3.3.1</span> <span class="toctext">Microsoft IIS web server</span></a></li>
<li class="toclevel-3"><a href="#Apache"><span class="tocnumber">3.3.2</span> <span class="toctext">Apache</span></a></li>
<li class="toclevel-3"><a href="#Tomcat_6.0"><span class="tocnumber">3.3.3</span> <span class="toctext">Tomcat 6.0</span></a></li>
<li class="toclevel-3"><a href="#Tomcat_7.0_.7E_8.0"><span class="tocnumber">3.3.4</span> <span class="toctext">Tomcat 7.0 ~ 8.0</span></a></li>
<li class="toclevel-3"><a href="#Nginx"><span class="tocnumber">3.3.5</span> <span class="toctext">Nginx</span></a></li>
<li class="toclevel-3"><a href="#Other"><span class="tocnumber">3.3.6</span> <span class="toctext">Other</span></a></li>
</ul>
</li>
<li class="toclevel-2"><a href="#Step_4._Restart_your_services"><span class="tocnumber">3.4</span> <span class="toctext">Step 4. Restart your services</span></a></li>
</ul>
</li>
<li class="toclevel-1"><a href="#Ralated_Issue:_Server_has_a_weak_ephemeral_Diffie-Hellman_public_key"><span class="tocnumber">4</span> <span class="toctext">Ralated Issue: Server has a weak ephemeral Diffie-Hellman public key</span></a>
<ul>
<li class="toclevel-2"><a href="#description"><span class="tocnumber">4.1</span> <span class="toctext">description</span></a></li>
<li class="toclevel-2"><a href="#investigation"><span class="tocnumber">4.2</span> <span class="toctext">investigation</span></a></li>
<li class="toclevel-2"><a href="#solution"><span class="tocnumber">4.3</span> <span class="toctext">solution</span></a></li>
<li class="toclevel-2"><a href="#how_to_generate_ciphers"><span class="tocnumber">4.4</span> <span class="toctext">how to generate ciphers</span></a></li>
</ul>
</li>
<li class="toclevel-1"><a href="#Reference"><span class="tocnumber">5</span> <span class="toctext">Reference</span></a></li>
</ul>
</td></tr></tbody></table><script type="text/javascript"> if (window.showTocToggle) { var tocShowText = "show"; var tocHideText = "hide"; showTocToggle(); } </script>
<a name="Vulnerability_Description" id="Vulnerability_Description"></a><h1> <span class="mw-headline"> Vulnerability Description </span></h1>
<a name="Case_Info" id="Case_Info"></a><h2> <span class="mw-headline"> Case Info </span></h2>
<p>The TLS/SSL server supports cipher suites based on weak algorithms. This may enable an attacker to launch man-in-the-middle attacks and monitor or tamper with sensitive data. 
</p><p>In general, the following ciphers are considered weak:
</p>
<ul><li> So called "null" ciphers, because they do not encrypt data.
</li><li> Export ciphers using secret key lengths restricted to 40 bits. This is usually indicated by the word EXP/EXPORT in the name of the cipher suite.
</li><li> Obsolete encryption algorithms with secret key lengths considered short by today's standards, eg. DES or RC4 with 56-bit keys.
</li></ul>
<a name="Affected_Nodes" id="Affected_Nodes"></a><h2> <span class="mw-headline"> Affected Nodes </span></h2>
<p>Every CDP Node which will use SSL/TLS protocol, e.g., HTTPS Server, FTP Server, LDAP Server, SMTP Server, etc.
</p><p>At present, the affected nodes of CDP are listed as follows:
</p>
<table border="1">

<tbody><tr>
<th width="200px"> Affected Node
</th><th> Affected Service
</th><th> Additional Information
</th></tr>
<tr>
<td> CDP Management Node:443
</td><td> <center>HTTPS</center>
</td><td> Negotiated with the following insecure cipher suites. SSLv3 ciphers: <br>
<p>SSL_RSA_WITH_DES_CBC_SHA <br>
SSL_DHE_RSA_EXPORT_WITH_DES40_CBC_SHA <br>
SSL_RSA_EXPORT_WITH_DES40_CBC_SHA <br>
SSL_DHE_RSA_WITH_DES_CBC_SHA <br>
SSL_RSA_EXPORT_WITH_RC4_40_MD5 
</p>
</td></tr></tbody></table>
<a name="Background_Knowledge" id="Background_Knowledge"></a><h1> <span class="mw-headline"> Background Knowledge </span></h1>
<a name="SSL.2FTSL_Protocol" id="SSL.2FTSL_Protocol"></a><h2> <span class="mw-headline"> SSL/TSL Protocol </span></h2>
<p>SSL and TSL are cryptographic protocols designed to provide communications security over a computer network. 
Several versions of the protocols are in widespread use in applications such as web browsing, email, Internet faxing, instant messaging, and voice-over-IP (VoIP). 
</p><p>You can use SSL/TLS to authenticate servers and clients and then use it to encrypt messages between the authenticated parties.In the authentication process, a TLS/SSL client sends a message to a TLS/SSL server, and the server responds with the information that the server needs to authenticate itself. The client and server perform an additional exchange of session keys, and the authentication dialog ends. When authentication is completed, SSL-secured communication can begin between the server and the client using the symmetric encryption keys that are established during the authentication process.
</p>
<a name="SSL.2FTLS_Development" id="SSL.2FTLS_Development"></a><h2> <span class="mw-headline"> SSL/TLS Development </span></h2>
<p><b>SSL 1.0, 2.0 and 3.0</b>
</p><p>Netscape developed the original SSL protocols. 
</p><p>SSL 1.0 was never publicly released because of serious security flaws in the protocol; 
</p><p>SSL 2.0, released in February 1995, which contained a number of security flaws which ultimately led to the design of SSL 3.0, and SSl 2.0 deprecated in 2011 by <a href="http://tools.ietf.org/html/rfc6176" class="external" title="http://tools.ietf.org/html/rfc6176">RFC 6176</a>.
</p><p>SSL 3.0, released in 1996, represented a complete redesign of the protocol.In 2014, SSL 3.0 is considered insecure as it is vulnerable to the POODLE attack that affects all block ciphers in SSL; and RC4, the only non-block cipher supported by SSL 3.0, is also feasibly broken as used in SSL 3.0. And SSL 3.0 deprecated in June 2015 by <a href="http://tools.ietf.org/html/rfc7568" class="external" title="http://tools.ietf.org/html/rfc7568">RFC 7568</a>.
</p><p><b>TLS 1.0</b>
</p><p>TLS 1.0 was first defined in <a href="http://tools.ietf.org/html/rfc2246" class="external" title="http://tools.ietf.org/html/rfc2246">RFC 2246</a> in January 1999 as an upgrade of SSL Version 3.0. As stated in the RFC, "the differences between this protocol and SSL 3.0 are not dramatic, but they are significant enough to preclude interoperability between TLS 1.0 and SSL 3.0". TLS 1.0 does include a means by which a TLS implementation can downgrade the connection to SSL 3.0, thus weakening security.
</p><p><b>TLS 1.1</b>
</p><p>TLS 1.1 was defined in <a href="http://tools.ietf.org/html/rfc4346" class="external" title="http://tools.ietf.org/html/rfc4346">RFC 4346</a> in April 2006. It is an update from TLS version 1.0. Significant differences in this version include:
</p>
<ul><li> Added protection against cipher-block chaining (CBC) attacks.
<ul><li> The implicit initialization vector (IV) was replaced with an explicit IV.
</li><li> Change in handling of padding errors.
</li></ul>
</li><li> Support for IANA registration of parameters.
</li></ul>
<p><b>TLS 1.2</b>: TLS 1.2 was defined in <a href="http://tools.ietf.org/html/rfc5246" class="external" title="http://tools.ietf.org/html/rfc5246">RFC 5246</a> in August 2008. It is based on the earlier TLS 1.1 specification. 
</p><p><b>TLS 1.3 (draft)</b>: As of October 2015, TLS 1.3 is a working draft, and details are provisional and incomplete. It is based on the earlier TLS 1.2 specification. 
</p>
<a name="Cipher_Suits" id="Cipher_Suits"></a><h2> <span class="mw-headline"> Cipher Suits </span></h2>
<p>Cipher Suite just is algorithms used to negotiate the security settings for SSL/TLS. Before a client and server can begin to exchange information protected by TLS, they must securely exchange or agree upon an encryption key and a cipher to use when encrypting data. Public and private keys are common generated with RSA, Diffie-Hellman (TLS_DH), ephemeral Diffie-Hellman (TLS_DHE), Elliptic Curve Diffie-Hellman (TLS_ECDH), ephemeral Elliptic Curve Diffie-Hellman (TLS_ECDHE), anonymous Diffie-Hellman (TLS_DH_anon), pre-shared key (TLS_PSK) and Secure Remote Password (TLS_SRP).
</p><p>A reference for named cipher suites is provided in the TLS Cipher Suite Registry(<a href="https://www.iana.org/assignments/tls-parameters/tls-parameters.xhtml#tls-parameters-4" class="external free" title="https://www.iana.org/assignments/tls-parameters/tls-parameters.xhtml#tls-parameters-4" rel="nofollow">https://www.iana.org/assignments/tls-parameters/tls-parameters.xhtml#tls-parameters-4</a>)
</p>
<a name="SSL.2FTLS_configurations" id="SSL.2FTLS_configurations"></a><h2> <span class="mw-headline"> SSL/TLS configurations </span></h2>
<p>Three configurations are recommended. Pick the right configuration depending on your audience. If you do not need backward compatibility, and are building a service for modern clients only (post FF27), then use the Modern configuration. Otherwise, prefer the Intermediate configuration. Use the Old backward compatible configuration only if your service will be accessed by very old clients, such as Windows XP IE6, or ancient libraries &amp; bots.
</p>
<pre><b>Configuration</b>	<b>Oldest compatible client</b>
Modern	        Firefox 27, Chrome 22, IE 11, Opera 14, Safari 7, Android 4.4, Java 8
Intermediate	Firefox 1, Chrome 1, IE 7, Opera 5, Safari 1, Windows XP IE8, Android 2.3, Java 7
Old	        Windows XP IE6, Java 6
</pre>
<a name="Modern_compatibility" id="Modern_compatibility"></a><h3> <span class="mw-headline"> Modern compatibility </span></h3>
<p>For services that don't need backward compatibility, the parameters below provide a higher level of security. This configuration is compatible with Firefox 27, Chrome 22, IE 11, Opera 14 and Safari 7.
</p><p>Ciphersuite: ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!3DES:!MD5:!PSK <br>
Versions: TLSv1.1, TLSv1.2<br>
</p>
<a name="Intermediate_compatibility_.28default.29" id="Intermediate_compatibility_.28default.29"></a><h3> <span class="mw-headline"> Intermediate compatibility (default) </span></h3>
<p>For services that don't need compatibility with legacy clients (mostly WinXP), but still need to support a wide range of clients, this configuration is recommended. It is is compatible with Firefox 1, Chrome 1, IE 7, Opera 5 and Safari 1.
</p><p>Ciphersuite: ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:ECDHE-RSA-DES-CBC3-SHA:ECDHE-ECDSA-DES-CBC3-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:AES:CAMELLIA:DES-CBC3-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA<br>
Versions: TLSv1, TLSv1.1, TLSv1.2<br>
</p>
<a name="Old_backward_compatibility" id="Old_backward_compatibility"></a><h3> <span class="mw-headline"> Old backward compatibility </span></h3>
<p>This is the old ciphersuite that works with all clients back to Windows XP/IE6. It should be used as a last resort only.
</p><p>Ciphersuite: ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:ECDHE-RSA-DES-CBC3-SHA:ECDHE-ECDSA-DES-CBC3-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:AES:DES-CBC3-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA<br>
Versions: SSLv3, TLSv1, TLSv1.1, TLSv1.2<br>
</p>
<a name="Vulnerability_Solution" id="Vulnerability_Solution"></a><h1> <span class="mw-headline"> Vulnerability Solution </span></h1>
<p>Owning to low versions and weak ciphers used on SSL/TLS server, you should reconfigure the server to disable support for low versions and weak ciphers, then restart your services which use SSL/TLS.
</p>
<a name="Step_1._Confirm_the_versions_of_SSL.2FTLS" id="Step_1._Confirm_the_versions_of_SSL.2FTLS"></a><h2> <span class="mw-headline"> Step 1. Confirm the versions of SSL/TLS </span></h2>
<p>Because of that SSL 2.0 and SSL 3.0 had were deprecated and TLS 1.3 is at status of TBD, TLS 1.0 ~ 1.2 are suitable and used at most of web server commonly at present.
</p><p>But if the version of your openssl is 0.9.8 which don’t support TLS 1.1 and TLS 1.2, so just TLS 1.0 is suitable.
</p>
<a name="Step_2._Confirm_the_ciphersuites" id="Step_2._Confirm_the_ciphersuites"></a><h2> <span class="mw-headline"> Step 2. Confirm the ciphersuites </span></h2>
<a name="Common_Linux_System" id="Common_Linux_System"></a><h3> <span class="mw-headline"> Common Linux System </span></h3>
<p><b>OpenSSL ciphers</b>
</p><p>In view of moderate backward compatibility, supporting Internet Explorer 7, Opera 5, and all versions of Firefox, Chrome, and Safari. And refer to your server vendor documentation to apply the recommended cipher configuration as follows(note: following ciphers string use OpenSSL nameing):
</p>
<pre>TLSv1:!LOW:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA
</pre>
<p><b>JSSE ciphers</b>
</p><p>If you want to configure a pure java ( Tomcat, Jetty ) based WebServer, not an Apache or OpenSSL based webserver, you need to map above OpenSSL's ciphers to Java JSSE.
</p><p>Java's JSSE cipher suite names correspond to the standard <b>TLS Cipher Suite List</b> <a href="http://www.iana.org/assignments/tls-parameters/tls-parameters.xml" class="external free" title="http://www.iana.org/assignments/tls-parameters/tls-parameters.xml" rel="nofollow">http://www.iana.org/assignments/tls-parameters/tls-parameters.xml</a>) , where you can download a CSV file of the official codes and their names at the TLS Cipher Suite Registry section. The CSV file is called <b>tls-parameters-4.csv</b> (<a href="http://www.iana.org/assignments/tls-parameters/tls-parameters-4.csv" class="external free" title="http://www.iana.org/assignments/tls-parameters/tls-parameters-4.csv" rel="nofollow">http://www.iana.org/assignments/tls-parameters/tls-parameters-4.csv</a>). With a little shell script you can map the OpenSSL name to the JSSE name - via the official hex code of the cipher.
</p>
<pre>$ vi resolve.sh
#!/bin/bash
JSSE_CIPHERS=
while read line
do
   HEX_CODE=`echo $line | cut -d '-' -f1`
   JSSE_CIPHER=`grep $HEX_CODE tls-parameters-4.csv | cut -d ',' -f3`
   JSSE_CIPHERS=$JSSE_CIPHERS,$JSSE_CIPHER
done

if [[ $JSSE_CIPHERS == ,* ]]&nbsp;;
then
   # echo 'ciphers start with ,'
   JSSE_CIPHERS=`echo $JSSE_CIPHERS | cut -c 2- `
fi

if [[ $JSSE_CIPHERS == *, ]]&nbsp;;
then
   # echo 'ciphers end with ,'
   JSSE_CIPHERS=`echo $JSSE_CIPHERS | rev | cut -c 2- | rev`
fi

echo "ciphers="$JSSE_CIPHERS
</pre>
<p>Piping the output of openssl ciphers -V into the resolve.sh script will result in a correctly sorted java cipher suite list.
</p>
<pre> $ openssl ciphers -V '&lt;paste recommended ciphersuite here&gt;' | column -t | ./resolve.sh 
 ciphers=TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256,...,TLS_DHE_DSS_WITH_CAMELLIA_128_CBC_SHA,TLS_RSA_WITH_CAMELLIA_128_CBC_SHA
</pre>
<a name="ACDP_System" id="ACDP_System"></a><h3> <span class="mw-headline"> ACDP System </span></h3>
<p>Owning to that the version of Openssl on CDP is lower and can't be upgraded easily, when you plan to get JSSE ciphers string and type 'openssl cipher -V', 
you can't see the official hex code of ciphers but error message 'error:1410D0B9:SSL routines:SSL_CTX_set_cipher_list:no cipher match:ssl_lib.c:1188'.
</p><p>A feasible method to generate JSSE ciphers on CDP is verified, as follows:
</p>
<ul><li> 1. get a full list of available OpenSSL ciphers on a common linux system, e.g., ubuntu.
</li></ul>
<pre>$ openssl ciphers -V 'ALL:COMPLEMENTOFALL:eNULL'| column -t &gt; ciphers.list
</pre>
<ul><li> 2. back to CDP node, edit 'resolve.sh', download <b>tls-parameters-4.csv</b>(<a href="http://www.iana.org/assignments/tls-parameters/tls-parameters-4.csv" class="external free" title="http://www.iana.org/assignments/tls-parameters/tls-parameters-4.csv" rel="nofollow">http://www.iana.org/assignments/tls-parameters/tls-parameters-4.csv</a>) and get 'ciphers.list' from step 1.
</li></ul>
<pre>$ vi resolve.sh
#!/bin/bash
JSSE_CIPHERS=
while read line
do
CODE=`grep " $line " ciphers.list | cut -d '-' -f1`
JSSE_CIPHER=`grep $CODE tls-parameters-4.csv | cut -d ',' -f3`
JSSE_CIPHERS=$JSSE_CIPHERS,$JSSE_CIPHER
done

if [[ $JSSE_CIPHERS == ,* ]]&nbsp;;
then
   # echo 'ciphers start with ,'
   JSSE_CIPHERS=`echo $JSSE_CIPHERS | cut -c 2- `
fi

if [[ $JSSE_CIPHERS == *, ]]&nbsp;;
then
   # echo 'ciphers end with ,'
   JSSE_CIPHERS=`echo $JSSE_CIPHERS | rev | cut -c 2- | rev`
fi

echo "ciphers="$JSSE_CIPHERS 
</pre>
<ul><li> 3. generate available JSSE ciphers on CDP
</li></ul>
<pre>$ openssl ciphers -V '&lt;paste recommended ciphersuite here&gt;' | sed 's/:/\n/g' | ./resolve.sh
ciphers=TLS_DHE_RSA_WITH_AES_256_CBC_SHA,TLS_DHE_DSS_WITH_AES_256_CBC_SHA,TLS_RSA_WITH_AES_256_CBC_SHA,TLS_DHE_RSA_WITH_AES_128_CBC_SHA,TLS_DHE_DSS_WITH_AES_128_CBC_SHA,
TLS_RSA_WITH_AES_128_CBC_SHA,TLS_RSA_WITH_3DES_EDE_CBC_SHA
</pre>
<a name="Step_3._Configure_your_servers" id="Step_3._Configure_your_servers"></a><h2> <span class="mw-headline"> Step 3. Configure your servers </span></h2>
<a name="Microsoft_IIS_web_server" id="Microsoft_IIS_web_server"></a><h3> <span class="mw-headline"> Microsoft IIS web server </span></h3>
<p>see: <a href="https://support.microsoft.com/zh-cn/kb/245030" class="external free" title="https://support.microsoft.com/zh-cn/kb/245030" rel="nofollow">https://support.microsoft.com/zh-cn/kb/245030</a>
</p>
<a name="Apache" id="Apache"></a><h3> <span class="mw-headline"> Apache </span></h3>
<p>edit conf file:
</p>
<pre>&lt;VirtualHost *:443&gt;
   ...
   # Intermediate configuration, tweak to your needs.
   SSLProtocol             all -SSLv2 -SSLv3
   SSLCipherSuite          &lt;paste recommended ciphersuite here&gt;
   SSLHonorCipherOrder     on
   SSLCompression          off
   ...
&lt;/VirtualHost&gt;
</pre>
<a name="Tomcat_6.0" id="Tomcat_6.0"></a><h3> <span class="mw-headline"> Tomcat 6.0 </span></h3>
<p><b>Note</b>: CDP use tomcat 6.0
</p><p>edit server.xml
</p>
<pre>...
&lt;Connector port="443" protocol="HTTP/1.1" SSLEnabled="true"
               maxHttpHeaderSize="8192" maxThreads="150" minSpareThreads="25" maxSpareThreads="75"
               keystoreFile="keystore" keystorePass="atmoscdp"
               enableLookups="false" disableUploadTimeout="true"
               acceptCount="100" scheme="https" secure="true"
               clientAuth="false" sslProtocols="TLSv1, TLSv1.1, TLSv1.2"
               ciphers="&lt;paste recommended ciphersuite here&gt;"
 /&gt;
 ...
</pre>
<p><b>Note</b>: If the version of openssl on your CDP is 0.9.8 which don’t support TLS 1.1 and TLS 1.2, sslProtocols must be set to "TLSv1" .
</p>
<a name="Tomcat_7.0_.7E_8.0" id="Tomcat_7.0_.7E_8.0"></a><h3> <span class="mw-headline"> Tomcat 7.0 ~ 8.0 </span></h3>
<p>edit server.xml
</p>
<pre>...
&lt;Connector port="443" protocol="HTTP/1.1" SSLEnabled="true"
               maxHttpHeaderSize="8192" maxThreads="150" minSpareThreads="25" maxSpareThreads="75"
               keystoreFile="keystore" keystorePass="atmoscdp"
               enableLookups="false" disableUploadTimeout="true"
               acceptCount="100" scheme="https" secure="true"
               clientAuth="false" sslEnabledProtocols="TLSv1, TLSv1.1, TLSv1.2"
               ciphers="&lt;paste recommended ciphersuite here&gt;"
 /&gt;
 ...
</pre>
<a name="Nginx" id="Nginx"></a><h3> <span class="mw-headline"> Nginx </span></h3>
<p>edit conf file:
</p>
<pre>server {
   listen 443 ssl;
   ...
   # Intermediate configuration. tweak to your needs.
   ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
   ssl_ciphers '&lt;paste recommended ciphersuite here&gt;';
   ssl_prefer_server_ciphers on;
   ....
}
</pre>
<a name="Other" id="Other"></a><h3> <span class="mw-headline"> Other </span></h3>
<p>see: <a href="https://wiki.mozilla.org/Security/Server_Side_TLS" class="external free" title="https://wiki.mozilla.org/Security/Server_Side_TLS" rel="nofollow">https://wiki.mozilla.org/Security/Server_Side_TLS</a>
</p>
<a name="Step_4._Restart_your_services" id="Step_4._Restart_your_services"></a><h2> <span class="mw-headline"> Step 4. Restart your services </span></h2>
<p>Restart your web server, e.g., restart tomcat on CDP:
</p>
<pre>service tomcat restart
</pre>
<a name="Ralated_Issue:_Server_has_a_weak_ephemeral_Diffie-Hellman_public_key" id="Ralated_Issue:_Server_has_a_weak_ephemeral_Diffie-Hellman_public_key"></a><h1> <span class="mw-headline"> Ralated Issue: Server has a weak ephemeral Diffie-Hellman public key  </span></h1>
<a name="description" id="description"></a><h2> <span class="mw-headline"> description </span></h2>
<p>when you try to open portal of ACDP by use of a ssl/tls connention, you maybe get this error tips: "Server has a weak ephemeral Diffie-Hellman public key" and "ERR_SSL_WEAK_SERVER_EPHEMERAL_DH_KEY".
</p>
<a name="investigation" id="investigation"></a><h2> <span class="mw-headline"> investigation </span></h2>
<p>The background of this issue is alike as which of above issue. If you will set a ssl/tls connection between your web client and web server, web server will choose a cipher suite from its own cipher suites, then generate a secret key to exchange with client . Maybe web server will check the cipher suites supported by your client and make a reference, or maybe not.
</p><p>When the chosen cipher suite is considered insecure by client, or the exchanged secret key is passed for too weak, the connection will fail and be closed.
</p><p>I restored the configuration of tomcat to reproduce this scenario and collect some info from some clients .
</p>
<pre># vim /opt/cloudcommon/apache-tomcat/conf/server.xml
…
    &lt;Connector port="443" protocol="HTTP/1.1" SSLEnabled="true"
                maxHttpHeaderSize="8192" maxThreads="150" minSpareThreads="25" maxSpareThreads="75"
                keystoreFile="keystore" keystorePass="atmoscdp"
                enableLookups="false" disableUploadTimeout="true"
                acceptCount="100" scheme="https" secure="true"
                clientAuth="false" sslProtocol="TLS"
    /&gt; 
…
</pre>
<p>For testing older version of browser, I installed <a href="http://www.my-debugbar.com/wiki/IETester/HomePage" class="external text" title="http://www.my-debugbar.com/wiki/IETester/HomePage" rel="nofollow">IETester</a> to test versions of IE, <a href="http://utilu.com/UtiluMFC/" class="external text" title="http://utilu.com/UtiluMFC/" rel="nofollow">Utilu Mozilla Firefox Collection</a> to test versions of FF, and <a href="http://sourceforge.net/projects/crportable/files/" class="external text" title="http://sourceforge.net/projects/crportable/files/" rel="nofollow">Chromium Portable</a> to test versions of Chrome/Chromium. Following report is the results of affected clients (I have not tested all versions of clients, so more detailed investigation need to be in plan.):
</p>
<ul><li> IE: Not affected
</li><li> Chrome on win7_64: Version 45, 46, 48
</li><li> Firefox on win7_64&nbsp;: Version 31, 38, 42, 43, 44, 45
</li></ul>
<p>Like all good security vulnerabilities,  the suggested fixes for various web servers are listed there. Unfortunately, if you are using Tomcat (not behind an Apache or Nginx proxy, for example), it doesn't seem to be possible to change / improve the required Diffie-Hellman bits parameter. All you can do is disable ciphers using DH (although ECDHE seems to be OK). Further more if you don't have the "JCE Unlimited Strength Jurisdiction Policy Files" set up to enable AES 256, you're also limited to AES 128 ciphers. Also limited to (what seems to be the most common certificate type), RSA.
</p>
<a name="solution" id="solution"></a><h2> <span class="mw-headline"> solution </span></h2>
<p>Step 1: install jre1.6.0_105 on cdp sysmgmt node.
</p><p>Related rpms had been placed at 10.32.120.23:/share/pub/ACDP/jre6u105.rpms/ (root:password),<br>
Rpms dependency had been recorded in file jre6u105.rpm.dependency
</p>
<pre># scp –r root@10.32.120.23:/share/pub/ACDP/jre6u105.rpms ./
# cd jre6u105.rpms
## jre6u105.rpm.dependency could help you to check rpms installation order
# rpm -i  java-1.6.0-sun-1.6.0.105-1jpp.2.el5_11.x86_64.rpm
</pre>
<p>Step 2: change tomcat startup script and configure file, and service tomcat restart.
</p>
<pre># cp /etc/init.d/tomcat tomcat.old
# vi /etc/init.d/tomcat
# diff tomcat.old  /etc/init.d/tomcat
9c9,10
&lt; JAVA_HOME=/usr/java/latest; export JAVA_HOME
---
&gt; #JAVA_HOME=/usr/java/latest; export JAVA_HOME
&gt; JAVA_HOME=/usr/lib/jvm/java-1.6.0-sun-1.6.0.105.x86_64/jre; export JAVA_HOME
12a14
&gt; JAVA_OPTS="$JAVA_OPTS -Djdk.tls.ephemeralDHKeySize=2048"; export JAVA_OPTS
# rm tomcat.old
#
# cp /opt/cloudcommon/apache-tomcat/conf/server.xml server.xml.old
# vi /opt/cloudcommon/apache-tomcat/conf/server.xml
# diff server.xml.old /opt/cloudcommon/apache-tomcat/conf/server.xml
88c88
&lt;               ciphers="TLS_DHE_RSA_WITH_AES_256_CBC_SHA,TLS_DHE_DSS_WITH_AES_256_CBC_SHA,
TLS_DHE_RSA_WITH_AES_128_CBC_SHA,TLS_RSA_WITH_AES_256_CBC_SHA,
TLS_DHE_DSS_WITH_AES_128_CBC_SHA,TLS_RSA_WITH_AES_128_CBC_SHA,TLS_RSA_WITH_3DES_EDE_CBC_SHA"
---
&gt;                ciphers="TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA,TLS_DHE_DSS_WITH_AES_256_CBC_SHA,
TLS_DHE_RSA_WITH_AES_256_CBC_SHA,TLS_RSA_WITH_3DES_EDE_CBC_SHA,TLS_RSA_WITH_AES_256_CBC_SHA"
# rm server.xml.old
# service tomcat restart
</pre>
<a name="how_to_generate_ciphers" id="how_to_generate_ciphers"></a><h2> <span class="mw-headline"> how to generate ciphers </span></h2>
<p>Step 1. Confirm the parameters of security and cipher suits according to the server environment and the client environment.
</p>
<pre>1. SSL/TLS Versions: TLSv1 # SSL protocol is weak and openssl 0.9.8 can't support TLSv1.1 and higher.
2. Ciphers Encryption Algorithms: ECDH, DH(128B), DH(256B), SHA256, etc,.
3. Ciphers Safety Restricts: 
   Not eNULL(offering no encryption)
   Not aNULL(offering no authentication)
   Not LOW(using 64 or 56 bit encryption algorithms)
   Not EXPORT(using export encryption algorithms)
   Not Weak Encryption or Authentication Algorithms(DES, RC2, RC4, MD5, PSK, aPSK, aECDH, AES128, etc,.)
4. Client Compatibility: Firefox , Chrome , IE , Opera , etc.
</pre>
<p>Get cipher matching strings: 
</p>
<pre>'TLSv1:!LOW:!NULL:!aNULL:!eNULL:!EXPORT:!DES:!RC2:!RC4:!MD5:!PSK:!aPSK:!aECDH:!AES128'
</pre>
<p>Step 2. Get suitable ciphers generated by cipher strings of step 1 according to previous procedure on ACDP system.
</p>
<pre># openssl ciphers -V '&lt;cipher matching strings&gt;' | sed 's/:/\n/g' | ./resolve.sh <br>
TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA
TLS_DHE_DSS_WITH_AES_256_CBC_SHA
TLS_DHE_RSA_WITH_3DES_EDE_CBC_SHA
TLS_DHE_RSA_WITH_AES_256_CBC_SHA
TLS_RSA_WITH_3DES_EDE_CBC_SHA
TLS_RSA_WITH_AES_256_CBC_SHA
</pre>
<p>Step 3. Configure the conf file of ACDP web server which would use openssl, then restart your web service.
</p>
<pre># edit /opt/cloudcommon/apache-tomcat/conf/server.xml
...
&lt;Connector port="443" protocol="HTTP/1.1" SSLEnabled="true"
                maxHttpHeaderSize="8192" maxThreads="150" minSpareThreads="25" maxSpareThreads="75"
                keystoreFile="keystore" keystorePass="atmoscdp"
                enableLookups="false" disableUploadTimeout="true"
                acceptCount="100" scheme="https" secure="true"
                clientAuth="false" sslProtocols="TLSv1"
                ciphers="TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA,TLS_DHE_DSS_WITH_AES_256_CBC_SHA,TLS_DHE_RSA_WITH_3DES_EDE_CBC_SHA,
                         TLS_DHE_RSA_WITH_AES_256_CBC_SHA,TLS_RSA_WITH_3DES_EDE_CBC_SHA,TLS_RSA_WITH_AES_256_CBC_SHA"
/&gt;
...
</pre>
<p>Step 4. Check if your web server works correctly on common browser clients
</p>
<a name="Reference" id="Reference"></a><h1> <span class="mw-headline"> Reference </span></h1>
<ul><li> Wikipedia: Transport Layer Security <a href="https://en.wikipedia.org/wiki/Transport_Layer_Security" class="external free" title="https://en.wikipedia.org/wiki/Transport_Layer_Security" rel="nofollow">https://en.wikipedia.org/wiki/Transport_Layer_Security</a> 
</li><li> What is TLS/SSL <a href="https://technet.microsoft.com/en-us/library/cc784450(v=ws.10).aspx" class="external free" title="https://technet.microsoft.com/en-us/library/cc784450(v=ws.10).aspx" rel="nofollow">https://technet.microsoft.com/en-us/library/cc784450(v=ws.10).aspx</a>
</li><li> Wikipedia: Cipher suite <a href="https://en.wikipedia.org/wiki/Cipher_suite" class="external free" title="https://en.wikipedia.org/wiki/Cipher_suite" rel="nofollow">https://en.wikipedia.org/wiki/Cipher_suite</a>
</li><li> Security/Server Side TLS <a href="https://wiki.mozilla.org/Security/Server_Side_TLS" class="external free" title="https://wiki.mozilla.org/Security/Server_Side_TLS" rel="nofollow">https://wiki.mozilla.org/Security/Server_Side_TLS</a>
</li><li> How do I disable SSLv3 in tomcat? <a href="http://askubuntu.com/questions/537293/how-do-i-disable-sslv3-in-tomcat" class="external free" title="http://askubuntu.com/questions/537293/how-do-i-disable-sslv3-in-tomcat" rel="nofollow">http://askubuntu.com/questions/537293/how-do-i-disable-sslv3-in-tomcat</a>
</li><li> Check Cipher Suites Supported by Your Browser <a href="https://cc.dcsec.uni-hannover.de/" class="external free" title="https://cc.dcsec.uni-hannover.de/" rel="nofollow">https://cc.dcsec.uni-hannover.de/</a>
</li><li> <b>The ciphers are described here</b>: <a href="http://www.openssl.org/docs/apps/ciphers.html" class="external free" title="http://www.openssl.org/docs/apps/ciphers.html" rel="nofollow">http://www.openssl.org/docs/apps/ciphers.html</a>
</li></ul>
<ul><li> Google Chrome Help Forum：Server has a weak ephemeral Diffie-Hellman public <a href="https://productforums.google.com/forum/#!topic/chrome/o3vZD-Mg2Ic" class="external free" title="https://productforums.google.com/forum/#!topic/chrome/o3vZD-Mg2Ic" rel="nofollow">https://productforums.google.com/forum/#!topic/chrome/o3vZD-Mg2Ic</a>
</li><li> Apache Tomcat 6.0 Configuration Reference <a href="https://tomcat.apache.org/tomcat-6.0-doc/config/http.html" class="external free" title="https://tomcat.apache.org/tomcat-6.0-doc/config/http.html" rel="nofollow">https://tomcat.apache.org/tomcat-6.0-doc/config/http.html</a>
</li><li> Apache Tomcat 7.0 Configuration Reference <a href="https://tomcat.apache.org/tomcat-7.0-doc/config/http.html" class="external free" title="https://tomcat.apache.org/tomcat-7.0-doc/config/http.html" rel="nofollow">https://tomcat.apache.org/tomcat-7.0-doc/config/http.html</a>
</li><li> How do I list the SSL/TLS cipher suites a particular website offers? <a href="http://superuser.com/questions/109213/how-do-i-list-the-ssl-tls-cipher-suites-a-particular-website-offers" class="external free" title="http://superuser.com/questions/109213/how-do-i-list-the-ssl-tls-cipher-suites-a-particular-website-offers" rel="nofollow">http://superuser.com/questions/109213/how-do-i-list-the-ssl-tls-cipher-suites-a-particular-website-offers</a>
</li></ul>
<ul><li> How to Use IETester <a href="http://www.wikihow.com/Use-IETester" class="external free" title="http://www.wikihow.com/Use-IETester" rel="nofollow">http://www.wikihow.com/Use-IETester</a>
</li><li> List of User Agent Strings <a href="http://www.useragentstring.com/pages/useragentstring.php" class="external free" title="http://www.useragentstring.com/pages/useragentstring.php" rel="nofollow">http://www.useragentstring.com/pages/useragentstring.php</a>
</li></ul>

##<a id=Docker>Docker Homepage</a>
<div id="bodyContent">
<table id="toc" class="toc" summary="Contents"><tbody><tr><td><div id="toctitle">
</div>
<ul>
<li class="toclevel-1"><a href="#Research_Environment"><span class="tocnumber">1</span> <span class="toctext">Research Environment</span></a></li>
<li class="toclevel-1"><a href="#Docker_Introduction"><span class="tocnumber">2</span> <span class="toctext">Docker Introduction</span></a></li>
<li class="toclevel-1"><a href="#Docker_Installation"><span class="tocnumber">3</span> <span class="toctext">Docker Installation</span></a></li>
<li class="toclevel-1"><a href="#Docker_Commands"><span class="tocnumber">4</span> <span class="toctext">Docker Commands</span></a></li>
<li class="toclevel-1"><a href="#Docker_.26_ACDP"><span class="tocnumber">5</span> <span class="toctext">Docker &amp; ACDP</span></a></li>
<li class="toclevel-1"><a href="#Docker_Reference"><span class="tocnumber">6</span> <span class="toctext">Docker Reference</span></a></li>
</ul>
</td></tr></tbody></table><script type="text/javascript"> if (window.showTocToggle) { var tocShowText = "show"; var tocHideText = "hide"; showTocToggle(); } </script>
<a name="Research_Environment" id="Research_Environment"></a><h2> <span class="mw-headline"> Research Environment </span></h2>
<p><b>Ubuntu System</b>: 10.32.119.131(test:123456, root:123456) 
</p>
<pre># ssh test@10.32.119.131
# cd ~&nbsp;; mkdir yourname&nbsp;; cd yourname
# take your test here
</pre>
<a name="Docker_Introduction" id="Docker_Introduction"></a><h2> <span class="mw-headline"> Docker Introduction </span></h2>
<p><b>Overview</b> 
</p>
<ul><li> Wikipedia: Docker <a href="https://en.wikipedia.org/wiki/Docker_(software)" class="external free" title="https://en.wikipedia.org/wiki/Docker_(software)" rel="nofollow">https://en.wikipedia.org/wiki/Docker_(software)</a>
</li><li> What is docker <a href="https://www.docker.com/what-docker" class="external free" title="https://www.docker.com/what-docker" rel="nofollow">https://www.docker.com/what-docker</a>
</li><li> Get Started with Docker for Linux  <a href="http://docs.docker.com/linux/started/" class="external free" title="http://docs.docker.com/linux/started/" rel="nofollow">http://docs.docker.com/linux/started/</a>
</li><li> Docker Docs&nbsp;: <a href="https://docs.docker.com/" class="external free" title="https://docs.docker.com/" rel="nofollow">https://docs.docker.com/</a>
</li></ul>
<p><br>
<b>Component</b>
</p>
<ul><li> Images
</li><li> Container
</li><li> Repository
</li></ul>
<a name="Docker_Installation" id="Docker_Installation"></a><h2> <span class="mw-headline"> Docker Installation </span></h2>
<pre>$ sudo apt-get install apt-transport-https
$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
$ sudo bash -c "echo deb https://get.docker.io/ubuntu docker main &gt; /etc/apt/sources.list.d/docker.list"
$ sudo apt-get update
$ sudo apt-get install lxc-docker
</pre>
<p>Reference: 
</p>
<ul><li> Install docker on Ubuntu: <a href="http://docs.docker.com/engine/installation/ubuntulinux/" class="external free" title="http://docs.docker.com/engine/installation/ubuntulinux/" rel="nofollow">http://docs.docker.com/engine/installation/ubuntulinux/</a>
</li></ul>
<a name="Docker_Commands" id="Docker_Commands"></a><h2> <span class="mw-headline"> Docker Commands </span></h2>
<ul><li> <b>docker push [OPTIONS] NAME[:TAG]</b> # Push an image or a repository to the registry
</li><li> <b>docker pull [OPTIONS] NAME[:TAG|@DIGEST]</b> # Pull an image or a repository from the registry. -a Download all tagged images in the repository
</li><li> <b>docker save [OPTIONS] IMAGE [IMAGE...]</b> # Save an image(s) to a image snapshot tar file (streamed to STDOUT by default), -o file
</li><li> <b>docker load [OPTIONS]</b> # Load an image from a image snapshot tar file on STDIN,  -i a tar file
</li><li> <b>docker export [OPTIONS] CONTAINER</b> # Export a filesystem as a container snapshot tar file (streamed to STDOUT by default), -o file
</li><li> <b>docker import [OPTIONS] URL|- [REPOSITORY[:TAG]]</b> # Create an empty filesystem image and import the contents of the container snapshot tar file (.tar, .tar.gz, .tgz, .bzip, .tar.xz, .txz) into it, then optionally tag it. -c, Apply Dockerfile instruction to the created image
</li></ul>
<pre> Note: all of 'docker load' and 'docker import' could get a image, the diff of 'docker load' and 'docker import' is&nbsp;:
 1. Container snapshot file will lost all history records and metadata info, only store current status of container.
 2. Image snapshot file will store entire records and info, and larger than container snapshot file
 3. We could re-tag when 'docker import'
</pre>
<p><br>
</p><p>Reference:
</p>
<ul><li> Docker run reference&nbsp;: <a href="https://docs.docker.com/engine/reference/run/" class="external free" title="https://docs.docker.com/engine/reference/run/" rel="nofollow">https://docs.docker.com/engine/reference/run/</a>
</li></ul>
<a name="Docker_.26_ACDP" id="Docker_.26_ACDP"></a><h2> <span class="mw-headline"> Docker &amp; ACDP </span></h2>
<p>Step 1: Run 8 SLES12 hosts
</p><p>Step 2: Create 4 kinds of docker images based on RHEL5.5 for sysmgmt node, metering node, auth node, access node respectively
</p><p>Step 3: Push images to private repository and pull to respective host from it
</p>
<pre>## Kill the docker deamon, and Open another session to restart it by&nbsp;: sudo docker -d --insecure-registry 10.32.118.242:5000
# docker tag cdp/access:0.2 10.32.118.242:5000/cdp-access
# docker push 10.32.118.242:5000/cdp-access
#
## switch to target host
# docker pull 10.32.118.242:5000/cdp-access
</pre>
<p>Step 4: Build the map between docker container ports and host ports
</p><p>Step 5: Run a docker images on a host respectively
</p>
<pre># docker run -idt ImageID /bin/bash
# nsenter -m -u -i -n -p -t `docker inspect --format {{.State.Pid}} &lt;containeid&gt;`
</pre>
<a name="Docker_Reference" id="Docker_Reference"></a><h2> <span class="mw-headline"> Docker Reference </span></h2>
<ul><li>Simple Docker Introduction (In Chinese): <a href="https://yeasy.gitbooks.io/docker_practice/content/introduction/what.html" class="external free" title="https://yeasy.gitbooks.io/docker_practice/content/introduction/what.html" rel="nofollow">https://yeasy.gitbooks.io/docker_practice/content/introduction/what.html</a>
</li><li>Simple Docker Tutorial (In Chinese): <a href="http://www.simapple.com/docker-tutorial" class="external free" title="http://www.simapple.com/docker-tutorial" rel="nofollow">http://www.simapple.com/docker-tutorial</a>
</li><li>Difference between save and export in Docker&nbsp;: <a href="http://tuhrig.de/difference-between-save-and-export-in-docker/" class="external free" title="http://tuhrig.de/difference-between-save-and-export-in-docker/" rel="nofollow">http://tuhrig.de/difference-between-save-and-export-in-docker/</a>
</li></ul>
