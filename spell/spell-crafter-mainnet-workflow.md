# Spell Crafter Mainnet Workflow

## Mainnet

Repo: https://github.com/makerdao/spells-mainnet

### [Governance Cadence Improvement](https://forum.makerdao.com/t/governance-cadence-improvement/14972)

![](https://ipfs.io/ipfs/QmUqCvy7c8Qmzn7yZ6D3353wTqCZ3VDAwQKYB37pJ2BjXb)

### Steps:
* [ ] Create Branch on GitHub `PE-<kanban ticket issue number>`
  * [ ] Note: for now current consensus is to use the same ticket issue number as per `spells-goerli` (this could be revised)
* [ ] Pull `master` Locally and Checkout Branch
* [ ] Cleanup Previous Spell's Actions in `DssSpell.sol` (diffcheck with Goerli)
  * [ ] Check previous spells in the `archive` folder for cleanup patterns
* [ ] Clenaup Previous Spell's Onboarding Actions in `DssSpellCollateral.sol`
  * [ ] Check previous spells in the `archive` folder for cleanup patterns
* [ ] Cleanup Base Tests in `config.sol` (under `test` folder in `src`)
  * [ ] Set `deployed_spell` to `address(0)`
  * [ ] Set `deployed_spell_created` to `0`
  * [ ] Consider to add `previous_spell` address if it haven't been executed yet
* [ ] Cleanup Specific Tests in `DssSpell.t.sol`
  * [ ] Check previous spells in the `archive` folder for cleanup patterns
  * [ ] Disable specific tests IF Not Used (eg. `testCollateralIntegrations`, `testNewChainlogValues`, `testNewIlkRegistryValues`, ...)
    * [ ] Remove specific part
    * [ ] Keep setup
    * [ ] Disable by setting visibility to `private`
    * [ ] Add commented notes
      * [ ] eg. `// Insert new collateral integration tests here`
    * [ ] Keep commented tests (eg. `testOSMs`, `testMedianizers`)
* [ ] Add Spell Actions as per GovAlpha Spell Content Sheet - [Polls](https://vote.makerdao.com/polling?network=mainnet)
  * [ ] Polls starts on Monday and ends on Thursday
    * [ ] Check polls details and links (forum posts, MIPs portal, ...)
  * [ ] Check on `new-spells` discord channel when Exec Doc is ready
  * [ ] Pragma
    * [ ] Current solc version `0.6.12`
    * [ ] Collateral Onboarding
      * [ ] Add `pragma experimental ABIEncoderV2;` (Only IF `DssExecLib.addNewCollateral` is used)
  * [ ] Interfaces
    * [ ] Avoid `dss-interfaces` multi-import layout (see [issue #69](https://github.com/makerdao/dss-interfaces/issues/69))
    * [ ] Prefer single import layout
      * [ ] `import { VatAbstract } from "dss-interfaces/dss/VatAbstract.sol";`
    * [ ] Consider static interfaces
      * [ ] IF not present in `dss-interfaces`
      * [ ] IF present in `dss-interfaces` but TOFIX
      * [ ] IF only few function interfaces are needed
  * [ ] Consider Turn Office Hours On
    * [ ] Check spell actions (eg. Onboarding, Liquidation Adjustments, New DSS Modules, ...)
    * [ ] Note: office hours are on by default if the modifier `officeHours` is not overridden
  * [ ] Deploy New Join, Clip and Calc (Onboarding)
    * [ ] `Join` (check which one is required)
      * [ ] Use [JoinFab](https://github.com/makerdao/JoinFab) to deploy
      * [ ] Etherscan Verification
        * [ ] Make sure AGPLv3 is specified and used
        * [ ] IF Flatten, consider removing `HEVM` interface artifacts
    * [ ] `Clip`
      * [ ] Use [ClipFab](https://github.com/makerdao/dss-deploy) to deploy
      * [ ] Etherscan Verification
        * [ ] Make sure AGPLv3 is specified and used
    * [ ] `Calc` (check which one is required)
      * [ ] Use [CalcFab](https://github.com/makerdao/dss-deploy) to deploy
      * [ ] Note: automatically verified on etherscan
    * [ ] Check with Oracle CU
      * [ ] Check IF oracle deployment is required (eg. univ3-lp-oracle, new ilk pip, ...)
      * [ ] Note: oracle should be deployed on mainnet before Friday (usually Wed-Thu)
  * [ ] Add New Addresses in the ChainLog
  * [ ] Bump ChainLog, accordingly with spec (`major`, `minor`, `patch`)
    * [ ] MAJOR -> New Vat
    * [ ] MINOR -> Core Module (DSS) Update (e.g. Flapper)
    * [ ] PATCH -> Collateral addition or addition/modification
  * [ ] Use DssExecLib Functions
    * [ ] Ensure `DssExecLib` address used in current spell (`DssExecLib.address`) matches `dss-exec-lib` [Latest Release Tag](https://github.com/makerdao/dss-exec-lib/releases/latest)
    * [ ] Check previous spells for common patterns
* [ ] Adjust Sytem Values, Collateral Values in `config.sol` ([diffcheck](https://www.diffchecker.com/) with `spells-goerli`)
* [ ] Add Specific Tests in `DssSpell.t.sol` ([diffcheck](https://www.diffchecker.com/) with `spells-goerli`)
  * [ ] Add new collateral tests
  * [ ] Add new chainLog value tests
  * [ ] Add new ilk registry value tests
  * [ ] Add specific tests (DAI/MKR Streams/Payments, Lerps, ...)
* [ ] Add new ChainLog Address in `addresses_mainnet.sol` (e.g. Collateral Onboarding)
* [ ] Run Tests `make test` or `make test match=<test_name>` to inspect debug traces
  * [ ] Make sure to use latest `dapptools`
    * [ ] `dapp --version`
    * [ ] `duppgrade <latest release>` (eg. `duppgrade hevm/0.49.0`)
* [ ] Debug via `make test-forge` or `make test-forge match=<test_name>` to inspect debut traces (for quicker testing)
  * [ ] Make sure to use latest `foundry`
    * [ ] Run `foundryup`
* [ ] Test PASS via `make test` (Ensure Good Coverage)
* [ ] Commit & Push
* [ ] Open Draft PR on `spells-mainnet` titled "PE-<ticket number>: YYYY-MM-DD Executive"
* [ ] Assign to yourself
* [ ] Open PR & Add Reviewers
* [ ] Iterate till Polls are Ended and Exec Copy is Merged
* [ ] Confirm Exec Doc Actions
* [ ] Make sure CI PASS
* [ ] Add Exec Hash
  * [ ] Check when Copy is Merged
  * [ ] Reference either Latest Change or Merge Commit
* [ ] Wait for at Least Two Approvals with Local Tests to Deploy
* [ ] Pre-Deploy Setup and Checks
  * [ ] Set local env (`.sethrc`)
    * [ ] Deployer
      * [ ] `export ETH_PASSWORD=~/.env/password.txt`
      * [ ] `export ETH_KEYSTORE=~/.ethereum/keystore`
      * [ ] `export ETH_FROM=<address>`
    * [ ] Mainnet EIP1559
      * [ ] `export ETH_GAS=X`
        * [ ] Run `make estimate` and adjust `ETH_GAS` accordingly
      * [ ] `export ETH_GAS_PRICE=$(seth --to-wei X gwei)`
        * [ ] Check Current Gas Price and Set `ETH_GAS_PRICE` accordingly
          * [ ] Consider adding margin to account for spikes (eg. current gas price 25 `gwei`, 50 `gwei` could be set)
      * [ ] `export ETH_PRIO_FEE=$(seth --to-wei X gwei)`
        * [ ] Check current gas priority fee and set `ETH_PRIO_FEE` accordingly
    * [ ] Mainnet RPC URL
      * [ ] `export ETH_RPC_URL=<url>`
    * [ ] Etherscan API KEY
      * [ ] `export ETHERSCAN_API_KEY=<key>`
    * [ ] Ensure to `source` the `.sethrc` file to make env vars available
  * [ ] Check local env
    * [ ] `seth ls`
    * [ ] `seth chain`
* [ ] Deploy Spell on Mainnet via `make deploy` (this should also verify the Spell on Etherscan)
* [ ] Add Deployed Spell Address & Timestamp to Base Tests
  * [ ] `deployed_spell: address(<deployed_spell_address>)`
  * [ ] `deployed_spell_created: <timestamp>`
    * [ ] Run `seth block <block_number> timestamp` or `make deploy-stamp tx=<tx_hash>`
* [ ] Run Tests Locally with deployed spell address
* [ ] Commit & Push for Review
* [ ] Wait for CI to PASS
* [ ] Wait for at Least two Approvals to Share for Publishing to GovAlpha
* [ ] Share Deployed Address in `new-spells`
* [ ] Archive Spell via `make archive-spell` for current date (or `date="YYYY-MM-DD" make archive-spell`)
* [ ] Commit & Push Archive for Review
* [ ] Wait for Merge Approvals and CI to PASS
* [ ] Squash & Merge
* [ ] Fill Spell Crafter Related Boxes in the Main Exec Doc Sheet

## Next Steps
* [ ] `MegaPoker` Contract Updates (handed over to Oracle Core Unit (OCU))
  * [ ] Inform oracle core unit of any change affecting the `MegaPoker`:
    * [ ] Collateral Onboardings
      * [ ] Including new ilk types (e.g. `ETH-B`)
    * [ ] Collateral Offboardings
    * [ ] Oracle replacements
    * [ ] ...
  * [ ] Sanity Checks
    * [ ] Coordinate with oracle CU and TechOps
    * [ ] Previous code patterns are followed
    * [ ] MegaPoker changes are reviewed & approved by OCU
    * [ ] CI & local tests PASS
    * [ ] New `MegaPoker` is deployed
    * [ ] New deployed `MegaPoker` contract address is handed to TechOps
    * [ ] New deployed `Megapoker` contract address is updated in the README
* [ ] Keeper Support for New Onboarded Collateral
* [ ] Keeper Support for New Lerps (to call `tall` daily) via [dss-cron](https://github.com/makerdao/dss-cron)
  * [ ] IF new offboardings via lerp (in current spell)
