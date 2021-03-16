---
title: "private_key"
date: 2021-01-31 11:59
---
[toc]



# Private key

a private key for Bitcoin (and many other cryptocurrencies) is a series of 32 bytes. Now, there are many ways to record these bytes. It can be a string of 256 ones and zeros (32 * 8 = 256) or 100 dice rolls. It can be a binary string, Base64 string, a [WIF key](https://en.bitcoin.it/wiki/Wallet_import_format), [mnemonic phrase](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki), or finally, a hex string. For our purposes, we will use a 64 character long hex string.

![image-20210131120247323](private_key.assets/image-20210131120247323.png)



## 32 bytes

Bitcoin uses the **ECDSA**, or Elliptic Curve Digital Signature Algorithm. More specifically, it uses one particular curve called **secp256k1**.

Now, this curve has an order of 256 bits, takes 256 bits as input, and outputs 256-bit integers. And 256 bits is exactly 32 bytes. So, to put it another way, we need 32 bytes of data to feed to this curve algorithm.

There is an additional requirement for the private key. Because we use ECDSA, the key should be positive and should be less than the order of the curve. The order of secp256k1 is `FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEBAAEDCE6AF48A03BBFD25E8CD0364141`, which is pretty big: almost any 32-byte number will be smaller than it.





## Cryptographically strong RNG

Along with a standard RNG (random number generation) 

This method is usually much more secure, because it draws entropy straight from the operating system. The result of such RNG is much harder to reproduce. You can’t do it by knowing the time of generation or having the seed, because there is no seed. Well, at least the user doesn’t enter a seed — rather, it’s created by the program.

In Python, cryptographically strong RNG is implemented in the `secrets` module. Let’s modify the code above to make the private key generation secure

```
import secrets

bits = secrets.randbits(256)
print(bits)
bits_hex = hex(bits)
print(bits_hex)

private_key = bits_hex[2:]
print(private_key)

>>>
9138976318288522481132697828447841686925836552785867534050609495529084110128
0x14347a2fb534005b30c132f8fffa2d7da6a4fc02e9439efcf04403e715874530
14347a2fb534005b30c132f8fffa2d7da6a4fc02e9439efcf04403e715874530
```



Below is insecure method which can be attacked by brute-force with a few variants 

```
import random

bits = random.getrandbits(256)
print(bits)
bits_hex = hex(bits)
print(bits_hex)

private_key = bits_hex[2:]
print(private_key)

>>>
65358812825735084032636193620386220831461791295226259059545162764499623487988
0x907fc6f15933b0a265a83eb9ee4c199ded80267815f69034e885aad953de29f4
907fc6f15933b0a265a83eb9ee4c199ded80267815f69034e885aad953de29f4
```





## public key

```
import codecs
import ecdsa as ecdsa

private_key = '60cf347dbc59d31c1358c8e5cf5e45b822ab85b79cb32a9f3d98184779a9efc2'

private_key_bytes = codecs.decode(private_key, 'hex')
key = ecdsa.SigningKey.from_string(private_key_bytes, curve=ecdsa.SECP256k1).verifying_key
key_bytes = key.to_string()
key_hex = codecs.encode(key_bytes, 'hex')
bitcoin_byte = b'04'
public_key = bitcoin_byte + key_hex
print(public_key)

>>>
b'041e7bcc70c72770dbb72fea022e8a6d07f814d2ebe4de9ae3f7af75bf706902a7b73ff919898c836396a6b0c96812c3213b99372050853bd1678da0ead14487d7'
```





## convert to address

```
b'041e7bcc70c72770dbb72fea022e8a6d07f814d2ebe4de9ae3f7af75bf706902a7b73ff919898c836396a6b0c96812c3213b99372050853bd1678da0ead14487d7'
```



```

```



# Appendix

https://www.freecodecamp.org/news/how-to-generate-your-very-own-bitcoin-private-key-7ad0f4936e6c/



