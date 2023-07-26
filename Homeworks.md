
---

# Homeworks 1

1. **Why is client diversity important for Ethereum?**

Client diversity is important for Ethereum to ensure the robustness and security of the network. Ethereum clients implement the Ethereum protocol, allowing nodes to join the network, process transactions, and maintain the blockchain. Client diversity means having multiple implementations of the Ethereum protocol (like Geth, OpenEthereum, Besu, etc.) in different programming languages. 

This diversity is critical for several reasons. First, it reduces the risk of network-wide failures due to bugs in a single client. If there's a bug in one client, nodes running different clients can continue operating and maintaining the network. Second, it can prevent centralization around a single client, preserving Ethereum's decentralized nature. Lastly, it encourages innovation, as different client teams might propose different solutions for enhancing Ethereum's performance and functionality.

2. **Where is the full Ethereum state held?**

The full Ethereum state is held in each full node on the Ethereum network. The Ethereum state represents the current information of all accounts in the system, including both externally owned accounts (normal addresses) and contract accounts. Each full node maintains a version of this state, and it's updated with every block according to the block's transactions.

3. **What is a replay attack? Which 2 pieces of information can prevent it?**

A replay attack happens when a valid data transmission is maliciously or fraudulently repeated or delayed. In the context of Ethereum, a replay attack could occur when a transaction meant for one Ethereum chain (like Ethereum Classic) is replayed on another (like Ethereum). This could potentially lead to unintended loss of funds.

To prevent replay attacks, Ethereum introduced two pieces of information into each transaction: chainID and replay protection. ChainID was introduced during the Ethereum and Ethereum Classic fork. Each network has a unique chainID, and transactions from one chain are considered invalid on the other. This measure helps to prevent replay attacks between chains. The other piece of information, replay protection, uses increasing nonce values for each transaction from an address. Transactions with lower nonce values are considered invalid and are rejected by the network.

4. **In a contract, how do we know who called a view function?**

In Solidity, the keyword `msg.sender` is used within a function to access the address of the entity (externally owned account or another contract) that called the function. However, for `view` functions which do not modify the state and are often used to read data, it's important to note that when they are called off-chain (like from a web3.js script, not part of a transaction), `msg.sender` would be meaningless since there's no real sender of the call. In these cases, it would return the zero address. 

But if a `view` function is called within the execution of a transaction (i.e., it's called by another function that is part of a transaction), `msg.sender` would correctly return the address of the account that initiated the transaction or the contract that made the call.

---

# Homeworks 2

1. **What are the advantages and disadvantages of the 256-bit word length in the EVM?**

Advantages of 256-bit word length in EVM:

- Security: 256-bit word length is a standard for cryptographic operations. This is large enough to secure against brute force attacks even with the most powerful computers.
  
- Compatibility: Ethereum addresses and cryptographic functions like SHA-3 hashes use 256 bits, thus EVM’s 256-bit word length aligns with these.

- Consistency: The EVM uses 256-bit words consistently throughout its operation, reducing the complexity of working with multiple word lengths.

Disadvantages of 256-bit word length in EVM:

- Inefficiency for smaller types: For operations on smaller data types (like 8-bit integers), 256-bit word length can be overkill and lead to inefficiency in storage and computation.
  
- Complexity: 256-bit arithmetic is more complex than smaller sizes, and many general-purpose CPUs don't natively support 256-bit operations. This means operations on the EVM can be slower and consume more gas than they would with smaller word sizes.

2. **What would happen if the implementation of a precompiled contract varied between Ethereum clients?**

If the implementation of a precompiled contract varied between Ethereum clients, it could lead to a consensus failure or a network split (also known as a hard fork). This is because precompiled contracts, like all contracts, must be executed identically across all nodes. If different nodes interpret the same contract in different ways, they will produce different results and therefore different states. In turn, this could lead to a disagreement about the valid state of the blockchain, splitting the network into separate chains. 

3. **Using Remix write a simple contract that uses a memory variable, then using the debugger step through the function and inspect the memory.**

Here's a simple Solidity contract:

```solidity
pragma solidity ^0.8.6;

contract SimpleContract {
    function add(uint a, uint b) public pure returns (uint) {
        uint result = a + b;
        return result;
    }
}
```

You can follow these steps to use the Remix debugger:

- Paste the contract into the Remix editor.
- Compile the contract using the "Solidity Compiler" plugin.
- Deploy the contract using the "Deploy & run transactions" plugin.
- Call the `add` function with some inputs.
- After the function executes, go to the "Debugger" tab.
- Click "Start debugging".
- As you step through the execution, you can inspect the memory, storage, and stack.

Remember that memory in Solidity is volatile and is wiped clean between external function calls. For this reason, the `add` function in the example above does not affect the contract's state.

---

# Homeworks 3

Sure, one gas-efficient way to delete an item from an array in Solidity without leaving a gap is to move the last element of the array to the index of the element to be deleted, and then decrease the length of the array by 1. Note that this method does not preserve the original order of the array. If order matters, this method is not appropriate, and you'd need to shift all elements down, which is more gas-expensive.

Here's a Solidity contract that includes this function:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.6;

contract ArrayDelete {
    uint[] public data;

    // function to populate the array for example
    function fillData(uint _size) public {
        delete data;
        for(uint i=0; i<_size; i++) {
            data.push(i);
        }
    }
    
    function deleteAtIndex(uint index) public {
        require(index < data.length, "index out of bounds");
        
        // Move the last element to the index to be deleted
        data[index] = data[data.length - 1];

        // Remove the last element
        data.pop();
    }

    // helper function to get the array content
    function getData() public view returns(uint[] memory) {
        return data;
    }
}
```
In the example above, the `deleteAtIndex` function deletes an element from the `data` array at the specified index. First, it checks to ensure the index is within the bounds of the array. Then, it moves the last element of the array to the index of the element to be deleted. Finally, it removes the last element of the array, which is now a duplicate, using the `pop` function.

Remember to populate the array first by calling `fillData` and passing the size of the array you want. After that, you can call `deleteAtIndex` passing the index you want to delete. You can inspect the array at any time by calling `getData` to confirm the operation worked as expected.

---
