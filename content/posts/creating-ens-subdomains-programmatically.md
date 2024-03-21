+++
authors = ["vibern"]
title = "📗 Creating ENS subdomains programatically"
date = "2024-03-21"
description = "How to create a subdomain generatooorr!"
tags = [
    "ens",
    "ethereum",
    "name",
    "service",
    "domains",
    "subdomains",
]
categories = [
    "web3",
    "smart contracts",
]
series = ["smart contracts"]
+++


Suppose you want to have a smart contract creating ENS subdomains under a specific ENS domain and you don't need the user to be the subdomain owner.

Normally, you'd go to the ENS website, buy the domain, and then register subdomains. You are then able to add data to the domain or subdomains.

When doing it with a smart contract it's very much the same steps. The only difference is that you, as the owner of the ENS domain, need to allow the smart contract to control the ENS domain, so it can create new subdomains and set data.

So the steps are
1. write the smart contract to, 1. create the subdomain and 2. set data to that subdomain
2. authorize the smart contract to control the ENS
3. use it 🤪

In this example, we will use Ethereum Sepolia.

We need to interact with two different smart contracts. The NameWrapper and the Resolver. In Sepolia, the addresses are `0x0635513f179D50A207757E05759CbD106d7dFcE8` and `0x8FADE66B79cC9f707aB26799354482EB93a5B7dD` respectively. The NameWrapper is used to create a new subdomain and the resolver will then set data on that subdomain.

So, the first thing you need is a smart contract as mentioned before. Below is an example.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

interface INameWrapper {
    function setSubnodeRecord(bytes32 parentNode, string memory label, address owner, address resolver, uint64 ttl, uint32 fuses, uint64 expire) external;
}
interface IResolver {
    function setText(bytes32 node, string memory key, string memory value) external;
    function setAddr(bytes32 node, address addr) external;
}

contract ENSRecorderWrapper {
    address nameWrapper;
    address ensResolver;
    address parentDomainOwner;
    bytes32 parentNode;

    constructor(address _nameWrapper, address _ensResolver, address _parentDomainOwner, bytes32 _parentNode) {
        nameWrapper = _nameWrapper;
        ensResolver = _ensResolver;
        parentDomainOwner = _parentDomainOwner;
        parentNode = _parentNode;
    }
    
    function addSubdomain(string memory label) public {
        INameWrapper(nameWrapper).setSubnodeRecord(parentNode, label, parentDomainOwner, ensResolver, 0, 0, 0);
    }

    function setText(bytes32 node, string memory key, string memory value) public {
        IResolver(ensResolver).setText(node, key, value);
    }

    function setAddr(bytes32 node, address addr) public {
        IResolver(ensResolver).setAddr(node, addr);
    }
}
```

Now, you need to allow this smart contract on both the NameWrapper and the Resolver, so it can create the subdomains and set data.
For that, go to the NameWrapper and call "setApproveForAll" with the address of the deployed contract and "true". Then go to the Resolver and do the same.

Now you should be able to use the smart contract.

The following calls should work
```
addSubdomain("hello")
setText(namehash("hello.<your-domain>.eth"), <some-text>, <some-text>);
setAddr(namehash("hello.<your-domain>.eth"), <some-address>);
```

`namehash` method is available with viem at https://viem.sh/docs/ens/utilities/namehash#namehash. There is also an online tool https://ethtools.com/ens-namehash-labelhash-node-generator.
