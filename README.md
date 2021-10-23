# Ethereum Smart Contracts
- My notes for the [Solidity Docs](https://docs.soliditylang.org/en/latest/introduction-to-smart-contracts.html)
- Creating smart contracts on the Ethereum blockchain
- Using Solidity (a programming language)
- Can create contracts for voting, crowdfunding, blind auctions, and multi-signature wallets using it

## A Simple Smart Contract
- Sets the value of a variable and exposes it for other contracts to access
- A contract is a collection of code (a class?)
  - its functions
  - and data (state)
```solidity
// SPDX-License-Identifier: GPL-3.0
// ^- a machine readable license specifier
// tells the machine that the source code is licensed under the
// GPL version 3.0
pragma solidity >=0.4.16 <0.9.0;
// ^- specifies that the source code is written for v0.4.16
// and will work up to the language up to 0.9.0

contract SimpleStorage {
    // declare a state variable as an 256 bit unsigned integer
    uint storedData;

    // a setter to change the state
    function set(uint x) public {
        // don't use this (like in javascript)!
        // it has a different meaning in solidity
        storedData = x;
    }

    // a getter to get the state
    function get() public view returns (uint) {
        return storedData;
    }
}
```
- This contract allows anyone to store a single number
  - Anyone could set it with a different number and overwrite my number

### Subcurrency Example
- Implements a simple cryptocurrency
- Allows only its creator to create nw coins
- Anyone can send coins to each other without a need for registering with  username and password
  - All you need is an Ethereum keypair

```solidity
// SPDX-License-Identifier; GPL-3.0
pragma solidity ^0.8.4;

contract Coin {
    // makes this variable accessible from other contracts
    address public minter;
    mapping (address => uint) public balances;

    // events allow clients to react to specific contract changes you declare
    event Sent(address from, address to, uint amount);

    // constructor code is only run when the contract is created
    constructor() {
        minter = msg.sender;
    }

    // sends an amount of newly generated tokens to an address
    // can only be used by the creator
    function mint(address receiver, uint amount) public {
        require(msg.sender == minter);
        balances[receiver] += amount;
    }

    // errors allow you to provide inormation
    // about why an operation failed
    // they are returned to the caller of the function
    error InsufficientBalance(uint requested, uint available);

    // Sends an amount of existing coins from the caller to an address
    function send(address receiver, uint amount) public {
        if (amount > balances[msg.sender])
            revert InsufficientBalance({
                requested: amount,
                available: balances[msg.sender]
            })
        balances[msg.sender] -= amount;
        balances[receiver] += amount;
        emit Sent(msg.sender, receiver, amount);
    }

}

```