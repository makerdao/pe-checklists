# Goerli Executive Spell Review Checklist [PE-TODO]

## Goerli

Spell Actions:

* TODO
* TODO

## Checklist
* [ ] Office Hours
  * [ ] Off (default, spell casted after deploying, pause delay 60 sec.)
* [ ] Exec Hash
  * [ ] empty (default)
* [ ] 30 Days Expiry
* [ ] dss-interfaces
  * [ ] git submodule hash matches github master commit
  * [ ] used in the current spell
  * [ ] cleanup previous ones
  * [ ] if submodule upgrades are present make sure `dss-exec-lib` is synced as well
* [ ] Static Interfaces
  * [ ] ensure they match `dss-interfaces`
  * [ ] check on-chain interface of deployed contract via `cast interfaces <contract_address>` to ensure correctness
  * [ ] interface naming style should match with `Like` suffix (e.g. `VatLike`)
* [ ] Rates Match
  * [ ] Compare against [IPFS](https://ipfs.io/ipfs/QmVp4mhhbwWGTfbh2BzwQB9eiBrQBKiqcPRZCaAxNUaar6)
  * [ ] Calculate manually using `bc -l <<< 'scale=27; e( l(1.0X)/(60 * 60 * 24 * 365) )'` (replace X with %, e.g. 1 = 1%)
  * [ ] Variable visibility declared as internal
* [ ] Math Match
  * [ ] Internal Precision
    * [ ] `WAD = 10**18`
    * [ ] `RAY = 10**27`
    * [ ] `RAD = 10**45`
    * [ ] Ensure they match with [ds-math](https://github.com/dapphub/ds-math/blob/master/src/math.sol) and the [Numerical Ranges](https://github.com/makerdao/dss/wiki/Numerical-Ranges#notation)
    * [ ] Variable visibility declared as internal
  * [ ] Units
    * [ ] `HUNDRED  = 10**2`
    * [ ] `THOUSAND = 10**3`
    * [ ] `MILLION  = 10**6`
    * [ ] `BILLION  = 10**9`
    * [ ] Ensure they match with [config](https://github.com/makerdao/spells-mainnet/blob/master/src/test/config.sol)
    * [ ] Variable visibility declared as internal
* [ ] Deployed Contracts
  * [ ] Verified on etherscan
  * [ ] Optimizations match Repo
  * [ ] `GNU AGPLv3` license
  * [ ] Constructor args ok (e.g. `vat`, `dai`, `dog`, ...)
    * [ ] Match [ChainLog](https://chainlog.makerdao.com/)
  * [ ] Wards ok (pause proxy relied, deployer denied)
  * [ ] Matches corresponding github source code (i.e. diffcheck via vscode `code --diff etherscan.sol github.sol`)
  * [ ] Ensure deployer address is included into `addresses_deployers.sol` (**to keep up to date**)
* [ ] External Contracts Calls (e.g. Starknet)
  * [ ]  Target Contract don't block spell execution
  * [ ]  External call is NOT delegate call
  * [ ]  Target Contract doesn't have permissions on the Vat
  * [ ]  Target Contract doesn't do anything untoward (e.g. interacting with unsafe contracts)
  * [ ]  MCD Pause Proxy doesn't give any approvals
  * [ ]  All possible actions of the Target Contract are documented
  * [ ]  Target contract is not upgradable
  * [ ]  Target Contract is included in the ChainLog
  * [ ]  Test Coverage is comprehensive
* [ ] Onboarding
  * [ ] [Collateral Onboarding](./collateral-onboarding-checklist.md)
  * [ ] [RWA Onboarding](./rwa-onboarding-checklist.md)
  * [ ] [Teleport Onboarding](./teleport-onboarding-checklist.md)
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
    * [ ] Sanity Check `pip` must be set (not the zero address)
    * [ ] `ilk` follows format "RWAXXX-A"
    * [ ] `val` price ignored (`0`) if `init` has already been called
    * [ ] `doc` new legal document (IPFS HASH) matches Doc (or Forum Post)
    * [ ] `tau` parameter used is the old `tau` value
  * [ ] Autoline (`line`) + Liquidation Oracle Price Bump (`val`)
    * [ ] Enable Autoline
      * [ ] `ilk` follows format "RWAXXX-A"
      * [ ] `line` (max debt ceiling)
      * [ ] `gap`
      * [ ] `ttl`
    * [ ] `bump` `RwaLiquidationOracle` with new computed increased price (`val`)
      * [ ] ensure `val` is set accordingly with autoline max debt ceiling (`line`)
      * [ ] `val` should enable DAI to be drawn over the loan period while taking into
            account the configured `ink` amount, interest rate and liquidation ratio
    * [ ] Poke `spotter` to pull in the new price
  * [ ] Debt Ceiling (`line`) + Liquidation Oracle Price Bump (`val`)
    * [ ] Increase Ilk Debt Ceiling (set DC + increase Global DC)
    * [ ] `bump` `RwaLiquidationOracle` with new computed increased price (`val`)
      * [ ] `val` should enable DAI to be drawn over the loan period while taking into
            account the configured `ink` amount, interest rate and liquidation ratio
    * [ ] Poke `spotter` to pull in the new price
* [ ] Payments
  * [ ] Streams (`DssVest`)
    * [ ] `DssVest` Interface is correct
    * [ ] Ensure that `cap` > max new vest `tot`/`tau` otherwise file cap as well
    * [ ] Timestampts match Doc (`bgn`, `fin`)
    * [ ] CUs Addresses match Doc (`usr`)
    * [ ] Amount matches Doc (`tot`, if decimals are present consider using `ether`)
    * [ ] Vesting Duration matches Doc (`tau`)
    * [ ] Cliff Duration matches Doc (`eta`)
    * [ ] Restricted (by default)
    * [ ] Manager match Doc (`mgr`, set to zero for DAI streams by default)
    * [ ] IF [DssVestTransferrable](https://github.com/makerdao/dss-vest/blob/master/src/DssVest.sol#L463) is used
      * [ ] Ensure `DssVestTransferrable` allowance is increased by new vesting delta (by approving the `transferrable` vest contract to allowance + new total amount streamed)
  * [ ] CUs MKR Transfers
    * [ ] Recipient Addresses match Doc
    * [ ] Transfers Amounts match Doc
    * [ ] Follows Previous Patterns
  * [ ] Direct SB DAI Payment
    * [ ] Recipient Addresses match Doc
    * [ ] Payment Amounts match Doc
    * [ ] Follow Previous Patterns
  * [ ] Ensure Recipient Addresses match `addresses_wallets.sol`
* [ ] ChainLog
  * [ ] Bump ChainLog, accordingly with spec (major, minor, patch)
    * [ ] MAJOR -> New Vat
    * [ ] MINOR -> Core Module (DSS) Update (e.g. Flapper)
    * [ ] PATCH -> Collateral addition or addition/modification
* [ ] `addresses_goerli.sol` matches spell code
* [ ] Ensure every spell variable is declared as public/internal
* [ ] Spell actions match [GovAlpha Spell Content Sheet](https://docs.google.com/spreadsheets/d/1w_z5WpqxzwreCcaveB2Ye1PP5B8QAHDglzyxKHG3CHw)
* [ ] Tests PASS
  * [ ] Ensure Good Coverage
  * [ ] Ensure every test function is declared as public if enabled or private if disabled
* [ ] Local Tests and CI PASS
* [ ] Deployed Spell is Verified
  * [ ] Optimization Enabled: No
  * [ ] Other Settings: default evmVersion, GNU AGPLv3 license
* [ ] Deployed Spell Code matches GitHub
  * [ ] diffcheck etherscan source against spell PR (i.e. via vscode `code --diff etherscan.sol github.sol`)
* [ ] Deploy Timestamp & Block Number Match
  * [ ] Ensure `make deploy-info tx=<tx>` matches [config](https://github.com/makerdao/spells-mainnet/blob/master/src/test/config.sol)
    * [ ] `deployed_spell_created` timestamp
    * [ ] `deployed_spell_block` block number
* [ ] Ensure Etherscan `Libraries Used` matches DssExecLib [Latest Release](https://github.com/makerdao/dss-exec-lib/releases/latest)
  * [ ] git submodule hash matches [dss-exec-lib](https://github.com/makerdao/dss-exec-lib) latest release's tag commit
* [ ] Local Tests and CI PASS
* [ ] Spell is Cast (**only on Goerli**)
  * [ ] Check Cast Trace (via [EthTx](https://ethtx.info/))
    * [ ] Ensure no reverts are present that block execution
      * [ ] Inspect low level call reverts if expected
    * [ ] Ensure all actions are executed and no out-of-gas errors are present
* [ ] Archive matches `src`
  * `make diff-archive-spell`
