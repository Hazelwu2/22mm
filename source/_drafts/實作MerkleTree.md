---
title: 實作 MerkleTree JS
date: 2022-03-10 22:47:57
tags:
- hardhat
category:
- Solidity
- Hardhat
---
https://github.com/miguelmota/merkletreejs-solidity
https://lab.miguelmota.com/merkletreejs/example/
// 前端
``` js
import MerkleTree from "merkletreejs";
import keccak256 from "keccak256";

this.whitelist = [
  "0x80DA2dF0D07DA601e2604af1AF1d63c9dA8399c4",
  "0x1A0d14C90b6DFC79c4538e6b9CcBf2452C19674B",
  "0xb910a94f17D35488c4028AABCDFDaCFd4aAF3E1F",
];

const leaves = this.whitelist.map((address) => keccak256(address));
this.tree = new MerkleTree(leaves, keccak256, { sortPairs: true });

handleBuyTokens = async () => {
  const leaf = keccak256(this.accounts[0]);
  const proof = this.tree.getHexProof(leaf);
  await this.tokenSaleInstance.methods
    .buyTokens(this.accounts[0], proof)
    .send({
      from: this.accounts[0],
      value: this.web3.utils.toWei(this.state.tokenAmount, "wei"),
    });
};
```

``` js
// Solidity
contract KycContract is Ownable {
  bytes32 public merkleRoot;

  function setMerkleRoot(bytes32 root) public onlyOwner {
    merkleRoot = root;
  }

  function verify(bytes32[] calldata proof, address sender)
      public
      view
      returns (bool)
  {
    return
          MerkleProof.verify(
            proof,
              merkleRoot,
              keccak256(abi.encodePacked(sender))
          );
  }
}
```