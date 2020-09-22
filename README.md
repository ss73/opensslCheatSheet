# OpenSSL Cheat Sheet
OpenSSL is one of the most versatile tools there is when it comes to cryptography and the various associated storage formats. 
The CLI, however, is not very intuitive to say the least. This document is a collection of quick reference guides for performing
some of the most common operations that I have compiled from various resources online.

## Public key generation and inspection

### Generate an RSA key:

```bash
openssl genrsa -out example.key [optional size]
```

### Print public key or modulus:

```bash
openssl rsa -in example.key -pubout
openssl rsa -in example.key -noout -modulus
```

### Print textual representation of RSA key:

```bash
openssl rsa -in example.key -text -noout
```

### Generate and password-protect an RSA key:

Generate new RSA key and encrypt with a pass phrase based on AES CBC 256 encryption:

```bash
openssl genrsa -aes256 -out example.key [optional size]
```

### Check a private key. 

If the key has a pass phrase, you’ll be prompted for it:

```bash
openssl rsa -check -in example.key
```

### Remove passphrase from a key

```bash
openssl rsa -in example.key -out example.key
```

### Encrypt existing private key with a pass phrase

```bash
openssl rsa -des3 -in example.key -out example_with_pass.key
```
### Generate ECDSA private key

Generate a private ECDSA key in PEM format:

```bash
openssl ecparam -name secp384r1 -genkey -noout -out privatekey.pem
```

The `secp384r1` curve name can be replaced with any supported elliptic curve. In order to list the available curves:

```bash
openssl ecparam -list_curves
```

### Generate ECDSA public key

Using the previously generated private key to generate a public key counterpart:

```bash
openssl ec -in privatekey.pem -pubout -out publickey.pem
```

### Print ECDSA key

```bash
openssl ec -in privatekey.pem -text -noout
```

## Format conversions

### Converting cert and PEM private key to PFX

```bash
openssl pkcs12 -inkey privatekey.pem -in example.cert -export -out example.pfx
```

### Converting a HEX key to PEM format

Assuming it's an EC key, starting with a hex string, first it has to get decoded to get to the DER-encoding. The hex string should start with 30 which
indicates that it's an ASN.1 sequence. First, the `xxd` command will be used for the decoding, then piped to OpenSSL to parse the DER key:

If it is a public key:

```bash
cat key.hex | xxd -r -p - | openssl ec -inform der -pubin -pubout -out publickey.pem
```

If it is a private key:

```bash
cat key.hex | xxd -r -p - | openssl ec -inform der -out privatekey.pem
```
