---
title: "bitcoin_core"
date: 2021-03-19 19:00
---
[toc]



# Bitcoin Core Wallet









## Ubuntu GUI (please go to website for latest version)

```
wget https://bitcoin.org/bin/bitcoin-core-0.21.0/bitcoin-0.21.0-x86_64-linux-gnu.tar.gz
wget https://bitcoin.org/bin/bitcoin-core-0.21.0/SHA256SUMS.asc
tar -xf bitcoin-0.21.0-x86_64-linux-gnu.tar.gz 
```

 

If you use sudo to run commands as root, use the following command line:

```
sudo install -m 0755 -o root -g root -t /usr/local/bin bitcoin-0.21.0/bin/*
```


If you use su to run commands as root, use the following command line:

```
su -c 'install -m 0755 -o root -g root -t /usr/local/bin bitcoin-0.21.0/bin/*'
```



 to start Bitcoin Core GUI

```
/usr/local/bin/bitcoin-qt
```









## wallet.dat

钱包软件在保存wallet.dat的时候，实际上是用AES算法（高级加密标准，Advanced Encryption Standard）进行了加密处理，这样即使别人拿走了你的wallet.dat文件，没有密码也无法动用你的比特币。





根据以上道理，比特币丢失有这样几种情况：

* 私钥泄漏，被别人把币转移了。

* wallet.dat找不到了，彻底没办法了，因为私钥没有了。

* wallet.dat被偷。如果这个文件已经加密，那么小偷还需知道密码才能真正偷走你的钱，所以务必要启用钱包加密功能。

* 密码不记得了。你唯一的办法就是慢慢尝试吧，因为没有忘记密码的找回功能，只能把你以前用过的密码都试一遍。

* wallet.dat被破坏了。用Bitcoin Core提供的备份功能，一般没问题，但如果这个文件被病毒感染了，请找到以前备份到U盘里的正常文件。





## backup wallet

Bitcoin Core钱包的默认文件名是wallet.dat，用“文件”菜单里的“备份钱包”功能把它备份到2个U盘中，并把U盘分散保管在安全的地方，牢记密码。普通的U盘就行，有20 MB的剩余磁盘空间就足够。而且，一个钱包只需备份一次就行，以后没有特殊情况，甚至不用拿出来。



Bitcoin Core中用wallet.dat文件保存你的**私钥**，请启用软件的加密选项，要用至少8位以上的**复杂密码**（建议12个以上的大小写字母、数字和特殊字符组合），并牢记密码。



把加密的wallet.dat备份在U盘、移动硬盘等，多存放几份，牢记你的密码，如果更换电脑，则只需重装Bitcoin Core，打开wallet.dat，就可以恢复你的比特币。





## Accelerate Sync speed

**Give Higher CPU Priority to Bitcoin-Qt Process**

The above approach of blockchain import should be faster than syncing the whole blockchain block by block. Give the process higher CPU priority might further help speeding up the blockchain import especially you have more CPU cycle at your disposal. If you're using Mac OS X, and assuming the Process ID (PID) of Bitcoin-Qt is 2202, use the following command to give Bitcoin-Qt process hight CPU priority

```
sudo renice -20 -p 2202
```

>  -20 is the [nice value](http://www.thegeekstuff.com/2013/08/nice-renice-command-examples/) a value between -20 and +19. The lower the nice value, the higher priority the process gets.





## dump private key

go to console

```
walletpassphrase "passphrase" timeout(seconds)

e.g.
walletpassphrase "my pass phrase" 60
```



```
dumpprivkey "address"
```







# bitcoind

默认位置为`/usr/local/bin`

第一次运行需要创建配置文件

```
cat /data/.bitcoin/bitcon.conf
server=1
rpcuser=bit_user
rpcpassword=this_password_must_more_complexity
rpcport=8332
```



通过下述命令运行到后台

```
bitcoind -daemon
```





# bitcoin-cli

通过`bitcoin-cli` 实现JSON-RPC接口访问，需要确保bitcoind是在后台运行的



## -getinfo

```
$ bitcoin-cli -getinfo
{
  "version": 210000,
  "blocks": 0,
  "headers": 609999,
  "verificationprogress": 1.575419360338358e-09,
  "timeoffset": 0,
  "connections": {
    "in": 0,
    "out": 4,
    "total": 4
  },
  "proxy": "",
  "difficulty": 1,
  "chain": "main",
  "relayfee": 0.00001000,
  "warnings": ""
}
```



# RPC call



## bitcoin.conf

```
$ cat .bitcoin/bitcoin.conf
# Expose the RPC/JSON API
server=1
rpcbind=127.0.0.1
rpcallowip=0.0.0.0/0
rpcport=8332
rpcuser=rxu
rpcpassword=bitcoin_pass
txindex=1
```



```
curl --user rxu --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "getnetworkinfo", "params": [] }' -H 'content-type: text/plain;' http://127.0.0.1:8332/
```



## btc-rpc-explorer







## elextrum personal server

1. Download the 

    ```
    wget https://github.com/chris-belcher/electrum-personal-server/archive/refs/tags/eps-v0.2.1.1.tar.gz
    ```

    of Electrum Personal Server. (Not the Windows version, the "Source code" zip or tar.gz.)

2. Extract the compressed file

3. Enter the directory

4. `cp config.ini_sample config.ini`

5. Edit the config.ini file:

    1. Add bitcoind back-end RPC auth information
    2. Add wallet master public keys for your wallets

6. Install the server to your home directory with `pip3 install --user .`

7. Make sure `~/.local/bin` is in your $PATH (`echo $PATH`). If not, add it: `echo 'PATH=$HOME/.local/bin:$PATH' >> ~/.profile`, logout, and log in again

8. Run the server: `electrum-personal-server config.ini`

9. Rescan if needed: `electrum-personal-server --rescan config.ini`

10. Restart the server if needed

11. Start your Electrum wallet: `electrum --oneserver --server localhost:50002:s`.





# Appendix

https://github.com/janoside/btc-rpc-explorer

https://github.com/bitembassy/home-node/blob/master/README.md#bitcoin-core

https://stadicus.github.io/RaspiBolt/raspibolt_55_explorer.html

https://github.com/chris-belcher/electrum-personal-server

https://github.com/spesmilo/electrumx