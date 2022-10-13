# Mainnet Exec Spell Review Checklist [PE-TODO]

PR: https://github.com/makerdao/spells-mainnet/pull/TODO

Spell Crafter: 
Reviewers:

Spell Actions:

* TODO
* TODO

## Checklist
* [ ] Office Hours
  * [ ] On (Collateral Onboarding, Keepers, Integrations, ...)
  * [ ] matches exec doc
* [ ] Exec Hash
  * [ ] `community` commit is the latest change or merge commit
  * [ ] `community` repo url matches
  * [ ] script matches
* [ ] 30 Days Expiry
  * [ ] weekly
  * [ ] monthly (discarded)
* [ ] Pragma OK
  * [ ] Collateral Onboarding
    * [ ] `pragma experimental ABIEncoderV2;` (Only IF `DssExecLib.addNewCollateral` is used)
* [ ] dss-interfaces
  * [ ] submodule matches github master commit
  * [ ] used in the current spell
  * [ ] cleanup previous ones
  * [ ] if dapp upgrade changes are present make sure dss-exec-lib is synced as well
* [ ] Rates OK
  * [ ] [IPFS](https://ipfs.io/ipfs/QmVp4mhhbwWGTfbh2BzwQB9eiBrQBKiqcPRZCaAxNUaar6)
  * [ ] `bc -l <<< 'scale=27; e( l(1.0X)/(60 * 60 * 24 * 365) )'` (replace X with %, e.g. 1 = 1%)
* [ ] Math OK
  * [ ] Internal Precision
    * [ ] `WAD = 10**18`
    * [ ] `RAY = 10**27`
    * [ ] `RAD = 10**45`
    * [ ] Make sure they match with [ds-math](https://github.com/dapphub/ds-math/blob/master/src/math.sol) and the [Numerical Ranges](https://github.com/makerdao/dss/wiki/Numerical-Ranges#notation)
  * [ ] Units
    * [ ] `HUNDRED  = 10**2`
    * [ ] `THOUSAND = 10**3`
    * [ ] `MILLION  = 10**6`
    * [ ] `BILLION  = 10**9`
    * [ ] Make sure they match with [config](https://github.com/makerdao/spells-mainnet/blob/master/src/test/config.sol)
* [ ] Deployed Contracts
  * [ ] Verified on etherscan
  * [ ] Optimizations Match Repo
  * [ ] `GNU AGPLv3` license
  * [ ] Constructor args ok (e.g. `vat`, `dai`, `dog`, ...)
    * [ ] Match [ChainLog](https://chainlog.makerdao.com/)
  * [ ] Wards ok (pause proxy relied, deployer denied)
  * [ ] Matches corresponding github source code (i.e. diffchecked using [diffchecker](https://www.diffchecker.com/))
* [ ] External Contracts Calls (e.g. Starknet)
  * [ ]  Target Contract don't block spell execution
  * [ ]  External call is NOT delegate call
  * [ ]  Target Contract doesn't have permissions on the Vat
  * [ ]  Target Contract doens't do anything untoward (e.g. interacting with unsafe contracts)
  * [ ]  MCD Pause Proxy doesn't give any approvals
  * [ ]  All possible actions of the Target Contract are documented
  * [ ]  Target Contract is included in the ChainLog
  * [ ]  Test Coverage is comprehensive
* [ ] Onboarding
  * [ ] [Collateral Onboarding](./collateral-onboarding-checklist.md)
  * [ ] [RWA Onboarding](./rwa-onboarding-checklist.md)
* [ ] Offboarding (Lerp `mat`)
  * [ ] 1st Stage Spell Actions
    * [ ] Remove Ilk from Autoline
    * [ ] Set Ilks Debt Ceilings to `0`
    * [ ] Cache Ilks `line` to Reduce in the Global Debt Ceiling
    * [ ] Decrease Global Debt Ceiling by Total Amount of Offboarded Ilks `line` Cached
  * [ ] 2nd Stage Spell Actions
    * [ ] Set Ilk Liquidation Penalty `chop` to `0`
    * [ ] Set Keeper Incentive Flat Rate `tip` to `0`
    * [ ] Check IF `chip` is required to be adjusted as well
    * [ ] Use `DssExecLib.linearInterpolation`
      * [ ] `name` Format matches "XXX-A Offboarding"
      * [ ] `target` matches `spotter`
      * [ ] `ilk` Format matches "XXX-A"
      * [ ] `what` matches `mat`
      * [ ] `startTime` matches `block.timestamp`
      * [ ] `start` matches Var `CURRENT_XXX_A_MAT`
      * [ ] `end` matches Var `TARGET_XXX_A_MAT` (Mach Exec Doc & Risk Computations)
        * [ ] Check IF Target `mat` Covers All Remaining Vaults CR times Risk Multiplier Factor
      * [ ] `duration` matches Exec Doc
* [ ] RWA Updates
  * [ ] `doc`
    * [ ] `init` the `RwaLiquidationOracle` to reset the `doc`
    * [ ] Sanity Check `pip` must be set (different than zero address)
    * [ ] `ilk` follows format "RWAXXX-A"
    * [ ] `val` price ignored (`0`) if `init` has already been called
    * [ ] `doc` new legal document (IPFS HASH) matches Doc (or Forum Post)
    * [ ] `tau` old `tau` value
  * [ ] Debt Ceiling (`line`) + Liquidation Oracle Price Bump
    * [ ] Increase Ilk Debt Ceiling (set DC + increase Global DC)
    * [ ] `bump` `RwaLiquidationOracle` with new computed increased price (`val`)
      * [ ] `val` should enable DAI to be drawn over the loan period while taking into
            account the configured `ink` amount, interest rate and liquidation ratio
    * [ ] Poke `spotter` to pull in the new price
* [ ] Payments
  * [ ] Streams (`DssVest`)
    * [ ] `DssVest` Interface is correct
    * [ ] Ensure that `cap` > max new vest `tot`/`tau` otherwise file cap as well
    * [ ] Timestampts Match Doc (`bgn`, `fin`)
    * [ ] CUs Addresses Match Doc (`usr`)
    * [ ] Amount matches Doc (`tot`, if decimals are present consider using `ether`)
    * [ ] Vesting Duration matches Doc (`tau`)
    * [ ] Cliff Duration matches Doc (`eta`)
    * [ ] Restricted (by default)
    * [ ] Manager matches Doc (`mgr`, set to zero in Dai Streams by default)
  * [ ] CUs MKR Transfers
    * [ ] Amounts and Addresses Match Doc
    * [ ] MKR `DssVestTransferrable` Allowance Match Total Transfer Amounts
    * [ ] Follows Previous Patterns
  * [ ] Direct SB DAI Payment
    * [ ] Amounts and Addresses Match Doc
    * [ ] Follow Previous Patterns
* [ ] ChainLog
  * [ ] Bump ChainLog, accordingly with spec (major, minor, patch)
    * [ ] MAJOR -> New Vat
    * [ ] MINOR -> Core Module (DSS) Update (e.g. Flapper)
    * [ ] PATCH -> Collateral addition or addition/modification
* [ ] `addresses_mainnet.sol` Matches
* [ ] Spell Actions Look Good
* [ ] Tests Look Good
* [ ] Deployed Spell Verified
  * [ ] Optimization Enabled: No
  * [ ] Other Settings: default evmVersion, GNU AGPLv3 license
* [ ] Deployed Spell Code Matches GitHub
  * [diffcheker](diffchecker) agaist spell pr (ethescan code matches pr)
* [ ] Dss-Exec-Lib [Latest Release](https://github.com/makerdao/dss-exec-lib/releases/latest)
  * submoudule matches [dss-exec-lib](https://github.com/makerdao/dss-exec-lib) tag commit
* [ ] Archive matches `src`
  * `make diff-archive-spell`
