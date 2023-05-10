# Mainnet Exec Spell Review Checklist [PE-TODO]

## Mainnet

Spell Actions:

* TODO
* TODO

## Checklist
* [ ] Office Hours
  * [ ] On (Collateral Onboarding, Keepers, Integrations, ...)
  * [ ] matches exec doc
* [ ] Exec Hash
  * [ ] Run `make exec-hash` for current date, or `date=YYYY-MM-DD`
    * [ ] Executive vote file name and date is correct
    * [ ] [community](https://github.com/makerdao/community) repo commit hash corresponds to latest change
    * [ ] Raw GitHub URL is correct
    * [ ] Exec hash is correct
  * [ ] `description` date in `DssSpell.sol` matches exec copy date
* [ ] 30 Days Expiry
* [ ] `lib` (`git submodule update --init --recursive` to install/update)
  * [ ] `dss-exec-lib`
    * [ ] `DssExecLib.address` is unchanged by the spell PR (if changed, the new address must match the [Latest Release Tag](https://github.com/makerdao/dss-exec-lib/releases/latest) and be approved as a legitimate new release by GovAlpha)
    * [ ] if submodule upgrades are present make sure `dss-exec-lib` is synced as well
    * [ ] git submodule hash (run `git submodule status`) matches the latest release version or newer (NOTE: `dss-exec-lib` as installed locally will use GitHub code more recent than the 0.0.9 release)
  * [ ] `dss-test`
    * [ ] `dss-interfaces`
      * [ ] git submodule hash matches github master commit (Non-critical)
    * [ ] `forge-std`
      * [ ] git submodule hash matches github tag latest release version (Non-critical)
* [ ] `dss-interfaces`
  * [ ] used in the current spell
  * [ ] cleanup previous ones
  * [ ] ensure only single import layout is used (e.g. `import "dss-interfaces/dss/VatAbstract.sol";`)
* [ ] Static Interfaces
  * [ ] ensure they match `dss-interfaces` (Where there is a mismatch, use `cast interface` as the source of truth)
  * [ ] check on-chain interface of deployed contract via `cast interface <contract_address>` to ensure correctness
  * [ ] interface naming style should match with `Like` suffix (e.g. `VatLike`)
  * [ ] ensure they only list used functions in spell code
* [ ] Rates match
  * [ ] Compare against [IPFS](https://ipfs.io/ipfs/QmVp4mhhbwWGTfbh2BzwQB9eiBrQBKiqcPRZCaAxNUaar6)
  * [ ] Check manually via `make rates pct=<pct>` (e.g. pct=0.75, for 0.75%)
  * [ ] Variable name conforms to `X_PT_Y_Z_PCT_RATE` (e.g. `ZERO_PT_SEVEN_FIVE_PCT_RATE` for 0.75%)
  * [ ] Variable visibility declared as `internal`
  * [ ] State mutability declared as `constant`
* [ ] Math matches
  * [ ] Internal Precision
    * [ ] `WAD = 10 ** 18`
    * [ ] `RAY = 10 ** 27`
    * [ ] `RAD = 10 ** 45`
    * [ ] Ensure they match with [ds-math](https://github.com/dapphub/ds-math/blob/master/src/math.sol) and the [Numerical Ranges](https://github.com/makerdao/dss/wiki/Numerical-Ranges#notation)
    * [ ] Variable visibility declared as internal
    * [ ] State mutability declared as `constant`
  * [ ] Units
    * [ ] `HUNDRED  = 10 ** 2`
    * [ ] `THOUSAND = 10 ** 3`
    * [ ] `MILLION  = 10 ** 6`
    * [ ] `BILLION  = 10 ** 9`
    * [ ] Variable visibility declared as internal
    * [ ] State mutability declared as `constant`
    * [ ] Ensure they match with [config](https://github.com/makerdao/spells-mainnet/blob/master/src/test/config.sol)
  * [ ] Timestamps
    * [ ] Variable naming matches `MONTH_DD_YYYY` (e.g. `MAY_01_2023` for 2023-05-01)
    * [ ] Time of day makes logical sense (i.e. `11:59:59` on the last day of the month) in the context of exec actions
    * [ ] Variable visibility declared as `internal`
    * [ ] State mutability declared as `constant`
    * [ ] Ensure the timestamp [converts](https://www.epochconverter.com/) to the correct date
* [ ] Deployed Contracts (not on chainlog)
  * [ ] Verified on etherscan
  * [ ] Optimizations match Repo
  * [ ] `GNU AGPLv3` license
  * [ ] Constructor args ok (e.g. `vat`, `dai`, `dog`, ...)
    * [ ] Match [ChainLog](https://chainlog.makerdao.com/)
  * [ ] Wards ok (pause proxy relied, deployer denied)
    * [ ] `MCD_ESM` is relied (as approved by GovAlpha) in order to allow de-authing the pause proxy during Emergency Shutdown, via `denyProxy`.
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
* [ ] Risk Parameters Changes
  * [ ] `dog.ilk.hole` ([setIlkMaxLiquidationAmount](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L699))
  * [ ] `vat.ilk.dust`([setIlkMinVaultAmount](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L676))
  * [ ] `dog.ilk.chop` ([liquidationPenalty](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L689))
  * [ ] `jug.ilk.duty` ([setIlkStabilityFee](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L792))
  * [ ] `spotter.ilk.mat`  ([liquidationRatio](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L709))
  * [ ] `clip.buf`  ([startingPriceFactor](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L720))
  * [ ] `clipperMom.clip.tolerance` ([setLiquidationBreakerPriceTolerance](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L772))
  * [ ] `clip.tail` ([auctionDuration](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L731))
  * [ ] `clip.cusp` ([permittedDrop](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L740))
  * [ ] `clip.chip` ([kprPctReward](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L750))
  * [ ] `clip.tip`([kprFlatReward](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L760))
  * [ ] `calc.tau` ([setLinearDecrease](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L811))
  * [ ] [setStairstepExponentialDecrease](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L822)
    * [ ] `calc.cut`
    * [ ] `calc.step`
* [ ] Autoline Changes
  * [setIlkAutoLineParameters](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L648)
    * [ ] `ilk`
    * [ ] `line`
    * [ ] `gap`
    * [ ] `ttl`
  * [setIlkAutoLineDebtCeiling](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L658)
    * [ ] `ilk`
    * [ ] `line`
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
    * [ ] Timestamps match Doc (`bgn`, `fin`)
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
  * [ ] `Yank`
    * [ ] Stream ID is matches exec
    * [ ] `MCD_VEST_DAI` for DAI stream `yank`
    * [ ] `MCD_VEST_MKR_TREASURY` for MKR stream `yank`
  * [ ] Ensure Recipient Addresses match `addresses_wallets.sol`
* [ ] ChainLog
  * [ ] Bump ChainLog, accordingly with spec (major, minor, patch)
    * [ ] MAJOR -> New Vat
    * [ ] MINOR -> Core Module (DSS) Update (e.g. Flapper)
    * [ ] PATCH -> Collateral addition or addition/modification
* [ ] `addresses_mainnet.sol` matches spell code
* [ ] Ensure every spell variable is declared as public/internal
* [ ] Ensure `immutable` visibility is only used when fetching addresses from the `ChainLog` via `DssExecLib.getChangelogAddress` and `constant` is used instead for static addresses
* [ ] Spell actions match [GovAlpha Spell Content Sheet](https://docs.google.com/spreadsheets/d/1w_z5WpqxzwreCcaveB2Ye1PP5B8QAHDglzyxKHG3CHw) and hashed exec doc
* [ ] Tests PASS
  * [ ] Ensure Good Coverage
  * [ ] Ensure every test function is declared as public if enabled or private if disabled
* [ ] Local Tests and CI PASS
* [ ] Deployed Spell is Verified
  * [ ] Optimization Enabled: No
  * [ ] Other Settings: default evmVersion, GNU AGPLv3 license
* [ ] Deployed Spell Code matches GitHub
  * [ ] diffcheck etherscan source against spell PR (via `make diff-deployed-spell`)
* [ ] Deployed Spell Etherscan Checks
  * [ ] automated checks via `make check-deployed-spell`
    * [ ] verified
    * [ ] license type matches
    * [ ] solc version matches
    * [ ] optimizations are disabled
    * [ ] dss-exec-lib library address used (under 'Libraries Used') matches the hardcoded local `DssExecLib.address` file
    * [ ] `deployed_spell_created` matches deployment timestamp
    * [ ] `deployed_spell_block` matches deployment block number
  * [ ] manual checks
      * [ ] Ensure `make deploy-info tx=<tx>` matches [config](https://github.com/makerdao/spells-mainnet/blob/master/src/test/config.sol)
        * [ ] `deployed_spell_created` timestamp
        * [ ] `deployed_spell_block` block number
      * [ ] Ensure Etherscan `Libraries Used` matches DssExecLib [Latest Release](https://github.com/makerdao/dss-exec-lib/releases/latest)
       * [ ] git submodule hash matches [dss-exec-lib](https://github.com/makerdao/dss-exec-lib/releases/latest) latest release's tag commit and inspect diffs if doesn't match to ensure expected behaviour (Currently Non-Critical pending the next DssExecLib release, double check that exec lib used by the contract matches the latest release)
* [ ] Archive matches `src`
  * [ ] `make diff-archive-spell` for current date or or `date="YYYY-MM-DD" make diff-archive-spell` (date as per target exec date)
  * [ ] ensure date corresponds to target exec date
* [ ] Local Tests and CI PASS
