# Spell Crafter Mainnet Workflow

## Mainnet

Repo: https://github.com/makerdao/spells-mainnet

### [Governance Cadence Improvement](https://forum.makerdao.com/t/governance-cadence-improvement/14972)

![](https://ipfs.io/ipfs/QmUqCvy7c8Qmzn7yZ6D3353wTqCZ3VDAwQKYB37pJ2BjXb)

### Steps:
* [ ] Create a new branch on the `spells-mainnet` repo named `YYYY-MM-DD` using the initial target date of the spell
  * [ ] Ensure the same target date is used as the corresponding `spells-goerli` spell branch
* [ ] Pull `master` Locally and Checkout Branch (IF Branch is created via GitHub)
* [ ] Pull `master` Locally, Create and Checkout Branch (IF Branch was not created via GitHub)
* [ ] Cleanup Previous Spell's Actions in `DssSpell.sol` (diffcheck with Goerli)
  * [ ] Check previous spells in the `archive` folder for cleanup patterns
  * [ ] Delete unused dependencies in the `src/dependencies` folder where applicable
* [ ] Cleanup `src/test/config.sol`
  * [ ] Set `deployed_spell` to `address(0)`
  * [ ] Set `deployed_spell_created` to `0`
  * [ ] Set `deployed_spell_block` to `0`
  * [ ] Consider to add `previous_spell` address if it haven't been executed yet
* [ ] Cleanup Specific Tests in `DssSpell.t.sol`
  * [ ] Check previous spells in the `archive` folder for cleanup patterns
  * [ ] Disable specific tests IF Not Used (e.g. `testCollateralIntegrations`, `testNewChainlogValues`, `testNewIlkRegistryValues`, ...)
    * [ ] Remove spell-specific part
    * [ ] Keep setup
    * [ ] Disable by setting visibility to `private`
    * [ ] Add commented notes
      * [ ] e.g. `// Insert new collateral integration tests here`
    * [ ] Keep commented tests (e.g. `testOSMs`, `testMedianizers`)
* [ ] Run Tests `make test` or `make test match=<test_name>` to inspect debug traces
  * [ ] Ensure to use latest `foundry` stable version
    * [ ] Run `foundryup`
* [ ] Tests PASS via `make test`
* [ ] Commit & Push `Base spell`
* [ ] CI Tests PASS
* [ ] Open Draft PR on `spells-mainnet` titled "PE-<ticket number>: YYYY-MM-DD Executive"
* [ ] Assign to yourself
* [ ] Add Spell Actions as per [GovAlpha Spell Content Sheet](https://docs.google.com/spreadsheets/d/1w_z5WpqxzwreCcaveB2Ye1PP5B8QAHDglzyxKHG3CHw) - [Polls](https://vote.makerdao.com/polling?network=mainnet)
  * [ ] Polls starts on Monday and ends on Thursday
    * [ ] Ensure spell actions match polls details and links (forum posts, MIPs portal, ...)
    * [ ] Check spell copy is merged in [community](https://github.com/makerdao/community/blob/master/governance/votes) repo
    * [ ] Add a comment for the polling detail URL
          `// https://vote.makerdao.com/polling/<hash>`
    * [ ] Add a comment for the forum post URL
          `// https://forum.makerdao.com/t/<title>/<number>`
  * [ ] Check on `new-spells` discord channel when Exec Doc is ready
  * [ ] Pragma
    * [ ] Current solc version `0.8.16`
  * [ ] Interfaces
    * [ ] Consider using `DssExecLib` actions where possible (to avoid introducing interfaces where not required)
    * [ ] Avoid `dss-interfaces` multi-import layout (see [issue #69](https://github.com/makerdao/dss-interfaces/issues/69))
    * [ ] Prefer single import layout
      * [ ] `import { VatAbstract } from "dss-interfaces/dss/VatAbstract.sol";`
    * [ ] Consider static interfaces
      * [ ] IF not present in `dss-interfaces`
      * [ ] IF present in `dss-interfaces` but TOFIX
      * [ ] IF only a few function interfaces are needed
  * [ ] Consider Turn Office Hours On
    * [ ] Check spell actions (e.g. Onboarding, Liquidation Adjustments, New DSS Modules, ...)
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
      * [ ] Check IF oracle deployment is required (e.g. univ3-lp-oracle, new ilk pip, ...)
      * [ ] Note: oracle should be deployed on mainnet before Friday (usually Wed-Thu)
  * [ ] Ensure every spell variable is declared as public/internal
  * [ ] Consider `immutable` visibility when fetching addresses from the `ChainLog` via `DssExecLib.getChangelogAddress` and use `constant` for static addresses
    * [ ] Fetch addresses as type `address` and wrap with `Like` suffix interfaces inline (when making calls) unless archive patterns permit otherwise (Such as `MKR`)
    * [ ] Use the [DssExecLib Core Address Helpers](https://github.com/makerdao/dss-exec-lib/blob/master/src/DssExecLib.sol#L166) where possible (e.g. `DssExecLib.vat()`)
    * [ ] Variable names of addresses fetched from the `ChainLog` match the corresponding ChainLog key (e.g. `MCD_VAT` rather than `vat`) except where archive pattern differs (e.g. MKR)
  * [ ] Add New Addresses in the ChainLog
  * [ ] Bump ChainLog, accordingly with spec (`major`, `minor`, `patch`)
    * [ ] MAJOR -> New Vat
    * [ ] MINOR -> Core Module (DSS) Update (e.g. Flapper)
    * [ ] PATCH -> Collateral addition or addition/modification
  * [ ] Use DssExecLib Functions
    * [ ] Ensure `DssExecLib` address used in current spell (`DssExecLib.address`) matches `dss-exec-lib` [Latest Release Tag](https://github.com/makerdao/dss-exec-lib/releases/latest)
    * [ ] Check previous spells for common patterns
* [ ] Adjust System Values, Collateral Values in `config.sol` (i.e. diffcheck via vscode `code --diff config1.sol config2.sol`) with `spells-goerli`)
* [ ] Add Specific Tests in `DssSpell.t.sol` (i.e. diffcheck via vscode `code --diff source1.sol source2.sol`) with `spells-goerli`)
  * [ ] Add new collateral tests
  * [ ] Add new chainLog value tests
  * [ ] Add new ilk registry value tests
  * [ ] Add specific tests (DAI/MKR Streams/Payments, Lerps, ...)
* [ ] Add new ChainLog Address in `addresses_mainnet.sol` (e.g. Collateral Onboarding)
* [ ] Run Tests `make test` or `make test match=<test_name>` to inspect debug traces
  * [ ] Ensure Good Coverage
  * [ ] Ensure every test function is declared as public if enabled or private if disabled
  * [ ] Tests PASS via `make test`
* [ ] Open PR & Add Reviewers
* [ ] Iterate until polls are ended and exec doc is confirmed
* [ ] Confirm Exec Doc Actions
* [ ] Make sure CI PASS
* [ ] Add Exec Hash
  * [ ] Run `make exec-hash` for current date, or `date=YYYY-MM-DD` and update spell code accordingly
    * [ ] Executive vote file name and date is correct
    * [ ] [community](https://github.com/makerdao/community) repo commit hash corresponds to latest change
    * [ ] Raw GitHub URL is correct
    * [ ] Exec hash is correct (use `cast keccak -- "$(curl '$URL' -o - 2>/dev/null)"` where `wget` doesn't work)
  * [ ] Ensure `description` date in `DssSpell.sol` matches exec copy one
* [ ] Wait for at Least Two Approvals with local tests to deploy
* [ ] Pre-Deploy Setup and Checks (currently via `dapptools`)
  * [ ] Set local env (`.sethrc`)
    * [ ] Deployer
      * [ ] Avoid using the same deployer for mainnet and testnet (to avoid deploying contracts with the same address but different sources)
      * [ ] `export ETH_PASSWORD=~/.env/password.txt`
      * [ ] `export ETH_KEYSTORE=~/.ethereum/keystore`
      * [ ] `export ETH_FROM=<address>`
    * [ ] Mainnet EIP1559
      * [ ] `export ETH_GAS=X`
        * [ ] Run `make estimate` and adjust `ETH_GAS` accordingly (e.g. 6000000)
      * [ ] `export ETH_GAS_PRICE=$(seth --to-wei X gwei)`
        * [ ] Check current gas price using `seth gas-price` and Set `ETH_GAS_PRICE` accordingly (e.g. 50 gwei)
          * [ ] Consider adding margin to account for spikes (e.g. current gas price 25 `gwei`, 50 `gwei` could be set)
      * [ ] `export ETH_PRIO_FEE=$(seth --to-wei X gwei)`
        * [ ] Check [current gas priority fee](https://etherscan.io/gastracker) and set `ETH_PRIO_FEE` accordingly (e.g. 2 gwei)
    * [ ] Mainnet RPC URL
      * [ ] `export ETH_RPC_URL=<url>`
    * [ ] Etherscan API KEY
      * [ ] `export ETHERSCAN_API_KEY=<key>`
    * [ ] Ensure to `source` the `.sethrc` file to make env vars available
  * [ ] Check local env
    * [ ] `seth ls`
    * [ ] `seth chain`
* [ ] Deploy spell on Goerli via `make deploy`
  * [ ] Ensure `src/test/config.sol` is edited correctly
    * [ ] `deployed_spell: address(<deployed_spell_address>)`
    * [ ] `deployed_spell_created: <timestamp>`
    * [ ] `deployed_spell_block: <block number>`
    * [ ] validate the above values via `make deploy-info tx=<tx_hash>`
  * [ ] Ensure spell is verified on etherscan
  * [ ] Ensure local tests PASS against deployed spell run via the deploy script
  * [ ] Push auto-generated commit
* [ ] Archive Spell via `make archive-spell` for current date or `date="YYYY-MM-DD" make archive-spell` (date as per target exec date)
* [ ] Commit & Push for Review
* [ ] Wait for CI to PASS
* [ ] Wait for at Least two Approvals to Share for Publishing to GovAlpha
* [ ] Share Deployed Address in `new-spells`
* [ ] Fill Spell Crafter Related Boxes in the Main Exec Doc Sheet
* [ ] Squash & Merge

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
