# Core Smart Contracts

## Definition of Core

A core contract is one that:

1. Any contract that is part of the [dss repo](https://github.com/makerdao/dss)
2. will have ward status on or other privileged access to any other core contract (i.e. `Vat.rely(<new contract address>)`)
3. will rely on the Pause Proxy as a permissioned actor. If the pause proxy has to directly interact with this contract then there is additional security and process work that needs to be done.
4. will be included in the chainlog as a canonical “Maker Contract”. Even if the contract does not have elevated permissions in the system, a failure of a contract in the chainlog could result in cost (bug bounties) or reputation damage for the protocol.
5. If a contract is put forward to a business partner of the DAO, by DAO members, close consideration should be given to whether it would be perceived as core Maker brand by those partners.

## Protocol Engineering Review Process for Core Contracts

All Core contracts need to go through a risk adjusted review process.  Risk should be evaluated at the beginning of the project and continue to be re-evaluated through the engineering process.

Our review process at a minimum requires two team member reviews and approvals.  However for higher risk contracts may also involve team Red Team reviews and audits by external auditors.

Core contracts should follow established coding patterns including repo and contract structures, variable naming patterns (yes Daiwanese is a standard for our team) and auth patterns.

Whenever possible, utilize an existing solution instead of building a new contract. MakerDAO is a very complex project and each new contract or derivation increases the cognitive and maintenance overhead of the entire protocol. By limiting the number of moving parts we reduce the risk of unexpected behavior.

This repo attempts to document our standards, other sources include (non-exhaustive list):

- https://github.com/makerdao/mips-best-practices

## Coding standards we expect to see in core contracts

### Permissions

Permissioning in the contract should be handled by `wards` and the `wards` mapping should occupy memory slot `0x0`

### Contract ordering

Following and expanding on [Solidity Order of layout recommendations](https://docs.soliditylang.org/en/stable/style-guide.html#order-of-layout) our contracts are generally laid out in this way:

1. Wards variables, events and functions
2. Other State variables (non-math)
3. Other Events
4. Modifiers
5. Internal Math variables and functions
6. Other Internal Functions
7. External Funcitons

### Spacing

We align variables with extra whitespacing. You will get PR comments if you are off by even one space.

### Naming

We prefer to try to have **Daiwanese** naming patterns (3 or 4 letter variables and functions).

If we are avoiding variable shadowing, we add a `_` to the end of the variable names.

If we are dealing with internal variables or functions we add the `_` to the front of the name.

If we need to interact with external contracts, we prefer to have a minimal interface using the naming convention of `<ContractName>Like`.

### Dependencies

We try to keep our repos relatively dependency free.  We do not rely on external contract libraries (i.e. Open Zeppelin) and try on only import submodules that are absolutely necessary as this keeps the cascading updates to a minimum.

### Antipatterns

In order to limit contract complexity, we also tend to stay away from inheritance patterns.

We believe that upgradeable proxies are an antipattern.  If you need upgradeability, it should be handled with modular design and permission given to the Pause Proxy.
