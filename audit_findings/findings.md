### [s-#] TITLE (storing the password on-chain is visable to anyone, and no longer private )

**Description:** All data on chain is visable to anyone and can be read directly from the blockchain `Passwordstore::s_password` virable is intended to be private and should be called from the `Passwordstore::getPassword`

**Impact:** since anyone can access the password then it breaks the functionality of the `Passwordstore and the contract. 

**Proof of Concept:**

the below test case shows anyone can access the password

1. Create a local running chain 

`make anvil`

2. Deploy the contract to the chain 

`make deploy`

3. Run the storage tool 

we use 1 beacuse thats the storage slot of s_password in the contract.

`cast storage <address_here> 1 --rpc-url http://127.0.0.1:8545`


**Recommended Mitigation:**