I've been busy over the last few months, compiling Solidity [Smart Contracts](https://en.wikipedia.org/wiki/Smart_contract) on the [Ethereum](https://www.ethereum.org/) platform.

Solidity itself is a really comfortable language for me to work with - it is conceptually a small language, with a manageable learning curve.

However, I was having many problems with "Stack too deep, try removing local variables" errors when compiling, and this caused a lot of refactoring efforts and headscratching for me. Hopefully this will be useful for others, as the problem is not particularly well documented at this point.

My contract had one function in that caused this:

````
function nominate(bytes16 categoryID, bytes16 nomineeUserID, uint nominatedAmount, bytes32 nominationReason) public returns (bool) {
  // lookup users involved
  User nominator = usersByAddress[msg.sender];
  User nomineeUser = usersByUserID[nomineeUserID];
  // lookup category
  Category c = categories[categoryID];
  // check that both nominator and nominee have been registered, and category exists and still open
  if (nominator.userName == "" || nominator.accountBalance < nominatedAmount || nomineeUser.userName == "" || c.categoryName == "" || now > c.endTime) {
    return false;
  }
  // get a nominee
  Nominee n;
  // check if user has been nominated in this category before
  uint nomineeSeq = c.nomineeSeqsByUserID[nomineeUserID];
  if (nomineeSeq == 0) {
    // first time nomination in this category
    nomineeSeq = c.numNominees++;
    c.nomineeSeqsByUserID[nomineeUserID] = nomineeSeq;
    // now construct nominee
    n.nomineeUser = nomineeUser;
    n.nomineeTotal = 0;
    c.nomineesBySeq[nomineeSeq] = n;
  } else {
    n = c.nomineesBySeq[nomineeSeq];
  }
  // construct the nomination
  Nomination nomination = c.nominationsBySeq[c.numNominations++];
  nomination.nominator = nominator;
  nomination.nominee = n;
  nomination.nominatedAmount = nominatedAmount;
  nomination.nominationReason = nominationReason;
  // update balances
  n.nomineeUser.accountBalance =+ nominatedAmount;
  n.nomineeTotal =+ nominatedAmount;
  nominator.accountBalance =- nominatedAmount;
  c.nominationTotal =+ nominatedAmount;
  return true;
}
````

The function is part of a reward-scheme contract, where nominators within organisations could nominate others, and  the organisation itself would commit to funding a rewards pool that could be distributed later in the ratio of nominations to nominees. I was trying to allow quite a complex state to be maintained, so that even with multiple nominations to a recipient, they would receive a single Ether payment.

The initial response from the Ethereum team was

> Depending on what you do, you can have around 16 local variables (including parameters and return parameters). The problem with this particular code is that it has a lot of storage references which actually take two stack slots (because of packed storage you also need the offset inside the slot), although they do not have to, I will try to fix that. What you could do now is to refactor that function into smaller functions.

So every input parameter to the function takes one variable, and each return value takes one variable, and each local declaration takes another variable, and references to storage take two - it surprised me how quickly the limit of 16 was reached. I tried refactoring sub-blocks of code into other internal functions, but then the contract became harder for me to test.

So the way I solved this was to simplify separate parts of the function.

````
function nominate(bytes16 categoryID, bytes16 nomineeUserID, uint nominationAmount, bytes32 nominationReason) public returns (bool) {
  // lookup nominator and recipient involved
  User nominator = usersByAddress[msg.sender];
  User user = usersByUserID[nomineeUserID];
  // lookup category
  Category c = categories[categoryID];
  // check that both nominator and recipient have been registered, and category exists and still open
  if (!nominator.exists || !user.exists || nominator.accountBalance < nominationAmount || !c.exists || now > c.endTime) {
    return false;
  }
  // lookup nominee
  Nominee nominee = c.nominees[nomineeUserID];
  if (!nominee.exists) {
    // nominee doesn't exist in this category yet
    nominee.user = user;
    nominee.previousNomineeID = c.lastNomineeID;
    nominee.nominationTotal = 0;
    nominee.numNominations = 0;
    nominee.exists = true;
    c.lastNomineeID = nomineeUserID;
  }
  nominee.nominationTotal += nominationAmount;
  // construct the nomination
  nominee.nominations.length += 1;
  nominee.nominations[nominee.nominations.length - 1] = Nomination({nominator: nominator, nominationAmount: nominationAmount, nominationReason: nominationReason});
  // update balances
  user.accountBalance += nominationAmount;
  nominator.accountBalance -= nominationAmount;
  c.nominationTotal += nominationAmount;
  c.numNominations++;
  return true;
}
````

So for example, the mapping of nominees within a category was changed to be by userID rather than sequence number. This removed the immediate ability to iterate over this mapping during the reward distribution, and so I replaced this by a single linked-list arrangement, allowing a later function to traverse the nominees. Also, a variable declaration was avoided by the setting of an array element by using the Struct({propertyName1: propertyValue1}) construction.

Further:

> The 16 is also not a strict limit, it can be smaller, it depends on what you do with the variables.

and

> The limit is imposed by the fact that the EVM can only reach a specific depth into the stack and the fact that local variables are stored on the stack in Solidity.

Note that this has nothing to do with [Packed Storage](https://github.com/ethereum/wiki/wiki/Solidity-Features#tightly-packed-storage) - each variable takes one slot, even a bool.

Finally, the issue of storage references taking two stack slots is now being fixed - see [here](https://github.com/ethereum/cpp-ethereum/pull/2460).

James
