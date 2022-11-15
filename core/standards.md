# Core Smart Contracts

## Definition of Core

A core contract is one that:

1. will have ward status on or other privileged access to any other core contract (i.e. `Vat.rely(<new contract address>)`)
2. will rely on the Pause Proxy as a permissioned actor. If the pause proxy has to directly interact with this contract then there is additional security and process work that needs to be done
3. will be included in the chainlog as a canonical “Maker Contract”. Even if the contract does not have elevated permissions in the system, a failure of a contract in the chainlog could result in cost (bug bounties) or reputation damage for the protocol.
4. If a contract is put forward to a business partner of the DAO, by DAO members, close consideration should be given to whether it would be perceived as core Maker brand by those partners.

## Protocol Engineering Review Process for Core Contracts

All Core contracts need to go through a risk adjusted review process.  Risk should be evaluated at the beginning of the project and continue to be re-evaluated through the engineering process.

Our review process at a minimum requires two team member reviews and approvals.  However for higher risk contracts may also involve team Red Team reviews and audits by external auditors.

Core contracts should follow established coding patterns including repo and contract structures, variable naming patterns (yes Daiwanese is a standard for our team) and auth patterns.

None exhaustive lists of our standards include:

- https://github.com/makerdao/mips-best-practices
- https://github.com/makerdao/pe-checklists (see spells and core)

## Coding standards we expect to see in core contracts

### Permissions

Permissioning in the contract should be handled by `wards` and the `wards` mapping should occupy memory slot `0x0`

### Contract ordering

We loosely follow: [Solidity Order of layout recommendations](https://docs.soliditylang.org/en/stable/style-guide.html#order-of-layout)

### Spacing

We align variables with extra whitespacing. You will get PR comments if you are off by even one  space.

### Naming

We prefer to try to have Daiwanese naming patterns (3 or 4 letter variables and functions).

In addition to this, if we are avoiding variable shadowing, we add a `_` to the end of the variable names.

If we need to interact with external contracts, we prefer to have a small interface using the naming convention of `<ContractName>Like`.

### Dependencies

We try to keep our repos relatively dependency free.  We do not rely on external contract libraries (i.e. Open Zeppelin) and try on only import submodules that are absolutely necessary as this keeps the cascading updates to a minimum.
