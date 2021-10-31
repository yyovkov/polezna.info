# Gnu Privacy Guard

This article is intenting to demonstate few simple use cases of GPG.

## What is GnuPG

* [Official website](https://gnupg.org/index.html)

For the goal of this document, the GnuPG could be refered as encryption software based on Private and Public key pair.

Public key is the part of the pair that should be shared with the world.

Private key is the part of the pair, that only the owner of the key should have access to. 

In short the private key is the person identity in the digital world. Private key is protected by password. The password should be strong and known only by the owner of the key.

Any encrypted documents could only be read by the person possesing the private key. I.e. the private key should be protected and it is recommended spare copy of it to be store on non-electronical media.

## Generate key pair

``` bash
gpg --full-generate-key
```

## Backup Keys

* List keys

``` bash
_$ gpg --list-keys 
gpg: checking the trustdb
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
/Users/yyovkov/.gnupg/pubring.kbx
---------------------------------
pub   ed25519 2021-10-31 [SC]
      483274EEC6547D8FC4BC534AB4BA5BAEE8DE54B1
uid           [ultimate] Yovko Yovkov (Personal) <yyovkov@yyovkov.net>
sub   cv25519 2021-10-31 [E]
```

* Export public key

``` bash
gpg --armor --output yyovkov.pub.key --export yyovkov@yyovkov.net
```

* Export private key in text format

To identify the key id, look at the `gpg --list-keys` command output above. In
 that case the `KEY_ID` is `483274EEC6547D8FC4BC534AB4BA5BAEE8DE54B1`.

``` bash
KEY_ID=<replace_with_your_key_id>
gpg --export-secret-key $KEY_ID | paperkey --output yyovkov.priv.key
```

* Print both keys on a paper

``` bash
lpr yyovkov.pub.key
lpr yyovkov.priv.key
```

## Restore keys

In case of incident leaving to loss of the digital copy of the private key, that procudure will help restoring the key.

* Dearmor public key

The dearmored key will be stored in the `yyovkov.pub.key.gpg`

``` bash
gpg --dearmor yyovkov.pub.key
```

* Import keys

``` bash
paperkey --pubring yyovkov.pub.key.gpg --secrets yyovkov.priv.key | gpg --import
```

## Import Public Key

In order to encrypt file (or data at all), that to be read only by another person, first that another person's public key must be added to encryptor's keyring

The public key could be obtained from the public keyservers and confirmed by the person by sending key fingerprint, or received by the email (or other data exchange).

``` bash
gpg --import yassen.gpg
```

## Trust Public Key

As the public key is added to the keyring, the next step is to verify it and trust it. Verification is another offline taks, that includes communicating with the owner (posessor) of the private key, asking him to share/confirm the key finger print. Once confirmed the fingerprint, then the public key may be trysted in your keyring, so not to ask confirmation every time encryption procedure is issued.

* List the keys in the keyring

``` bash
gpg --list-keys
```

* Select the key to be trusted

``` bash
gpg --edit-key ${PUBKEY_ID}
```

## Encrypt Document

Documents are encrypted with the public key of the reciever (mentioned above as another person). This way the reciver (the posessor of the reciever's private key) is the only person that can decrypt the document.

``` bash
gpg --encrypt --recipient yassen@gmail.com mydocument.pdf
```

The output file will be `mydocument.pdf.gpg`.

That newly generated file can be sent over unencrypted wire (email, shared on a public cloud and so on) and no-one (excluding the possesor of the private key) could read the content of the file.

