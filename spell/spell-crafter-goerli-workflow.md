# Spell Crafter Goerli Workflow

## Goerli

Repo: https://github.com/makerdao/spells-goerli

### [Governance Cadence Improvement](https://forum.makerdao.com/t/governance-cadence-improvement/14972)

![](https://ipfs.io/ipfs/QmUqCvy7c8Qmzn7yZ6D3353wTqCZ3VDAwQKYB37pJ2BjXb)

### Steps:
* [ ] Create Branch `PE-<kanban ticket issue number>`
  * [ ] Note: for now current consensus is to use the same ticket issue number as per `spells-mainnet` (this could be revised)
* [ ] Pull Locally and Checkout Branch
* [ ] Cleanup Previous Spell's Actions in `Goerli-DssSpell.sol`
  * [ ] Check Previous Spells in the `archive` Folder for Cleanup Patterns
  * [ ] Check Template in `template` (Ensure it's up to date, checking against latest spells)
* [ ] Clenaup Previous Spell's Onboarding Actions in `Goerli-DssSpellCollateral.sol`
  * [ ] Check Previous Spells in the `archive` Folder for Cleanup Patterns
  * [ ] Check Template in `template` (TO Keep UP TO DATE)
* [ ] Cleanup Base Tests in `config.sol` (under `test` folder in `src`)
  * [ ] Set `deployed_spell` to `address(0)`
  * [ ] Set `deployed_spell_created` to `0`
* [ ] Cleanup Specific Tests in `Goerli-DssSpell.t.sol`
  * [ ] Check Previous Spells in the `archive` Folder for Cleanup Patterns
  * [ ] Disable Specific Tests IF Not Used (eg. `testCollateralIntegrations`, `testNewChainlogValues`, `testNewIlkRegistryValues`, ...)
    * [ ] Remove Specific Part
    * [ ] Keep Setup
    * [ ] Disable by setting visibility to `private`
    * [ ] Add Commented Notes
      * [ ] eg. `// Insert new collateral integration tests here`
    * [ ] Keep Commented Tests (eg. `testOSMs`, `testMedianizers`)
* [ ] Add Spell Actions as per GovAlpha Spell Content Sheet - [Polls](https://vote.makerdao.com/polling?network=mainnet)
  * [ ] Polls Starts on Monday and Ends on Thursday
    * [ ] Check Polls Details and Links (Forum Posts, MIPs Portal, ...)
  * [ ] Check on `new-spells` discord channel when Exec Doc is ready
  * [ ] Pragma
    * [ ] Current Solc Version `0.6.12`
    * [ ] Collateral Onboarding
      * [ ] Add `pragma experimental ABIEncoderV2;` (Only IF `DssExecLib.addNewCollateral` is used)
  * [ ] Interfaces
    * [ ] Avoid `dss-interfaces` Multi-Import Layout (see [issue #69](https://github.com/makerdao/dss-interfaces/issues/69))
    * [ ] Prefer Single Import Layout
      * [ ] `import { VatAbstract } from "dss-interfaces/dss/VatAbstract.sol";`
    * [ ] Consider Static Interfaces
      * [ ] IF not present in `dss-interfaces`
      * [ ] IF present in `dss-interfaces` but TOFIX
      * [ ] IF only few function interfaces are needed
  * [ ] Office Hours (OFF by Default on Goerli)
  * [ ] Deploy New Join, Clip and Calc (Onboarding)
    * [ ] `Join` (check which one is required)
      * [ ] Use [JoinFab](https://github.com/brianmcmichael/JoinFab) to Deploy
      * [ ] Etherscan Verification
        * [ ] Make sure AGPLv3 is specified and used
        * [ ] IF Flatten, Consider Removing HEVM Interface Artifacts
    * [ ] `Clip`
      * [ ] Use [ClipFab](https://github.com/makerdao/dss-deploy) to Deploy
      * [ ] Etherscan Verification
        * [ ] Make sure AGPLv3 is specified and used
    * [ ] `Calc` (check which one is required)
      * [ ] Use [CalcFab](https://github.com/makerdao/dss-deploy) to Deploy
      * [ ] Note: Automatically Verified on Etherscan
    * [ ] Check with Oracle CU
      * [ ] Check IF Oracle Deployment is Required (eg. univ3-lp-oracle, new ilk pip, ...)
  * [ ] Add New Addresses in the ChainLog
  * [ ] Bump ChainLog, accordingly with spec (`major`, `minor`, `patch`)
    * [ ] MAJOR -> New Vat
    * [ ] MINOR -> Core Module (DSS) Update (e.g. Flapper)
    * [ ] PATCH -> Collateral addition or addition/modification
  * [ ] Use DssExecLib Functions
    * [ ] Check DssExecLib Address Used in Spell (`DssExecLib.address`) Matches `dss-exec-lib` [Latest Release Tag](https://github.com/makerdao/dss-exec-lib/releases/latest)
    * [ ] Check Previous Spells for Common Patterns
* [ ] Adjust Sytem Values, Collateral Values in `config.sol` ([diffcheck](https://www.diffchecker.com/) with `spells-mainnet`)
* [ ] Add Specific Tests in `Goerli-DssSpell.t.sol` ([diffcheck](https://www.diffchecker.com/) with `spells-mainnet`)
  * [ ] Add New Collateral Tests
  * [ ] Add New ChainLog Value Tests
  * [ ] Add New Ilk Registry Value Tests
  * [ ] Add Specific Tests (DAI/MKR Streams/Payments, Lerps, ...)
* [ ] Add new ChainLog Address in `addresses_goerli.sol` (e.g. Collateral Onboarding)
* [ ] Run Tests `make test` or `make test match=<test_name>` to inspect debug traces
  * [ ] Make sure to use latest `dapptools`
    * [ ] `dapp --version`
    * [ ] `duppgrade <latest release>` (eg. `duppgrade hevm/0.49.0`)
* [ ] Debug via `make test-forge` or `make test-forge match=<test_name>` to inspect debut traces (for quicker testing)
  * [ ] Make sure to use latest `foundry`
    * [ ] Run `foundryup`
* [ ] Test PASS via `make test` (Ensure Good Coverage)
* [ ] Commit & Push
* [ ] Open Draft PR on `spells-goerli` titled "PE-<ticket number>: YYYY-MM-DD Executive"
* [ ] Assign to yourself
* [ ] Open PR & Add Reviewers
* [ ] Iterate till Polls are Ended and Exec Doc is Confirmed
* [ ] Make sure CI PASS
* [ ] Wait for at Least Two Approvals with Local Tests to Deploy
* [ ] Pre-Deploy Checks
  * [ ] Set Local Env
  * [ ] Check Local Env
  * [ ] Run `make estimate` and adjust `ETH_GAS` accordingly
  * [ ] Check Current Gas Price and Set `ETH_GAS_PRICE` accordingly
  * [ ] Check Current Gas Priority Fee and Set `ETH_PRIO_FEE` accordingly
  * [ ] Check Env Chain `seth chain`
* [ ] Deploy Spell on Goerli via `make deploy` (this should also verify the Spell on Etherscan)
* [ ] Add Deployed Spell Address & Timestamp to Base Tests
  * [ ] `deployed_spell: address(<deployed_spell_address>)`
  * [ ] `deployed_spell_created: <timestamp>`
  * [ ] Run `seth block <block_number> timestamp` or `make deploy-stamp tx=<tx_hash>`
* [ ] Run Tests Locally with deployed spell address
* [ ] Commit & Push for Review
* [ ] Wait for CI to PASS
* [ ] Wait for Approval
* [ ] Cast Spell via `make cast-spell` (ONLY ON GOERLI)
* [ ] Archive Spell via `make archive-spell` for current date (or `date="YYYY-MM-DD" make archive-spell`)
* [ ] Wait for Merge Approval
* [ ] Squash & Merge
* [ ] Port Changes to `spells-mainnet`
