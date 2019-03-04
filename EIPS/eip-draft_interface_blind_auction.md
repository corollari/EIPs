---
eip: <to be assigned>
title: Interface for blind auctions
author: Albert Acebrón (@corollari)
discussions-to: https://github.com/ethereum/EIPs/issues/1745
status: Draft
type: Standards Track
category: ERC
created: 2019-03-04
---

<!--You can leave these HTML comments in your merged EIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new EIPs. Note that an EIP number will be assigned by an editor. When opening a pull request to submit your EIP, please use an abbreviated title in the filename, `eip-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the EIP.-->
Standard interface for contracts implementing blind auctions.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
Currently there doesn't exist any standard interface/contract for blind auctions, creating a void that has been filled with many de-facto standards such as the one used by ENS or the one used in the blind auction contract present in Solidity's documentation. This ERC aims to remove this fragmentation by proposing a standard interface for blind auction contracts.

## Motivation
<!--The motivation is critical for EIPs that want to change the Ethereum protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the EIP solves. EIP submissions without sufficient motivation may be rejected outright.-->
Many dApps that use blind auctions have appeared in the last years, each one reinventing the wheel and implementing blind auctions in ways that are conceptually similar but with special quirks and different interfaces, thus making it impossible for a system to interact with all of these without accomodating all these differences. This ERC aims to solve that by standarizing an interface for blind auctions contracts, which will enable the creation of systems that can interact with any blind auctions without requiring specialized code.

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Ethereum platforms (go-ethereum, parity, cpp-ethereum, ethereumj, ethereumjs, and [others](https://github.com/ethereum/wiki/wiki/Clients)).-->
```
interface BlindAuction {
    enum Mode { Open, Auction, Owned, Forbidden, Reveal, NotYetAvailable }

    event AuctionStarted(bytes32 indexed hash, uint registrationDate);
    event NewBid(bytes32 indexed hash, address indexed bidder, uint deposit);
    event BidRevealed(bytes32 indexed hash, address indexed owner, uint value, uint8 status);
    event HashRegistered(bytes32 indexed hash, address indexed owner, uint value, uint registrationDate);

    function startAuction(bytes32 _hash) external;
    function startAuctions(bytes32[] calldata _hashes) external;
    function newBid(bytes32 sealedBid) external payable;
    function startAuctionsAndBid(bytes32[] calldata hashes, bytes32 sealedBid) external payable;
    function unsealBid(bytes32 _hash, uint _value, bytes32 _salt) external;
    function cancelBid(address bidder, bytes32 seal) external;
    function finalizeAuction(bytes32 _hash) external;
    function entries(bytes32 _hash) external view returns (Mode, address, uint, uint, uint);
}
```

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->
The interface proposed has been chosen because it is the one used by the Ethereum Name System, which is the most popular project among the ones that use blind auctions, therefore being the one that has the most systems that integrate with it. By picking this interface we aim at minimizing the total amount of work that will be needed to migrate existing systems to the interface proposed, while maximizing the number of systems that will already be compatible with a contract implementing the interface described.

## Backwards Compatibility
<!--All EIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The EIP must explain how the author proposes to deal with these incompatibilities. EIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->
This EIP is backwards compatible with the Ethereum Name Service's blind auctions, the blind auction contract in the ETH community that has gained more traction and has the most systems interacting with it.

## Test Cases
<!--Test cases for an implementation are mandatory for EIPs that are affecting consensus changes. Other EIPs can choose to include links to test cases if applicable.-->
Not needed.

## Implementation
<!--The implementations must be completed before any EIP is given status "Final", but it need not be completed before the EIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->
[ENS implementation](https://github.com/ensdomains/ens/blob/94f4861a7783d8986e81b976e823caa81a16f285/contracts/HashRegistrar.sol).

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
