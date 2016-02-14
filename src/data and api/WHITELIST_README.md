Some of the contracts have internal state that shouldn't be modified by code or users, besides the official Augur contracts. For example, it is currently possible for a user to modify every aspect of a markets state, (alpha, cumScale, total number of shares bought per outcome and per participant, etc.) There needs to be a way for contracts like markets.se to check the msg.sender's address, to make sure it is part of the allowed callers. Lists of allowed caller addresses are called whitelists. Contracts like markets.se need to use a whitelist, and the whitelist needs to be updateable in a decentralized way so that people don't have to trust the Augur devs to control augur's entire state. The method joey and I designed uses three primitives to alter the contracts' whitelists; replace, add and delete. A rep-weighted voting system is used to choose among proposed changes, proposals that recieve a majority of rep during the current voting period are executed at the start of the next voting period. If a rep holder has voted, they must not be allowed to send their rep to other accounts, so methods are provided for checking whether of not a voter has voted. Other method are provided to get various metadata including details about how an address voted at a certain period.

Each type of whitelist change has an associated function to create a "ballot item" (the sha3 hash of relevant info) which is added to the "ballot" (a list of hashes) for the current period. When a user votes for a change, they do it by submitting the hash they want to vote for with the whitelistVote function.

Creating whitelists doesn't use a vote, the first call to setWhitelist will work and subsequent calls will fail. The only way to change a whitelist is to vote on it. There is a potential attack vector here, where someone can watch the blockchain for augur contracts being uploaded, and they can set the whitelist for that contract before the compilation process for augurs contracts finish, but this kind of attack can be fixed by a vote. The voting period of whitelist changes is 1800 blocks, identical to the voting period of the root branch. The period is counted from the block.number of the contract's creation.