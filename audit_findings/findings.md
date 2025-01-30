### [s-1] TITLE (storing the password on-chain is visable to anyone, and no longer private )

**Description:** All data on chain is visable to anyone and can be read directly from the blockchain `Passwordstore::s_password` virable is intended to be private and should be called from the `Passwordstore::getPassword`

**Impact:** since anyone can access the password then it breaks the functionality of the `Passwordstore and the contract. 

**Proof of Concept:**

the below test case shows anyone can access the password

1. Create a local running chain 

```
make anvil
```

2. Deploy the contract to the chain 

```
make deploy
```

3. Run the storage tool 

we use 1 beacuse thats the storage slot of s_password in the contract.

```
cast storage <address_here> 1 --rpc-url http://127.0.0.1:8545
```
0x9fE46736679d2D9a65F0992F2272dE9f3c7fa6e0
You will get an output like this

0x6d7950617373776f726400000000000000000000000000000000000000000014

0x6d7950617373776f726400000000000000000000000000000000000000000014

you can then parse that hex to a string

```
cast parse-bytes32-string 0x
```

And get an output of 

`mypassword`


**Recommended Mitigation:** Dues to this, the overall structure should be redefined. 

### [s-2] `Password::setPassword` has no access control, meaning a non-owner can change the password
**Description:**
**Impact:**
**Proof of Concept:**
**Recommended Mitigation:** 0x5FbDB2315678afecb367f032d93F642f64180aa3




