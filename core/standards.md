# Core Smart Contracts

## Definition of Core

*A core contract is defined as one which satisfies at least one of the following tests.*


1. The contract's source code is part of the [dss repo](https://github.com/makerdao/dss)
2. The contract has any type of privileged access to any core contract (such as `ward` status via `rely`)
3. The contract relies on the Pause Proxy as a permissioned actor; if the pause proxy will at any point make an external call to this contract, then the contract is considered core and there is additional security and process work that needs to be done.
4. The contract is intended for inclusion in the [chainlog](https://chainlog.makerdao.com/).
5. If a contract is put forward to a business partner of the DAO, by DAO members, close consideration should be given to whether it would be perceived as core Maker brand by those partners.

## Protocol Engineering Review Process for Core Contracts

Core contracts are the central point upon which multi-collateral DAI is built. Every current and future core contracts undergoes through review process based on the level of risk it carried. Risk is evaluated at the beginning of each project and is continuously re-evaluated throughout the engineering process.

The review process requires at least two team members reviewing and approving the code. For higher risk contracts, Red Team reviews or audits by external auditors may also be required.

Core contracts should follow established coding patterns including repo and contract structures, variable naming patterns (yes [Daiwanese](https://docs.makerdao.com/other-documentation/system-glossary) is a standard for our team) and auth patterns. For most contracts, compiler version should be fixed at 0.8.n or 0.6.12 and optimizations should be off.  While licensing is considered on a case by case basis, it should always be declared and we tend to default to AGPLv3. We value security over gas optimizations and simplicity over complexity. As such, low level calls should be used minimally and gas optimization tricks should be used sparingly.

Whenever possible, utilize an existing solution instead of building a new contract. MakerDAO is a very complex project and each new contract or derivation increases the cognitive and maintenance overhead of the entire protocol. By limiting the number of moving parts we reduce the risk of unexpected behavior.

This repo attempts to document our standards, other sources include (non-exhaustive list):

- [MIPS best practices](https://github.com/makerdao/mips-best-practices)

## Coding standards we expect to see in core contracts

### Permissions

Authorization permissions in the contract should be handled by a `wards` mapping, which should occupy storage slot `0x0`

### Contract File

`.sol` files should adhere to the following standards:

- The SPDX license identifier is included at the start of the file
- Pragma declarations immediately follow the license declaration
- In almost all cases, there should only be one contract per file
- A single newline at the end of the file

### Contract ordering

Following and expanding on [Solidity Order of layout recommendations](https://docs.soliditylang.org/en/stable/style-guide.html#order-of-layout) our contracts are generally laid out in this way:

1. State variables (including wards and math)

    a. Declare constants first, then immutables and finally storage variables. All constants and variables should have visibility explicitly declared (`internal` in most cases).

    b. Wards should be storage slot `0x0`
2. Events
3. Getter functions ( to make reading structs easier)

    a. these may be placed at the end of the file if they are extensive to improve readability
4. Constructor
5. Modifiers
6. Internal math constants and functions
7. Auth control functions such as `rely` or `deny`
8. Fileable functions
9. Other Internal functions
10. Other External functions

### Spacing

We align variables with extra whitespace. You will get PR comments if you are off by even one space.

### Naming

We prefer to try to have **Daiwanese** naming patterns (3 or 4 letter variables and functions) such as `guy`, `usr`, or `who` for address parameters and `wad` or `amt` for `uint` or `int` parameters (with care given to `RAY`, `RAD`, and `WAD` precision).

Variables named to avoid shadowing are appended with an underscore (`_`).

Internal variable and function names are prepended with an underscore (`_`).

When interacting with external contracts is required, a minimal static interface before the contract declaration is used, with the naming convention `<ContractName>Like`.

### Dependencies

Dependencies should be avoided unless absolutely necessary to minimize cascading updates.  Core contracts must not rely on external contract libraries (i.e. OpenZeppelin or Solmate) only import submodules that are absolutely necessary.

### Antipatterns

Inheritance patterns should be avoided to avoid unnecessary complexity.

Upgradable proxies are considered an antipattern.

Where upgradability is required, it should be handled via a `rely` on the Pause Proxy and modular design.
*There are rare exceptions to this such as where there is a user mapping in state and high chance of needing to upgrade (e.g. cropper or charter).*

### Documentation

Repositories should have a `README.md` file which outlines the contract's purpose, its functionality and relevant instructions.  Any `external` or `public` functions should have [NatSpec comments](https://docs.soliditylang.org/en/latest/style-guide.html#natspec).

### Deployment

Contracts should be verified on Etherscan with a license and pragma clearly set. Optimizations generally should be turned off.  The verified code should match the main branch of the source repository on Github.
