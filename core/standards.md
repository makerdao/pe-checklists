# Core Smart Contracts

## Definition of Core

A core contract is one that:

1. Any contract that is part of the [dss repo](https://github.com/makerdao/dss)
2. Will have ward status on or other privileged access to any other core contract (i.e. `Vat.rely(<new contract address>)`)
3. Will rely on the Pause Proxy as a permissioned actor. If the pause proxy has to directly interact with this contract then there is additional security and process work that needs to be done.
4. Will be included in the chainlog as a canonical “Maker Contract”. Even if the contract does not have elevated permissions in the system, a failure of a contract in the chainlog could result in cost (bug bounties) or reputation damage for the protocol.
5. If a contract is put forward to a business partner of the DAO, by DAO members, close consideration should be given to whether it would be perceived as core Maker brand by those partners.

## Protocol Engineering Review Process for Core Contracts

All Core contracts need to go through a risk adjusted review process.  Risk should be evaluated at the beginning of the project and continue to be re-evaluated through the engineering process.

Our review process at a minimum requires two team member reviews and approvals.  However for higher risk contracts may also involve Red Team reviews and audits by external auditors.

Core contracts should follow established coding patterns including repo and contract structures, variable naming patterns (yes Daiwanese is a standard for our team) and auth patterns.

Whenever possible, utilize an existing solution instead of building a new contract. MakerDAO is a very complex project and each new contract or derivation increases the cognitive and maintenance overhead of the entire protocol. By limiting the number of moving parts we reduce the risk of unexpected behavior.

This repo attempts to document our standards, other sources include (non-exhaustive list):

- [MIPS best practices](https://github.com/makerdao/mips-best-practices)

## Coding standards we expect to see in core contracts

### Permissions

Permissioning in the contract should be handled by `wards` and the `wards` mapping should occupy storage slot `0x0`

### Contract ordering

Following and expanding on [Solidity Order of layout recommendations](https://docs.soliditylang.org/en/stable/style-guide.html#order-of-layout) our contracts are generally laid out in this way:

1. State variables (including wards and math)

    a. constants then immutables then storage variables

    b. wards should be storage slot `0x0`
2. Events
3. Getter functions (i.e. to make reading structs easier)

    a. these may be placed at the end of the file if they are extensive to improve readability
4. Constructor
5. Modifiers
6. Internal Math variables and functions
7. Wards functions (i.e. `rely`/`deny`)
8. Fileable functions
9. Other Internal functions
10. Other External functions

### Spacing

We align variables with extra whitespace. You will get PR comments if you are off by even one space.

### Naming

We prefer to try to have **Daiwanese** naming patterns (3 or 4 letter variables and functions).

If we are avoiding variable shadowing, we add a `_` to the end of the variable names.

If we are dealing with internal variables or functions we add the `_` to the front of the name.

If we need to interact with external contracts, we prefer to have a minimal static interface using the naming convention of `<ContractName>Like`. (These should be declared at the top of the contract file.)

### Dependencies

We try to keep our repos relatively dependency free.  We do not rely on external contract libraries (i.e. Open Zeppelin) and try on only import submodules that are absolutely necessary as this keeps the cascading updates to a minimum.

### Antipatterns

In order to limit contract complexity, we also tend to stay away from inheritance patterns.

We believe that upgradable proxies are an antipattern.  If you need upgradability, it should be handled with modular design and permission given to the Pause Proxy. There are rare exceptions to this such as where there is a user storage mapping and high chance of needing to upgrade (e.g. cropper or charter).

### Documentation

Repositories should have a README file that outline the purpose, functionality and how to use the contract.  In addition to this external/public functions should have [NatSpec comments](https://docs.soliditylang.org/en/latest/style-guide.html#natspec).
