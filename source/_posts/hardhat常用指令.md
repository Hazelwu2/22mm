---
title: hardhat常用指令
date: 2022-03-10 22:47:57
tags:
- hardhat
category:
- Solidity
- Hardhat
---
介紹 Hardhat 常用指令

``` bash
mkdir hardhat && cd hardhat
npm init --yes
npm install --save-dev hardhat
# 初始化 hardhat
npx hardhat 
```
``` bash
mkdir hardhat && cd hardhat
npm init --yes
npm install hardhat --save-dev
npx hardhat
```

``` bash
# 編譯合約
npx hardhat compile
# 開啟節點
npx hardhat node
# 節點運作 smple-script.js，將合約部署到 localhost
npx hardhat run scripts/sample-script.js
# 合約部署到 rinkeby
npx hardhat run scripts/sample-script.js --network rinkeby
# 合約部署到 ropsten
npx hardhat run scripts/sample-script.js --network ropsten
```

## hardhat config
### 安裝 dotenv
安裝後可以讀取環境變數，像是 process.env.INFURA_PROJECT_ID，就會讀取 .env 檔內的 INFURA_PROJECT_ID
```
npm i dotenv --save
```

### 建立 .env.example、.env
在 .env 填上 Infura 專案的 ID、Secret、錢包註記詞
由於我是用 Ganache 本地開發，因此填上 Ganache給的註記詞
```
INFURA_PROJECT_ID=
INFURA_PROJECT_SECRET=
mnemonic=
```
由於是敏感資訊，在 .gitignore 要加上 .env，如果是用 hardhat 預設安裝的流程，他會自動幫你加上。加上後 .env 就不會被 git 追蹤了

.gitignore
```
node_modules
.env
coverage
coverage.json
typechain

#Hardhat files
cache
artifacts
```


## 設定 hardhat 測試網路
開啟 hardhat-config.js，設定常用網路 localhost, rinkeby, ropsten, mainnet
- 如果是用 Ganache 作為 localhost server，locahost url 要設定 http://127.0.0.1:8545
- 如果是用 hardhat 內建 `npx hardhat node`，locahost url  要設定 http://127.0.0.1:8545

``` js
require("@nomiclabs/hardhat-waffle");
require("dotenv").config();

task("accounts", "Prints the list of accounts", async (taskArgs, hre) => {
  const accounts = await hre.ethers.getSigners();

  for (const account of accounts) {
    console.log(account.address);
  }
});


module.exports = {
  defaultNetwork: 'localhost',
  networks: {
    localhost: {
      // Ganache Port
      url: "http://127.0.0.1:7545"
      // hardhat 節點，Port為 8545
      // url: "http://127.0.0.1:7545"
    },
    // 測試網路 Rinkeby
    rinkeby: {
      url: `https://rinkeby.infura.io/v3/${process.env.INFURA_PROJECT_ID}`,
      // 帳戶私鑰；如果是硬體錢包地址，欄位值請設為 remote
      accounts: [process.env.privateKey1],
      chainId: 4,
    },
    // 測試網路 Ropsten
    ropsten: {
      url: `https://ropsten.infura.io/v3/${process.env.INFURA_PROJECT_ID}`,
      accounts: { mnemonic: process.env.mnemonic, },
    },
    // 主網
    mainnet: {
      url: `https://mainnet.infura.io/v3/${process.env.INFURA_PROJECT_ID}`,
      // gasPrice: mainnetGwei * 1000000000,
      // accounts: { mnemonic: mnemonic(), },
    },
  },
  solidity: "0.8.10"
};
```

## 更改合約
更改後合約，需要重新編譯 compile，再重新部署，`--network`參數改成你要部署的網路
```
npx hardhat compile
npx hardhat run scripts/sample-script.js --network localhost
```

## 坑

### 出現錯誤訊息 TypeError: state.buffer is not iterable
```
for (const chunk of state.buffer) {
                            ^
TypeError: state.buffer is not iterable
    at consumeStart (/Users/hazel/vhost/side-project/kcrypto-camp/HomeworkSubmission2/homeworks/week4/todo-list/node_modules/undici/lib/api/readable.js:211:29)
    at processTicksAndRejections (internal/process/task_queues.js:77:11)
```
解決方式：用nvm切換最新 nodejs 版本為 16，再重新 deploy 即可成功
```
nvm install 16.13.2
nvm use 16.13.2
npx hardhat run scripts/sample-script.js
```

### VSCode 報錯 hardhat/console.sol
```
Source "hardhat/console.sol" not found: File import callback not supported
```
解決方式：修改路徑
```
// 修改前
import "hardhat/console.sol";
// 修改後
import "../node_modules/hardhat/console.sol";

```