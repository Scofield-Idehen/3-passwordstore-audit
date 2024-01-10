### [S-#] Storing the password on-chain makes it vsisbile to anyone, and no longer private 

**Description:** All data stored on chain is visible to anyone even when it explicitely says only the `PasswordStore::s_owner` is the only one that can set password. 

The `PasswordStore::getpassword` function does not limit who sets passwords to the owner of the contracts.

See below for proof of concept 

**Impact:** Anyone can read the private password, severly breaking the functionality of the protocol 

**Proof of Concept:**

**Recommended Mitigation:** 