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



## Setup 

go to check eletrum installation



# Appendix

https://en.bitcoin.it/wiki/Cold_storage

https://electrum.readthedocs.io/en/latest/coldstorage.html

https://electrum.org/#download





