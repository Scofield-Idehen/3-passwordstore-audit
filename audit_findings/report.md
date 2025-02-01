---
title: PasswordStore Protocol Audit
author: scofield Idehen
date: January 31, 2025
header-includes:
  - \usepackage{titling}
  - \usepackage{graphicx}
---

\begin{titlepage}
    \centering
    \begin{figure}[h]
        \centering
        \includegraphics[width=0.5\textwidth]{logo2.pdf}
    \end{figure}
    \vspace*{2cm}
    {\Huge\bfseries Protocol Audit Report\par}
    \vspace{1cm}
    {\Large Version 1.0\par}
    \vspace{2cm}
    {\Large\itshape \par}
    \vfill
    {\large \today\par}
\end{titlepage}

\maketitle

<!-- Your report starts here! -->

Prepared by: [Black Spider](https://scofield-idehen.github.io/home/)
Lead Auditors:

- xxxxxxx

# Table of Contents

- [Table of Contents](#table-of-contents)
- [Protocol Summary](#protocol-summary)
- [Disclaimer](#disclaimer)
- [Risk Classification](#risk-classification)
- [Audit Details](#audit-details)
  - [Scope](#scope)
  - [Roles](#roles)
- [Executive Summary](#executive-summary)
  - [Issues found](#issues-found)
- [Findings](#findings)
- [High](#high)
  - [\[s-1\] (storing the password on-chain is visable to anyone, and no longer private )](#s-1-storing-the-password-on-chain-is-visable-to-anyone-and-no-longer-private-)
  - [\[s-1\] (`Password::setPassword` has no access control, meaning a non-owner can change the password)](#s-1-passwordsetpassword-has-no-access-control-meaning-a-non-owner-can-change-the-password)

# Protocol Summary

Passwordstore is a protocol dedicated to storage and retrivial of a user's passwords. the protocol is designed to be used by a single user, and is not designed to be used by multiple users. Only the owner shoud be set and able to access the password.

# Disclaimer

The Black_spider team makes all effort to find as many vulnerabilities in the code in the given time period, but holds no responsibilities for the findings provided in this document. A security audit by the team is not an endorsement of the underlying business or product. The audit was time-boxed and the review of the code was solely on the security aspects of the Solidity implementation of the contracts.

# Risk Classification

|            |        | Impact |        |     |
| ---------- | ------ | ------ | ------ | --- |
|            |        | High   | Medium | Low |
|            | High   | H      | H/M    | M   |
| Likelihood | Medium | H/M    | M      | M/L |
|            | Low    | M      | M/L    | L   |

We use the [CodeHawks](https://docs.codehawks.com/hawks-auditors/how-to-evaluate-a-finding-severity) severity matrix to determine severity. See the documentation for more details.

# Audit Details

**The findings described in this document correspond to the following commit hash**

```
e3b0c44298fc1c149afbf4c8996fb92427ae41e4
```

## Scope

```
./src/
PassowrdStore.sol
```

## Roles

- Owner: the user who set the password and read the password
- Outsider: No one else should be able to set or read the password

# Executive Summary

The Passwordsol file was a 45 line code base with two fucntion and a constructor.

While reviwing the codebase, we found 3 issues which show the codebase was succibale to attack and should be fixed immediality.

## Issues found

| Severity | NUmber of issues Found |
| -------- | ---------------------- |
| High     | 2                      |
| Medium   | 0                      |
| Low      | 0                      |
| Info     | 0                      |
| Total    | 2                      |

# Findings

# High

## [s-1] (storing the password on-chain is visable to anyone, and no longer private )

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



## [s-1] (`Password::setPassword` has no access control, meaning a non-owner can change the password)

**Description:** The `PasswordStore::setPassword` function is set to be an `external` function, however the netspec of the function and overall purpose of the smart contract is that `this function allows only the owner to set a new password`

```javascript 

 function setPassword(string memory newPassword) external {
        // if (msg.sender != s_owner) {
        //     revert PasswordStore__NotOwner();
        // }
        s_password = newPassword;
        emit SetNewPassword();
    }
```

**Impact:** Anyone can set/change the password therby breaking the password store. 

**Proof of Concept:**

**Recommended Mitigation:**  Add an access control to the `setPassword` function. 

```javascript
if(msg.sender != s_onwer){
    revert PasswordStore_Notowner();
}
```






