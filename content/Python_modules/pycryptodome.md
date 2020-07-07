---
title: "pycryptodome"
date: 2020-06-15 21:57
---
[toc]





# pycryptodome



## install python3

```
pip install pycryptodome
```





# FAQ

## ModuleNotFoundError: No module named 'Crypto'

Rename the folder in site-packages from crypto to Crypto, below import error will dismiss

```
from Crypto.Cipher import AES
from Crypto.Hash import SHA256
```







# example

## Encrypt aes

```

from Crypto.Cipher import AES
import hashlib

password = b'qwerty12345'
key = hashlib.sha256(password).digest()
key = b'0123456789abcdef'

IV = 16 * '\x00'
mode = AES.MODE_CBC
encryptor = AES.new(key, mode, IV=IV)

text = b'j' * 32 + b'i' * 64
ciphertext = encryptor.encrypt(text)

decryptor = AES.new(key, mode, IV=IV)
plain = decryptor.decrypt(ciphertext)
print(plain)
```





## Encrypt file



```
import argparse
import os
import struct
import sys
import hashlib

from Crypto.Cipher import AES


def encrypt_file(key, in_filename, out_filename=None, chunksize=64*1024):
    ''' Encrypts a file using AES (CBC mode) with the
        given key.
        key:
            The encryption key - a bytes object that must be
            either 16, 24 or 32 bytes long. Longer keys
            are more secure.
        in_filename:
            Name of the input file
        out_filename:
            If None, '<in_filename>.enc' will be used.
        chunksize:
            Sets the size of the chunk which the function
            uses to read and encrypt the file. Larger chunk
            sizes can be faster for some files and machines.
            chunksize must be divisible by 16.
    '''
    if not out_filename:
        out_filename = in_filename + '.enc'

    iv = os.urandom(16)
    encrypt_engine = AES.new(key, AES.MODE_CBC, iv)
    file_size = os.path.getsize(in_filename)

    with open(in_filename, 'rb') as in_f:
        with open(out_filename, 'wb') as outfile:
            outfile.write(struct.pack('<Q', file_size))
            outfile.write(iv)

            while True:
                chunk = in_f.read(chunksize)
                if len(chunk) == 0:
                    break
                elif len(chunk) % 16 != 0:
                    chunk += b' ' * (16 - len(chunk) % 16)

                outfile.write(encrypt_engine.encrypt(chunk))
    return out_filename


def decrypt_file(key, in_filename, out_filename=None, chunksize=24*1024):
    ''' Decrypts a file using AES (CBC mode) with the
        given key. Parameters are similar to encrypt_file,
        with one difference: out_filename, if not supplied
        will be in_filename without its last extension
        (i.e. if in_filename is 'aaa.zip.enc' then
        out_filename will be 'aaa.zip')
    '''
    if not out_filename:
        out_filename = os.path.splitext(in_filename)[0]

    with open(in_filename, 'rb') as in_f:
        origin_size = struct.unpack('<Q', in_f.read(struct.calcsize('Q')))[0]
        iv = in_f.read(16)
        decryptor = AES.new(key, AES.MODE_CBC, iv)

        with open(out_filename, 'wb') as outfile:
            while True:
                chunk = in_f.read(chunksize)
                if len(chunk) == 0:
                    break
                outfile.write(decryptor.decrypt(chunk))

            outfile.truncate(origin_size)
    return out_filename


if __name__ == '__main__':
    parser = argparse.ArgumentParser(description='Encrypt/Decrypt AES')
    parser.add_argument('filename', nargs=1)
    parser.add_argument('-k', '--key', help='key value', required=True)
    parser.add_argument('-e', dest='encrypt', help='encrypt', action='store_true')
    parser.add_argument('-d', dest='decrypt', help='decrypt', action='store_true')
    args = parser.parse_args()
    infile = args.filename[0]

    key_string = args.key
    key_byte = key_string.encode('utf-8')
    input_key = hashlib.sha256(key_byte).digest()

    if args.encrypt:
        output_filename = encrypt_file(input_key, infile, out_filename=infile + '.enc')
        print('Encrypted to', output_filename)
    elif args.decrypt:
        output_filename = decrypt_file(input_key, infile, out_filename=infile.replace('.enc', ''))
        print('Decrypted to', output_filename)
    else:
        parser.print_help()
        sys.exit(1)
```

