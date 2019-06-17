# AEX 9

```
AEX: 9
Title: Fungible Token Standard
Author: Milen Radkov (@mradkov), Philipp Piwowarsky (@thepiwo)
License: BSD-3-Clause
Discussions-To: https://forum.aeternity.com/t/aex-9-fungible-token/3565
Status: Draft
Type: Standards Track
Created: 2019-05-11
```

## Simple Summary

This document aims to outline a standard and define how fungible tokens should be created and used on aeternity blockchain.

## Abstract

The following standard allows for the implementation of a standard API for tokens within smart contracts. This standard provides basic functionality to transfer tokens, as well as allow tokens to be approved so they can be spent by another on-chain third party.


## Motivation

This standard will allow decentralized applications and wallets to handle tokens across multiple interfaces.

The most important functions here are `balance` and `transfer` and the `Transfer` event.

A standard interface allows any tokens to be re-used by other applications: from wallets to decentralized exchanges.

- The following specification is following the ERC20 standart introduced in Ethereum for fungible tokens.
- This standart is proven to be working and we think we can leverage of using it. It will also help us with interoperability and easier developers onboarding as the main differences will be Sophia syntax related.

## Specification

## Token
### Methods

NOTES:

#### Meta info

```public function meta_info() : meta_info```

- Function returning the meta information of the token saved as a record with the following structure:

```
record meta_info =
    {
    // The name of the token e.g. "MyToken"
    name     : string

    // The symbol of the token e.g. "TKN"
    , symbol   : string 

    // Number of decimals the token uses - e.g. 8 means to divide the token amount by 100000000 to get its user representation
    , decimals : int }
```

OPTIONAL - This method is optional and can be used for usability improvements. Interfaces such as wallets, exchanges and other software MUST NOT expect this function and values to be present. 

#### Total supply

```public function total_supply() : int``` - Returns the total token supply.

#### Balance

```public function balance(owner: adderess) : option(int)``` - Returns optional the account balance of another account with address `owner`.

#### Transfer

```
public stateful function transfer(to_account: address, value: int)
```

`transfer` - Transfers `value` amount of tokens to `to_account` address, and MUST fire the `Transfer` event. The function SHOULD abort if the spender `Call.caller`'s account balance does not have enough tokens to spend.

Note: Transfers of 0 values MUST be treated as normal transfers and fire the Transfer event.

#### Transfer allowance

```public stateful function transfer_allowance(from_account: address, to_account: address, value: int)```
Transfers `value` amount of tokens from address `from_account` to address `to_account`, and MUST fire the `Transfer` event.

The `transfer_from` method is used for a withdraw workflow, allowing contracts to transfer tokens on your behalf. This can be used for example to allow a contract to transfer tokens on your behalf and/or to charge fees in sub-currencies. The function SHOULD throw unless the `from` account has deliberately authorized the sender of the message via some mechanism.

Note Transfers of 0 values MUST be treated as normal transfers and fire the Transfer event.

#### Create allowance

```public stateful function create_allowance(for_account: address, value: int)```
Allows `for_account` address to withdraw from your account (`Call.caller`) multiple times, up to the `value` amount. If this function is called again it overwrites the current allowance with `value`.

NOTE: To prevent attack vectors (like the ones possible in ERC20) clients SHOULD make sure to create user interfaces in such a way that they set the allowance first to 0 before setting it to another value for the same spender. THOUGH The contract itself shouldn’t enforce it, to allow backwards compatibility with contracts deployed before

#### Change allowance

```public stateful function change_allowance(for_account: address, value: int)```
Increases the amount which `for_account` is still allowed to withdraw from `Call.caller`.

Note: Passing of negative value can be used for decreasing the allowance.


### Events

#### Transfer


```
Transfer(indexed address, indexed address, indexed int)
```
The transfer event arguments should be as follows: `(spender, receiver, value)`

The event MUST be triggered when tokens are transferred, including zero value transfers.


#### Approval

```
Allowance(indexed address, indexed address, indexed int)
```

The approval event arguments should be as follows: `(spender, receiver, value)`

The event MUST be triggered on any successful contract call to `create_allowance`.


### Implementation
There are several implementations available at the moment, but they lack a thing or two (that is why this standart is being proposed).

Example implementations:
- [Aeternity Sophia Fungible Token - with events](https://github.com/mradkov/aeternity-fungible-token/blob/master/contracts/fungible-token.aes)
- [Fungible token implementation example](https://github.com/aeternity/aepp-sophia-examples/blob/master/libraries/FungibleToken/contracts/fungible-token.aes)

### References
[ERC-20](https://eips.ethereum.org/EIPS/eip-20)
[ERC-20 attack vectors](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)


