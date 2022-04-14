---
title: Dapp Wagmi串接
date: 2022-04-01 07:52:01
tags:
category:
---

用 Wagmi React Hook Libirary 快速串接
首先在專案上安裝 wagmi
```
yarn add wagmi
```



## 設定 Provider
Dapp最基礎串接，第一步驟是設定 Provider，Provider設定哪個網路

由於我的合約發布在 Rinkby 上，因此 ID 填寫 4
在 .env 環境變數上新增 REACT_APP_INFURA_ID，並在 infura 申請 Project 取得 ID，填入 .env

index.js
``` js
import React from 'react';
import App from './App';
import { Provider as WagmiProvider } from 'wagmi'
import { providers } from "ethers";

const provider = ({ chainId }) =>
  new providers.InfuraProvider(4, process.env.REACT_APP_INFURA_ID)

ReactDOM.render(
  <React.StrictMode>
    <WagmiProvider provider={provider}>
      <App />
    </WagmiProvider>
  </React.StrictMode>,
  document.getElementById('root')
);

```
若沒設定 Provider 會出現錯誤訊息

`Erroe: call revert exception`
如果你的合約發布在測試網路上，由於沒設定 Provider，預設是 Mainet 主網，但主網抓不到你的合約，便會噴錯


## 設定合約
接著新增 config.js 設定你的合約地址及 ABI，有幾種方式取得
- Remix 編譯完後切換到 compile 頁籤，下面有 ABI複製按鈕
- Hardhat 或 Truffle 部署合約後也會產生資料夾、json，裡面有 abo

要在 config.js 定義合約地址、合約ABI
``` js
export const CONTACT_ADDRESS = '0xf26D71977568065d935a866d19822b56A0Ebe7e0'

export const CONTACT_ABI = [
  {
    "inputs": [],
    "stateMutability": "nonpayable",
    "type": "constructor"
  },
  {
    "inputs": [
      {
        "internalType": "uint256",
        "name": "",
        "type": "uint256"
      }
    ],
    "name": "contacts",
    "outputs": [
      {
        "internalType": "uint256",
        "name": "id",
        "type": "uint256"
      },
      {
        "internalType": "string",
        "name": "name",
        "type": "string"
      },
      {
        "internalType": "string",
        "name": "phone",
        "type": "string"
      }
    ],
    "stateMutability": "view",
    "type": "function",
    "constant": true
  },
  {
    "inputs": [],
    "name": "count",
    "outputs": [
      {
        "internalType": "uint256",
        "name": "",
        "type": "uint256"
      }
    ],
    "stateMutability": "view",
    "type": "function",
    "constant": true
  },
  {
    "inputs": [
      {
        "internalType": "string",
        "name": "_name",
        "type": "string"
      },
      {
        "internalType": "string",
        "name": "_phone",
        "type": "string"
      }
    ],
    "name": "createContact",
    "outputs": [],
    "stateMutability": "nonpayable",
    "type": "function"
  }
]
```

## 連接錢包
使用到 wagmi useConnect 方法，這邊先不需要連接合約
``` js
const [{ data, error }, connect] = useConnect()
```
完整code

``` js
export const ConnectWallet = () => {
  const [{ data, error }, connect] = useConnect()

  return (
    <>
      {data.connectors.map((connector) => (
        <div className="col-sm-12" key={connector.id + Math.random()}>
          <div className="m-auto mb-2">
            <button
              className="btn btn-primary"
              disabled={!connector.ready}
              key={connector.id}
              onClick={() => connect(connector)}
            >
              連接錢包
              {!connector.ready && ' (unsupported)'}
            </button>
          </div>
        </div>
      ))}
      <div className="col-sm-12">
        {error && <div>{error?.message ?? 'Failed to connect'}</div>}
      </div>
    </>
  )
}
```


## 讀取合約
設定合約會使用到剛剛提到的 Provider, useContractRead 方法
也要把稍早設定好的合約 config.js 匯入進來

使用 useContractRead 時機點是 Solidity 定義方法是 public, view 時
``` js
import React, { useEffect } from "react";
import { lottery } from './contract/config.js'
import { useContractRead } from 'wagmi'

function App() {
  // 設定測試網路
  const provider = useProvider();
  const [{ }, getBalance] = useContractRead(
    {
      addressOrName: lottery.address,
      contractInterface: lottery.abi,
      signerOrProvider: provider,
    },
    'getBalance',
  )

  // 取得合約餘額
  const getBalanceValue = async () => {
    const { data, error } = await getBalance()
    // 返回的資料為 16進位 data._hex: "0x470de4df820000"
    // 用 formatEther 將 16進位轉為 10 進位
    const balance = ethers.utils.formatEther(data._hex)

    setBalance(balance);

    if (error) handleError(error)
  }

  useEffect(() => {
    // 取得合約餘額
    getBalanceValue()
  }, [])

}

```

## 寫入合約
寫資料到鏈上會用到 wagmi 的 useContractWrite

``` js
import React, { useEffect } from "react";
import { lottery } from './contract/config.js'
import { useContractWrite } from 'wagmi'

const [{ }, enter] = useContractWrite(
  {
    addressOrName: lottery.address,
    contractInterface: lottery.abi,
    signerOrProvider: provider,
  },
  'enter',
)

// ETH 0.01
const price = ethers.utils.parseUnits("0.01", 18);

const handleEnter = async () => {
  const { data, error } = await enter({
    args: [],
    overrides: {
      gasLimit: 203000,
      gasPrice: 60000000000,
      value: price,
    },
  });

  if (data && data.hash) {
    Swal.fire({
      icon: 'success',
      text: '等待交易執行中',
      title: '成功'
    })
  }

  if (error) handleError(error)
};

return (
  <div>
    <button onClick={handleEnter}>
      參加樂透
    </button>
  </div>
)
```

## 轉換 eth
```
const weiValue = 100000;
const ethValue = ethers.utils.formatEther(weiValue);
```
