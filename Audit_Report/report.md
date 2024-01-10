### [S-#] Storing the password on-chain makes it vsisbile to anyone, and no longer private 

**Description:** All data stored on chain is visible to anyone even when it explicitely says only the `PasswordStore::s_owner` is the only one that can set password. 

The `PasswordStore::getpassword` function does not limit who sets passwords to the owner of the contracts.

See below for proof of concept 

**Impact:** Anyone can read the private password, severly breaking the functionality of the protocol 

**Proof of Concept:** - First i set my forge init --force toi update my foundry setup
- spin up my local chain
 ```make anvil```  
- to run the contract 
```make deploy``` 
- this wil get the hash of the password stored in
```cast storage [address of the contract][contract PasswordStore 0x5FbDB2315678afecb367f032d93F642f64180aa3] --rpc-url http//127.0.0.1:8545``` 
- pass word stored here will be in the hash
``` string private s_password;```
- it will produce the password of the owner 
```cast parse-bytes32-string [hash value]``` 

You will get the password on chain 

**Recommended Mitigation:** I believe password should not be stored this way, as it will leave chances to anyone picking through the password, passwords should be stored out of chain and decrypt hash should be implemented to encrypt and then then decrypt using password hash.


### [S-#] No access control: Anyone can call and change the password 

**Description:** the `PasswordStore::setPassword` function is set to be an `external` function, however, the entire focus of the contract is that `his function allows only the owner to set a new password.`

```javascript
function setPassword(string memory newPassword) external {
    s_password = newPassword;
        emit SetNetPassword();
    }
```

**Impact:** anyone can change the password therby serverly limiting the value of the contract intended usage 

**Proof of Concept:** Add the following to the `Password.t.sol` test file

<details>
<summary>code</summary>

```javascript
function test_anyone_can_set_password() public {
        string memory expectedPassword = "anyonesPassword";

        // Set the password as a non-owner
        vm.startPrank(address(1));
        passwordStore.setPassword(expectedPassword);
        string memory actualPassword = passwordStore.getPassword();
        assertEq(actualPassword, expectedPassword);
    }

```
</details>

**Recommended Mitigation:** Add an access control to `setPassword` function 

```javascript
if(msg.sender != s_owner){
    revert Passsword__Not_Owner()
}

```

### [S-#] Paramemter does not exist but included 

**Description:** The `PasswordStore::getPassowrd` function signature is `getPassword` while the natspec says it should include a string a parameter `getPassowrd(string)`.

**Impact:** Wrong, misleading concept 

**Recommended Mitigation:** remove the line or correct it to meet the code struture 

```diff
- * @param newPassword The new password to set.
```