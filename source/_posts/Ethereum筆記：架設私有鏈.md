---
title: Ethereum筆記：用 Geth 架設私有鏈
date: 2022-02-20 01:47:51
tags:
category:
- Ethereum
---

開發筆記記錄如何架設私有區塊鏈鏈在自己電腦上，並成功完成一筆交易。會透過 Geth 來達成

## 安裝環境
安裝 Geth
``` bash
brew tap ethereum/ethereum
brew install ethereum
```
建立專案資料夾
```
mkdir ~/Desktop/private-chain
```

## 建立私有鏈
建立私有鏈需要具備兩個條件
- networkid：可自行定義 networkid 是多少
- Genesis：定義創世區塊 #0 也就是初始帳本的資料

其他節點連上私有鏈，必須設定相同的 networkid、genesis 檔案，表示初始共識一致，就能夠連上了

### 建立創世區塊 genesis
建立 genesis.json檔，會用此檔案來建立創世區塊，唯一一個是我們自己決定的區塊

genesis.json
``` json
{
  "config": {
    "chainID": 123456,
    "homesteadBlock": 0,
    "eip150Block": 0,
    "eip155Block": 0,
    "eip158Block": 0
  },
  "nonce": "0x0000000000000042",
  "difficulty": "0x020",
  "mixhash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "coinbase": "0x0000000000000000000000000000000000000000",
  "timestamp": "0x00",
  "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "extraData": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "gasLimit": "0x2fefd8",
  "alloc": {
    "0x0000000000000000000000000000000000000001": {
      "balance": "11111111111"
    },
    "0x0000000000000000000000000000000000000002": {
      "balance": "22222222222"
    }
  }
}
```
- chainID：networkid，我設定為 123456，可以設定任何你喜歡的數字，要避開主鏈、知名鏈。當 network、chainID、創世區塊配置都相同，才算是同一條鏈
- nonce：挖礦的 64bits 隨機數，與 PoW 機制有關的值
- difficulty：每次挖礦時最終的 nonce 難度
- parentHash：指定了本區塊的上一個區塊Hash，因此創世區塊的 parentHash 是 0
- alloc：從創世區塊預置帳號及其帳號內的金額，單位是 wei

### 執行私有鏈
先初始化，輸入以下指令便會建立創世區塊
```
$ geth --datadir data init genesis.json
```
初始化後，接著啟用私有鏈
```
# 第一個指令會報錯
$ geth --datadir data --networkid 123456 console
# 請執行下列指令，才可成功啟用
$ geth --datadir data --networkid 123456 --nodiscover --ipcdisable console
```

#### 錯誤：IPC opening failed
啟用私有鏈，報錯 IPC opening failed 
``` bash
$ geth --datadir data --networkid 123456 console
```

``` log
INFO [02-19|23:20:32.701] Maximum peer count                       ETH=50 LES=0 total=50
INFO [02-19|23:20:32.703] Set global gas cap                       cap=50,000,000
INFO [02-19|23:20:32.703] Allocated cache and file handles         database=/Users/hazel/vhost/side-project/kcrypto-camp/HomeworkSubmission2/homeworks/week1/myPrivateChain/data/geth/chaindata cache=16.00MiB handles=16
INFO [02-19|23:20:32.834] Writing custom genesis block
INFO [02-19|23:20:32.835] Persisted trie from memory database      nodes=3 size=401.00B time="255.18µs" gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=0.00B
INFO [02-19|23:20:32.836] Successfully wrote genesis state         database=chaindata hash=d9807d..d072c3
INFO [02-19|23:20:32.836] Allocated cache and file handles         database=/Users/hazel/vhost/side-project/kcrypto-camp/HomeworkSubmission2/homeworks/week1/myPrivateChain/data/geth/lightchaindata cache=16.00MiB handles=16
INFO [02-19|23:20:32.950] Writing custom genesis block
INFO [02-19|23:20:32.951] Persisted trie from memory database      nodes=3 size=401.00B time="191.938µs" gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=0.00B
INFO [02-19|23:20:32.951] Successfully wrote genesis state         database=lightchaindata hash=d9807d..d072c3
 ~/vhost/side-project/kcrypto-camp/HomeworkSubmission2/homeworks/week1/myPrivateChain   C0021601 ±  geth --datadir data --networkid 123456 console
INFO [02-19|23:20:42.269] Maximum peer count                       ETH=50 LES=0 total=50
INFO [02-19|23:20:42.270] Set global gas cap                       cap=50,000,000
INFO [02-19|23:20:42.270] Allocated trie memory caches             clean=154.00MiB dirty=256.00MiB
INFO [02-19|23:20:42.270] Allocated cache and file handles         database=/Users/hazel/vhost/side-project/kcrypto-camp/HomeworkSubmission2/homeworks/week1/myPrivateChain/data/geth/chaindata cache=512.00MiB handles=5120
INFO [02-19|23:20:42.512] Opened ancient database                  database=/Users/hazel/vhost/side-project/kcrypto-camp/HomeworkSubmission2/homeworks/week1/myPrivateChain/data/geth/chaindata/ancient readonly=false
INFO [02-19|23:20:42.514] Initialised chain configuration          config="{ChainID: 123456 Homestead: 0 DAO: <nil> DAOSupport: false EIP150: 0 EIP155: 0 EIP158: 0 Byzantium: <nil> Constantinople: <nil> Petersburg: <nil> Istanbul: <nil>, Muir Glacier: <nil>, Berlin: <nil>, London: <nil>, Arrow Glacier: <nil>, MergeFork: <nil>, Engine: unknown}"
INFO [02-19|23:20:42.514] Disk storage enabled for ethash caches   dir=/Users/hazel/vhost/side-project/kcrypto-camp/HomeworkSubmission2/homeworks/week1/myPrivateChain/data/geth/ethash count=3
INFO [02-19|23:20:42.514] Disk storage enabled for ethash DAGs     dir=/Users/hazel/Library/Ethash count=2
INFO [02-19|23:20:42.515] Initialising Ethereum protocol           network=123,456 dbversion=<nil>
INFO [02-19|23:20:42.516] Loaded most recent local header          number=0 hash=d9807d..d072c3 td=32 age=52y10mo3w
INFO [02-19|23:20:42.516] Loaded most recent local full block      number=0 hash=d9807d..d072c3 td=32 age=52y10mo3w
INFO [02-19|23:20:42.516] Loaded most recent local fast block      number=0 hash=d9807d..d072c3 td=32 age=52y10mo3w
WARN [02-19|23:20:42.516] Failed to load snapshot, regenerating    err="missing or corrupted snapshot"
INFO [02-19|23:20:42.516] Rebuilding state snapshot
INFO [02-19|23:20:42.516] Resuming state snapshot generation       root=46cc17..421f39 accounts=0 slots=0 storage=0.00B elapsed="285.796µs"
INFO [02-19|23:20:42.517] Regenerated local transaction journal    transactions=0 accounts=0
INFO [02-19|23:20:42.517] Generated state snapshot                 accounts=2 slots=0 storage=86.00B elapsed="712.137µs"
INFO [02-19|23:20:42.517] Gasprice oracle is ignoring threshold set threshold=2
WARN [02-19|23:20:42.517] Error reading unclean shutdown markers   error="leveldb: not found"
INFO [02-19|23:20:42.517] Starting peer-to-peer node               instance=Geth/v1.10.16-stable/darwin-amd64/go1.17.6
INFO [02-19|23:20:42.665] New local node record                    seq=1,645,284,042,664 id=25770860a7262285 ip=127.0.0.1 udp=30303 tcp=30303
INFO [02-19|23:20:42.666] Started P2P networking                   self=enode://8ca221f45f0d6318755dc92489048f45cab4486360ce8e09b404c7ed9373b489e8412eea580ac3978fe923718fb11ce6653862e456c2deb503cbb6612c6bd00e@127.0.0.1:30303
WARN [02-19|23:20:42.667] The ipc endpoint is longer than 104 characters.  endpoint=/Users/hazel/vhost/side-project/kcrypto-camp/HomeworkSubmission2/homeworks/week1/myPrivateChain/data/geth.ipc
WARN [02-19|23:20:42.667] IPC opening failed                       url=/Users/hazel/vhost/side-project/kcrypto-camp/HomeworkSubmission2/homeworks/week1/myPrivateChain/data/geth.ipc error="listen unix /Users/hazel/vhost/side-project/kcrypto-camp/HomeworkSubmission2/homeworks/week1/myPrivateChain/data/geth.ipc: bind: invalid argument"
Fatal: Error starting protocol stack: listen unix /Users/hazel/vhost/side-project/kcrypto-camp/HomeworkSubmission2/homeworks/week1/myPrivateChain/data/geth.ipc: bind: invalid argument
```

#### Fix Error 解決方式
IPC Disabled，即可成功執行，會進入 Javascript console 命令介面
```
geth --datadir data --networkid 123456 --nodiscover --ipcdisable console
```

## 帳號
在私有鏈進行走跳，首先要先要一個帳戶，進入 Javascript 交互環境，並開始下指令
1. 建立帳號 `> personal.newAccount("123456")`
要建兩個帳號，一個轉錢，一個接收，再創一個帳號 `> personal.newAccount(123123)`
passphrase 密碼分別是 123456、123123

``` log
INFO [02-19|23:30:27.042] Your new key was generated               address=0x70CFd5976e1E9ccEeC8C1dd95548E7bcE0142e9f
WARN [02-19|23:30:27.042] Please backup your key file!             path=/Users/hazel/vhost/side-project/kcrypto-camp/HomeworkSubmission2/homeworks/week1/myPrivateChain/data/keystore/UTC--2022-02-19T15-30-25.609833000Z--70cfd5976e1e9cceec8c1dd95548e7bce0142e9f
WARN [02-19|23:30:27.042] Please remember your password!
"0x70cfd5976e1e9cceec8c1dd95548e7bce0142e9f"
```

帳號 Address: `0x70cfd5976e1e9cceec8c1dd95548e7bce0142e9f`

``` bash
# 查看所有帳號
> eth.accounts # ["0x70cfd5976e1e9cceec8c1dd95548e7bce0142e9f"]
# 查看礦工帳號，系統預設礦工帳號會是第一個帳號
> eth.coinbase # ["0x70cfd5976e1e9cceec8c1dd95548e7bce0142e9f"]
# 查看帳戶有多少 eth
> eth.getBalance(eth.accounts[0]) # 0
```

做任何交易都需要 passphrase 來解鎖帳號，為了保障帳戶的安全性
``` js
personal.unlockAccount(eth.accounts[0])
```

需要獲得一些 eth 來執行交易，完成轉帳動作，所以要先來工作，也就是挖礦，取得 eth後才能轉帳
``` bash
# 查看帳戶有多少 eth
> eth.getBalance(eth.accounts[0]) # 0
# 挖礦
> miner.start(1)
```
挖礦可以用 ctrl+D / cmd+D 終止挖礦 或是輸入 `miner.stop()`



## 轉帳

和銀行一樣，轉帳需要有發送方、接收方、轉帳金額

- 發送方：eth.accounts[0]
- 接收方：eth.accounts[1]
- 金額：web3.toWei(7, 'ether')，送七顆以太幣給接收方

發送方轉帳前需要先解鎖帳戶 `personal.unlockAccount(eth.accounts[0])`
``` bash
> eth.accounts # 確認是否有兩個帳戶
["0x70cfd5976e1e9cceec8c1dd95548e7bce0142e9f", "0x2a486302c7f28bbc876265118ddc76e9c395fd60"]
> amount = web3.toWei(7, 'ether') # 定義 amount，發送 7 顆eth
> fromAccount = eth.accounts[0] # 定義發送方
> toAccount = eth.accounts[0] # 定義接收方

> personal.unlockAccount(eth.accounts[0]) # 交易前先解鎖帳戶
Unlock account 0x70cfd5976e1e9cceec8c1dd95548e7bce0142e9f
Passphrase:
true
> eth.sendTransaction({from: fromAccount, to: toAccount, value: amount}) # 開始交易
INFO [02-20|01:10:33.106] Setting new local account                address=0x70CFd5976e1E9ccEeC8C1dd95548E7bcE0142e9f
INFO [02-20|01:10:33.106] Submitted transaction                    hash=0x92c0d0a83ea3ec49241ba360b51110bd905652bd60d9a05c3150917f1a78af45 from=0x70CFd5976e1E9ccEeC8C1dd95548E7bcE0142e9f nonce=0 recipient=0x2a486302C7F28Bbc876265118dDC76e9c395fD60 value=7,000,000,000,000,000,000
"0x92c0d0a83ea3ec49241ba360b51110bd905652bd60d9a05c3150917f1a78af45"
```

## 確認交易
交易似乎成功，查看接收方餘額
```
> eth.getBalance(toAccount)
0
```
發現餘額仍是 0，表示轉帳沒有成功，可以透過 txpool 查看交易內存池來驗證 `txpool.status`
``` js
txpool.status
{
  pending: 1,
  queued: 0
}
```
我們需要將挖礦打開，這樣新的交易才能在新的挖礦區塊裡被確認

``` bash
> miner.start(1)
# 挖完區塊後停止
> miner.stop()
# 確認餘額
> eth.getBalance(toAccount)
7000000000000000000
>  web3.fromWei(eth.getBalance(toAccount), 'ether')
7
```
可以看到接收方帳號拿到 7 eth了


## 指令
私有鏈
``` bash
# init 初始化
$ geth --datadir data init genesis.json
# 啟用
$ geth --datadir data --networkid 123456 --nodiscover --ipcdisable console
```

Console 環境
``` bash
# 列出所有帳戶
> eth.accounts
# 取得帳戶餘額
> eth.getBalance(eth.accounts[0])
> web3.fromWei(eth.getBalance(toAccount), 'ether')
# 建立帳戶
> personal.newAccount(123456)
# 查看礦工帳號
> eth.coinbase
# 解鎖帳戶
> personal.unlockAccount(eth.accounts[0])
# 開始挖礦
> miner.start(1)
# 停止挖礦
> miner.stop()
# 確認交易池狀態
> txpool.status
# 轉帳
> fromAccount = eth.accounts[0]
> toAccount = eth.accounts[1]
> amount = web3.toWei(7, 'ether')
> eth.sendTransaction({ from: fromAccount, to: toAccount, value: amount })
# 更改礦工帳號，挖礦得到的錢會轉到對應的帳號
> miner.setEtherbase(eth.accounts[1])
# 查看區塊數量
> eth.blockNumber
# 查看 Node 此節點是否有在挖礦
> eth.mining
```

## 結語
還蠻有趣的，透過 Geth 建立私有鏈，也使用了許多 Geth 的基礎用法，做了建立帳戶、查詢餘額、進行交易前先解鎖帳戶、挖礦、停止挖礦

## 參考連結
- [Ethereum 開發筆記 2–2：Geth 基礎用法及架設 Muti-Nodes 私有鏈](https://blog.fukuball.com/ethereum-%E9%96%8B%E7%99%BC%E7%AD%86%E8%A8%98-22geth-%E5%9F%BA%E7%A4%8E%E7%94%A8%E6%B3%95%E5%8F%8A%E6%9E%B6%E8%A8%AD-muti-nodes-%E7%A7%81%E6%9C%89%E9%8F%88/)
- [KryptoCamp](https://chihaolu.gitbook.io/kryptocamp-office-hour/season-1-2022-1/oh-week1-overview)