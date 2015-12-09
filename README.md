# saltvault.py

```saltvault``` is a very simple Python script for encrypting and decrypting files.


## Usage

```    saltvault input_file output_file```


Note that the Python 3 version of libnacl is required. On Ubuntu and its
derivatives, this can be installed with pip3:

```
# apt-get install python3-pip
# pip3 install libnacl
```


# Crypto

```saltvault``` uses the SecretBox format via [libnacl](https://github.com/saltstack/libnacl).
All data is therefore encrypted using the authenticated stream cipher ```XSalsa20Poly1305```.

A random key and nonce, generated via the library's utilities, are used
to encrypt user data.


## Key Derivation & Protection

Another nonce is generated and used for salt. As a key derivation function,
2^17 iterations of SHA-512 are performed on the salt and a user-provided password.
The SHA-256 of the result is used for a protection key.

The salt-nonce and protection key are used to create a SecretBox for the randomly-
generated nonce and key used to encrypt the input file.


## File contents

Lengths given total 122 bytes of overhead.

```
10: Identifier (ASCII 'SALTVAULT0')

24: Dual-purpose salt for KDF and nonce for header SecretBox

SecretBox
{
    16: MAC of encrypted contents
    24: [data nonce]
    32: [data key]
}

SecretBox
{
    16: MAC of encrypted contents
     *: [file contents]
}
```
