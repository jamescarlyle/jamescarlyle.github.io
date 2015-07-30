---
layout: post
title:  "What is a Smart Contract?"
---

I'm spending quite a lot of time experimenting with [Smart Contracts](https://en.wikipedia.org/wiki/Smart_contract) on the [Ethereum](https://www.ethereum.org/) platform. There are many definitions for smart contracts: my favorite recollection (from [Richard Brown](http://gendal.me)) is 

> A smart contract is one where the control of assets is managed by a computer

Upon reflection, I think this would include all ***Coin protocols. Below, I am using the term "Smart Contracts" below in the sense that they exist on Ethereum, and have tried to capture what makes them special for me:

Features
========
1. Smart contracts are executable business logic and data, written in a computer language
2. Smart contracts can run on a public block-chain / distributed ledger that is de-centralised
3. The block chain is secure.
 
Taking each of these in turn:

Contracts are executable business logic and data, written in a computer language
--------------------------------------------------------------------------------

*	They are not ambiguous - because code inputs result in consistent outputs.

	They do not need a judicial system or lawyers to interpret them. But the lawyers will need to learn how to code! Consensus amongst techies and lawyers seems to be that law firms that have tech skills will do well in this space.

*	They can be unit-tested

	This is true in theory, and a tremendous potential benefit, since all parties to a contract would be able to test inputs and outputs, and verify the correct operation of a contract against a set of business rules. But this is still hard in practice, partly because writing effective tests is difficult anyway, and partly because the platforms (e.g. Ethereum) are still very immature in this area. For Ethereum, the Mix IDE is buggy and undocumented, and the Alethzero GUI offers a contract transaction debug but only against the EVM bytecode, not the human-readable Solidity code. The nature of committing to the blockchain doesn't help: transactions change state in the Ethereum contract, but the effects of the transaction are not felt until the transaction is mined in a block. So any test harness that is checking the actual outputs match the expected ones needs to wait until that mining is complete.

*	They execute quickly in seconds, automating back-office operations that are often manual today

	This is a key attribute - it's much easier and more efficient to validate if feedback is immediate. The whole point of shared ledgers and all parties seeing the same state is that reconciliation is not needed - but if a shared ledger was not updated for several days, then the reconciliation benefit is reduced.

*	They are binding

	Participants send irrevocable transactions and value into them. This means that participants need to be completely aware of what the contract means, and the implications of entering into it. A related issue is the representation of the contract - it could take the form of human-readable text, Solidity code, or compiled bytecode - what mechanisms exist to ensure that what is running is what is presented to the parties? I believe a decompiler, able to examine the actual compiled bytecode of a contract that has been launched and turn it into something more readable, will be necessary. Or at least a checking tool that can recompile a publicly stated Solidity contract and compare it against one running on the network.
   
*	BUT the State may still consider them to be illegal, or at least entered into illegally, or by coercion. 
	
	But a point that I made at a Law/Contract event hosted at the [Barclays Accelerator](http://www.barclaysaccelerator.com/#/home/) was that once they have been entered into, a censor-resistant public blockchain would prevent a judicial system from directly intervening in the operation of the contract anyway.
	
	Will we need a State-sponsored block-chain that allows the State to retain control?
	
	Or will a legal precedent be set that if the contract is executed and participated in an environment where the state has no control, that the State cannot intervene?

Contracts can run on a public block-chain / distributed ledger that is decentralised
--------------------------------------------------------------------------------

*	Contracts running on it are beyond the reach of manipulation

	They are executed on thousands of computers, beyond the control of a single powerful entity. It will become important to ensure that the network itself is sufficiently "diverse", i.e. that the mining is happening across nodes that are sufficiently separated from each other, both geographically and geopolitically. Of course, it also matters who those miners are - the predominance of a small number of Bitcoin miners in China is definitely an impediment for the adoption of Bitcoin by western financial service companies, for example.

*	Contracts on a public block-chain are permanent

	They cannot be re-written (they can be cancelled or reconfigured, but only through functions that are permanent and visible from the point of creation and before any participation)

	They can be very long-lived, and retain history and data state. Once a transaction is sent, it is visible, and the internal state of the contract is visible (since it is replicated on all nodes participating), so contract authors will need to ensure that only public data is held in the internal state. This has an interesting side-effect - it means that mechanisms involving secrets (such as a secret passsed to prove that delivery of something has occurred) cannot lodge the secret in the contract directly - a hash of the secret will need to be used insted.

*	Contracts do not rely on trust between parties

	They can disintermediate “middle-men” such as escrow agents and banks

	But where there is an interface with the real-world (e.g. exchange of goods), creative thinking is needed to prove real-world events. Actually I think trusted third parties will emerge, providing canonical data, so the need for Schelling Coin mechanisms to indicate whether events occurred will be reduced.

The block chain is secure
--------------------------------------------------------------------------------

*	All transactions are controlled by public/private keys and open hash cryptography algorithms – at least as secure as BitCoin. 

	This is key  - the larger the transaction, the more important this becomes. Ethereum itself has been subject to 2 external security audits, but the more compelling proof will be correct operation with a range of contracts and parties, over an extended period of time.


I'd love to debate these implications (or add any that you think are significant that I may have missed), so please get in touch if you disagree.

James
