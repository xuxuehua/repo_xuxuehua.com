---
title: "cold_storage"
date: 2021-03-12 19:02
---
[toc]



# Cold Storage

**Cold storage** in the context of Bitcoin refers to [storing Bitcoins](https://en.bitcoin.it/wiki/Storing_bitcoins) offline and spending without the [private keys](https://en.bitcoin.it/wiki/Private_key) controlling them ever being online. This resists theft by hackers and malware, and is often a necessary security precaution especially dealing with large amounts of Bitcoin.



# Conceptual How-to

1. Set up an online computer which has an internet connection, and an offline computer which is securely [airgapped](https://en.wikipedia.org/wiki/Air_gap_(networking)).
2. The offline computer must have bitcoin wallet software installed. Use the software to generate a wallet and write down its [seed phrase](https://en.bitcoin.it/wiki/Seed_phrase) on paper or another medium.
3. Obtain the [master public key](https://en.bitcoin.it/wiki/Deterministic_wallet#Master_public_key) of the wallet you just generated and transfer it to the online computer. Use it to create a watch-only wallet on the online computer.
4. The watch-only wallet on the online computer can provide bitcoin [addresses](https://en.bitcoin.it/wiki/Address) used for receiving money, and can tell the user when [transactions](https://en.bitcoin.it/wiki/Transaction) are received and how many [confirmations](https://en.bitcoin.it/wiki/Confirmation) they have.
5. For spending have the watch-only wallet create an [transaction](https://en.bitcoin.it/wiki/Transaction) without the signatures which makes it valid.
6. Transfer the unsigned transaction to the offline computer and use the wallet software to sign the transaction.
7. Transfer the now-fully-signed transaction to the online computer and broadcast it to the bitcoin network. The watch-only wallet will tell you when the transaction has [confirmations](https://en.bitcoin.it/wiki/Confirmation).



## Transferring data between offline and online



### USB drive

The [SecureDrop](https://en.wikipedia.org/wiki/SecureDrop) platform for securely leaking documents to journalists also uses USB drives for secure communication.





## Electrum (maybe outdate, follow official website)

Linux	Install dependencies:	

```
sudo apt-get install python3-pyqt5 libsecp256k1-0 python3-cryptography -y
```

Download package:	

```
wget https://download.electrum.org/4.0.9/Electrum-4.0.9.tar.gz
```

Verify signature:	

```
wget https://download.electrum.org/4.0.9/Electrum-4.0.9.tar.gz.asc
gpg --keyserver keys.gnupg.net --recv-keys 6694D8DE7BE8EE5631BED9502BD5824B7F9470E6
gpg --verify Electrum-4.0.9.tar.gz.asc
```

```
$ gpg --verify Electrum-4.0.9.tar.gz.asc Electrum-4.0.9.tar.gz
gpg: Signature made Sat 19 Dec 2020 04:07:21 AM JST
gpg:                using RSA key 6694D8DE7BE8EE5631BED9502BD5824B7F9470E6
gpg: Good signature from "Thomas Voegtlin (https://electrum.org) <thomasv@electrum.org>" [unknown]
gpg:                 aka "ThomasV <thomasv1@gmx.de>" [unknown]
gpg:                 aka "Thomas Voegtlin <thomasv1@gmx.de>" [unknown]
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: 6694 D8DE 7BE8 EE56 31BE  D950 2BD5 824B 7F94 70E6
```

> You can ignore this:
>
> WARNING: This key is not certified with a trusted signature!
> gpg:          There is no indication that the signature belongs to the owner.
> as it simply means you have not established a web of trust with other GPG users



Run without installing:	

```
tar -xvf Electrum-4.0.9.tar.gz
python3 Electrum-4.0.9/run_electrum
```

Install with PIP:	

```
sudo apt-get install python3-setuptools python3-pip
python3 -m pip install --user Electrum-4.0.9.tar.gz
```









# Appendix

https://en.bitcoin.it/wiki/Cold_storage

https://electrum.readthedocs.io/en/latest/coldstorage.html

https://electrum.org/#download





