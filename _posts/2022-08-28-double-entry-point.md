---
title: Ethernaut \#26 DoubleEntryPoint
tags: [ethernaut]
style: border
color: primary
description: Ethernaut DoubleEntryPoint challenge writeup
---

## DoubleEntryPoint

### Challenge

This level features a `CryptoVault` with special functionality, the `sweepToken` function. This is a common function to retrieve tokens stuck in a contract. The `CryptoVault` operates with an `underlying` token that can't be swept, being it an important core's logic component of the `CryptoVault`, any other token can be swept.

The underlying token is an instance of the DET token implemented in `DoubleEntryPoint` contract definition and the `CryptoVault` holds 100 units of it. Additionally the `CryptoVault` also holds 100 of `LegacyToken LGT`.

In this level you should figure out where the bug is in `CryptoVault` and protect it from being drained out of tokens.

The contract features a `Forta` contract where any user can register its own `detection bot` contract. Forta is a decentralized, community-based monitoring network to detect threats and anomalies on DeFi, NFT, governance, bridges and other Web3 systems as quickly as possible. Your job is to implement a `detection bot` and register it in the `Forta` contract. The bot's implementation will need to raise correct alerts to prevent potential attacks or bug exploits.

Things that might help:

* How does a double entry point work for a token contract ?


```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

interface DelegateERC20 {
  function delegateTransfer(address to, uint256 value, address origSender) external returns (bool);
}

interface IDetectionBot {
    function handleTransaction(address user, bytes calldata msgData) external;
}

interface IForta {
    function setDetectionBot(address detectionBotAddress) external;
    function notify(address user, bytes calldata msgData) external;
    function raiseAlert(address user) external;
}

contract Forta is IForta {
  mapping(address => IDetectionBot) public usersDetectionBots;
  mapping(address => uint256) public botRaisedAlerts;

  function setDetectionBot(address detectionBotAddress) external override {
      require(address(usersDetectionBots[msg.sender]) == address(0), "DetectionBot already set");
      usersDetectionBots[msg.sender] = IDetectionBot(detectionBotAddress);
  }

  function notify(address user, bytes calldata msgData) external override {
    if(address(usersDetectionBots[user]) == address(0)) return;
    try usersDetectionBots[user].handleTransaction(user, msgData) {
        return;
    } catch {}
  }

  function raiseAlert(address user) external override {
      if(address(usersDetectionBots[user]) != msg.sender) return;
      botRaisedAlerts[msg.sender] += 1;
  } 
}

contract CryptoVault {
    address public sweptTokensRecipient;
    IERC20 public underlying;

    constructor(address recipient) public {
        sweptTokensRecipient = recipient;
    }

    function setUnderlying(address latestToken) public {
        require(address(underlying) == address(0), "Already set");
        underlying = IERC20(latestToken);
    }

    /*
    ...
    */

    function sweepToken(IERC20 token) public {
        require(token != underlying, "Can't transfer underlying token");
        token.transfer(sweptTokensRecipient, token.balanceOf(address(this)));
    }
}

contract LegacyToken is ERC20("LegacyToken", "LGT"), Ownable {
    DelegateERC20 public delegate;

    function mint(address to, uint256 amount) public onlyOwner {
        _mint(to, amount);
    }

    function delegateToNewContract(DelegateERC20 newContract) public onlyOwner {
        delegate = newContract;
    }

    function transfer(address to, uint256 value) public override returns (bool) {
        if (address(delegate) == address(0)) {
            return super.transfer(to, value);
        } else {
            return delegate.delegateTransfer(to, value, msg.sender);
        }
    }
}

contract DoubleEntryPoint is ERC20("DoubleEntryPointToken", "DET"), DelegateERC20, Ownable {
    address public cryptoVault;
    address public player;
    address public delegatedFrom;
    Forta public forta;

    constructor(address legacyToken, address vaultAddress, address fortaAddress, address playerAddress) public {
        delegatedFrom = legacyToken;
        forta = Forta(fortaAddress);
        player = playerAddress;
        cryptoVault = vaultAddress;
        _mint(cryptoVault, 100 ether);
    }

    modifier onlyDelegateFrom() {
        require(msg.sender == delegatedFrom, "Not legacy contract");
        _;
    }

    modifier fortaNotify() {
        address detectionBot = address(forta.usersDetectionBots(player));

        // Cache old number of bot alerts
        uint256 previousValue = forta.botRaisedAlerts(detectionBot);

        // Notify Forta
        forta.notify(player, msg.data);

        // Continue execution
        _;

        // Check if alarms have been raised
        if(forta.botRaisedAlerts(detectionBot) > previousValue) revert("Alert has been triggered, reverting");
    }

    function delegateTransfer(
        address to,
        uint256 value,
        address origSender
    ) public override onlyDelegateFrom fortaNotify returns (bool) {
        _transfer(origSender, to, value);
        return true;
    }
}
```

### Background

This challenge description confused me a lot. I didn't really understand what was asked and were to look so I started reading through the code which gave me a better understanding, but still, I was confused as to how things were used. I think not having the deployment script was part of what made it confusing to me. So after spending some time reading through the code I searched online for `token contract double entry point` as suggested by the challenge description and avoided any spoilers (which came up over the actual result I was looking for) and found an [openzeppelin article](https://blog.openzeppelin.com/compound-tusd-integration-issue-retrospective/) regarding such an incidence.

So what the issue is here multistep so lets break it down

The first part is the upgrade mechanism which was implemeneted for the `LegacyToken` contract to the `DoubleEntryPoint` contract was by delegating calls from the old to the new contract. As a result, any calls to the old contract will be delegated to the new contract.

The second part is the way the `sweepToken` works. This function exists so that the contract owner can take out tokens that remained in the contract without any actual purpose and this could be called by anyone. This function however should not be called for the core coin of the contract. To achieve this the coin address to be swept was checked.

The third part is irrelevant for the challenge, but in the real scenario, if the core coin was swept it would have radical changes on the contract functionality which would allow an attacked to abuse the contract.

Each of the three parts are not vulnerable per se. The combination of the three parts is what makes the code vulnerable. An attacker could abuse the fact that both the `LegacyToken` and `DoubleEntryPoint` contracts, can be used to interact with the core coin, and bypass the filtering of the core coin in the `sweepToken` by calling it with the non-filtered address. Finally, even though the coins are not transfered to the attacker, just the fact that they are out of the contract is enough to manipulate the correctness of the contract.

In order to solve this, a `Forta` bot should be written that will detect this abuse.

### Attack

In order to attack this contract, one would simply need to provide the address of the old token to the `sweepToken` function since the one used currently is the new one and that would be enough to drain it.

### Defence

In order to defend against an attack as the one described above, a `Forta` bot should be written that will detect such abuse by checking if the `CryptoVault` originated the transfer using the `delegateTransfer`. This should never be the case, since `CryptoVault` should be using the new token contract. By looking at where the code will be called and what arguments are provided to the bot, we can see that we have access to the `msg.data` of the `delegateTransfer` call. By decoding the data we get the `to`, `value`, `origSender` arguments as well as the function selector. The only thing we need to do is check if the `origSender` is the `CryptoVault` address which we can provide in the contstuctor and if so, raise an alert on the `Forta` contract using `raiseAlert`.

* Create a contract takes the `CryptoVault` address and implements the `IDetectionBot` interface (impements `handleTransaction`).
  * The function `handleTransaction` should decode the `calldata`, get the third argument (excluding the function selector) `origSender`
  * Check if the `origSender` is equal to the `CryptoVault` and if so call the `raiseAlert` on the `msg.sender` using the `IForta` interface
* Submit the bot to the `Forta` contract using `setDetectionBot`

#### DoubleEntryPointSolve.sol

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.4;

import '../challenges/DoubleEntryPoint.sol';

contract DoubleEntryPointSolve is IDetectionBot {
    
    address cryptoVault;

    constructor(address _cryptoVault) public {
        cryptoVault = _cryptoVault;
    }

    function handleTransaction(address user, bytes calldata msgData) override external {
        address to;
        uint256 value;
        address origSender;

        (to, value, origSender) = abi.decode(msgData[4:], (address, uint256, address));

        if (origSender == cryptoVault) {
            IForta forta = IForta(msg.sender);
            forta.raiseAlert(user);
        }
    }

}
```

I'm also adding a slightly more advanced version of the IDetectionBot that will also make sure that the specific function (`delegateTransfer`) was called

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.4;

import '../challenges/DoubleEntryPoint.sol';

contract DoubleEntryPointSolve is IDetectionBot {
    
    address cryptoVault;

    constructor(address _cryptoVault) public {
        cryptoVault = _cryptoVault;
    }

    function getSelector(string memory _func) public pure returns (bytes4) {
        return bytes4(keccak256(bytes(_func)));
    }

    function toBytes4(bytes calldata data) public pure returns (bytes4) {
        uint32 val;
        val = uint8(data[0]);
        val = val << 8;
        val += uint8(data[1]);
        val = val << 8;
        val += uint8(data[2]);
        val = val << 8;
        val += uint8(data[3]);
        return bytes4(val);
    }

    function handleTransaction(address user, bytes calldata msgData) override external {
        // this check is not really needed for the challange
        // the check is added so that it will only run the code for the delegateTransfer function
        // if this check is removed we can also remove the getSelector and toBytes functions
        if (toBytes4(msgData[:4]) == getSelector("delegateTransfer(address,uint256,address)")) {
            address to;
            uint256 value;
            address origSender;

            (to, value, origSender) = abi.decode(msgData[4:], (address, uint256, address));

            if (origSender == cryptoVault) {
                IForta forta = IForta(msg.sender);
                forta.raiseAlert(user);
            }
        }
    }

}
```

#### double-entry-point-solve.js

```javascript
const hre = require("hardhat");
const { ethers, upgrades } = require("hardhat");

async function main() {
    doubleEntryPointAddress = "0x27DE0628694955AF7e5a8E3aAA5c2AEa6f8AefaB";
    contract = await ethers.getContractAt("DoubleEntryPoint", doubleEntryPointAddress);

    const accounts = await hre.ethers.getSigners();
    account = accounts[0];

    cryptoVaultAddress = await contract.cryptoVault()

    DoubleEntryPointSolve = await ethers.getContractFactory("DoubleEntryPointSolve");
    doubleEntryPointSolve = await DoubleEntryPointSolve.deploy(cryptoVaultAddress);
    await doubleEntryPointSolve.deployed();

    console.log("Level address: " + contract.address)

    // get forta
    fortaAddress = await contract.forta()
    forta = await ethers.getContractAt("IForta", fortaAddress);

    // set detection bot on forta
    tx = await forta.connect(account).setDetectionBot(doubleEntryPointSolve.address)
    receipt = await tx.wait()

}

main().catch((error) => {
    console.error(error);
    process.exitCode = 1;
});
```

```bash
$ npx hardhat run scripts/double-entry-point-solve.js --network rinkeby
Level address: 0x27DE0628694955AF7e5a8E3aAA5c2AEa6f8AefaB
```

### Success Message

Congratulations!

This is the first experience you have with a [Forta bot](https://docs.forta.network/en/latest/).

Forta comprises a decentralized network of independent node operators who scan all transactions and block-by-block state changes for outlier transactions and threats. When an issue is detected, node operators send alerts to subscribers of potential risks, which enables them to take action.

The presented example is just for educational purpose since Forta bot is not modeled into smart contracts. In Forta, a bot is a code script to detect specific conditions or events, but when an alert is emitted it does not trigger automatic actions - at least not yet. In this level, the bot's alert effectively trigger a revert in the transaction, deviating from the intended Forta's bot design.

Detection bots heavily depends on contract's final implementations and some might be upgradeable and break bot's integrations, but to mitigate that you can even create a specific bot to look for contract upgrades and react to it. Learn how to do it [here](https://docs.forta.network/en/latest/quickstart/).

You have also passed through a recent security issue that has been uncovered during OpenZeppelin's latest [collaboration with Compound protocol](https://compound.finance/governance/proposals/76).

Having tokens that present a double entry point is a non-trivial pattern that might affect many protocols. This is because it is commonly assumed to have one contract per token. But it was not the case this time :) You can read the entire details of what happened [here](https://blog.openzeppelin.com/compound-tusd-integration-issue-retrospective/).
