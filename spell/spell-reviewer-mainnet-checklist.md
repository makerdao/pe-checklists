# Mainnet Executive Spell Review Checklist

## Development Stage

* Exec Doc
  * [ ] Record target date for the executive spell
    _Insert taget date here in the YYYY-MM-DD format_
  * [ ] Exec Sheet for the specified date is found in the ["Executive Vote Implementation Process" google sheet](https://docs.google.com/spreadsheets/d/1w_z5WpqxzwreCcaveB2Ye1PP5B8QAHDglzyxKHG3CHw)
    _Insert URL to the specific sheet here_
  * [ ] Exec Doc for the specified date is found in the [`makerdao/community` GitHub repo](https://github.com/makerdao/community/tree/master/governance/votes)
  * [ ] Exec Doc file name follows the format `Executive vote - Month DD, YYYY.md`
  * [ ] Extract permanent URL to the raw markdown file and paste it below
    _Insert your Raw Exec Doc URL here_
  * [ ] Using Exec Doc URL from the above and the target date, generate Exec Doc Hash via `make exec-hash date=$date $URL`
    _Insert your Exec Doc Hash here_
  * [ ] Using Exec Doc URL from the above, generate Exec Doc Hash via `cast keccak -- "$(curl '$URL' -o - 2>/dev/null)"`
    _Insert your Exec Doc Hash here_
  * [ ] Using Exec Doc URL from the above, read spell instructions from the Exec Doc and list them below
    _List all instructions announced in the Exec Doc_
  * [ ] Ensure that instructions announced in the Exec Doc match instructions in the Exec Sheet
* Base checks
  * [ ] Current solc version `0.8.16`
  * [ ] Office hours is `true` IF spell introduces a major change that can affect external parties (e.g.: keepers are affected in case of collateral offboarding) OTHERWISE explicitely set to `false`
  * [ ] Office hours value matches the Exec Sheet
  * [ ] Office hours value matches the Exec Doc
  * [ ] 30 days spell expiry set in the constructor (`block.timestamp + 30 days`)
* Spell description
  * [ ] Description follows the format `TARGET_DATE MakerDAO Executive Spell | Hash: EXEC_DOC_HASH`
  * [ ] Target date in the description matches the Exec Doc target date
  * [ ] Accompanying comment above spell description follows the format `// Hash: cast keccak -- "$(wget 'EXEC_DOC_URL' -q -O - 2>/dev/null)"`
  * [ ] Exec Doc URL in comment matches your Raw Exec Doc URL above
  * [ ] Exec Doc URL in comment refers to the [`https://github.com/makerdao/community` repo](https://github.com/makerdao/community/tree/master/governance/votes)
* Comments inside the spell
  * [ ] Every _Section text_ from the Exec Sheet is copied to the spell code as a comment surrounded by the set of dashes (E.g. `// ----- Section text -----`)
  * [ ] Every _Instruction text_ from the Exec Sheet is copied to the spell code as `// Instruction text`
  * [ ] Every _Instruction text_ have newline above it
  * [ ] Every _Instruction text_ have a _single_ action below it
  * [ ] IF an instruction can not be taken, it should have explanation under the instruction prefixed with `// Note:` (e.g.: `// Note: Payments are skipped on goerli`)
  * [ ] IF action in the spell doesn't have relevant instruction (e.g.: `chainlog` version bump), the necessity of it is explained in the comment above prefixed with `// Note:`
  * [ ] Every proof url from the Exec Sheet, such as `Reasoning URL` and `Authority URL` is present in the spell code under relevant section or instruction (depending on which row the url is present)
  * [ ] Every proof url from the Exec Sheet, such as `Reasoning URL` and `Authority URL` have prefix derived from the url itself
      * `// Executive Vote:` if URL starts with `https://vote.makerdao.com/executive/`
      * `// Poll:` if URL starts with `https://vote.makerdao.com/polling/`
      * `// Forum:` if URL starts with `https://forum.makerdao.com/t/`
      * `// MIP:` if URL starts with `https://mips.makerdao.com/mips/details/`
* Dependency checks
  * [ ] Update Foundry by running `foundryup`
  * [ ] Reinstall libraries by running `rm -rf ./lib && git submodule update --init --recursive`
    ```bash
    Insert checked out submodule paths here
    ```
  * [ ] IF submodule upgrades are present, make sure `dss-exec-lib` is synced as well
  * [ ] git submodule hash of `dss-exec-lib` (run `git submodule status`) matches the [latest release version](https://github.com/makerdao/dss-exec-lib/releases) or newer
  * [ ] `dss-interfaces` library used inside `lib/dss-exec-lib` matches submodule used inside `lib/dss-test`
* IF interfaces are present in the spell
  * Interfaces imported from `dss-interfaces`
    * [ ] No unused `dss-interfaces`
    * [ ] Only single import layout is used (e.g. `import "dss-interfaces/dss/VatAbstract.sol";`)
  * Static Interfaces
    * [ ] No unused static interfaces
    * [ ] Declared static interface not present in the `dss-interfaces`, OTHERWISE should be imported from there
    * [ ] Interface matches deployed contract using `cast interface <contract_address>` command
    * [ ] Interface naming style should match with `Like` suffix (e.g. `VatLike`)
      * EXCEPTION: [known interface naming exceptions](https://github.com/makerdao/dss-exec-lib/blob/master/src/DssExecLib.sol#L24-L52)
    * [ ] Each static interface declare only functions actually used in the spell code
* IF variable declarations are present in the spell
  * IF precision units are present
    * [ ] Precision units used in the spell match their defined values:
      * `WAD = 10 ** 18`
      * `RAY = 10 ** 27`
      * `RAD = 10 ** 45`
    * [ ] Precision units match with [Numerical Ranges](https://github.com/makerdao/dss/wiki/Numerical-Ranges#notation)
    * [ ] Each variable visibility declared as `internal`
    * [ ] Each variable state mutability declared as `constant`
  * IF math units are present
    * [ ] Match their defined values:
      * `HUNDRED  = 10 ** 2`
      * `THOUSAND = 10 ** 3`
      * `MILLION  = 10 ** 6`
      * `BILLION  = 10 ** 9`
    * [ ] Match with [config](https://github.com/makerdao/spells-mainnet/blob/master/src/test/config.sol)
    * [ ] Each variable visibility declared as `internal`
    * [ ] Each variable state mutability declared as `constant`
  * IF rates are present
    * [ ] Rates match generated locally via `make rates pct=<pct>` (e.g. pct=0.75, for 0.75%)
    * [ ] Rates match [IPFS](https://ipfs.io/ipfs/QmVp4mhhbwWGTfbh2BzwQB9eiBrQBKiqcPRZCaAxNUaar6) document
    * [ ] Rate variable name conforms to `X_PT_Y_Z_PCT_RATE` (e.g. `ZERO_PT_SEVEN_FIVE_PCT_RATE` for 0.75%)
    * [ ] Rate variable visibility declared as `internal`
    * [ ] Rate variable state mutability declared as `constant`
  * IF timestamps are present
    * [ ] Comment above timestamp states full date including `UTC` timezone
    * [ ] Timestamp [converts](https://www.epochconverter.com/) back to the correct date
    * [ ] Timestamp [converts](https://www.epochconverter.com/) back to the `UTC` timezone
    * [ ] Variable naming matches `MMM_DD_YYYY` (e.g. `JAN_01_2023` for 2023-01-01)
    * [ ] Time of day makes logical sense in the context of timestamp usage (i.e. `23:59:59 UTC` for the final day of something, `00:00:00 UTC` for the first day of something)
    * [ ] Each variable visibility declared as `internal`
    * [ ] Each variable state mutability declared as `constant`
* IF new contract is present in the spell (not yet on chainlog or new to chainlog)
  * [ ] Source code is verified on etherscan
  * [ ] Compilation optimizations match deployment settings defined in the source code repo
  * [ ] `GNU AGPLv3` license
  * [ ] Every maker-related constructor argument matches chainlog (e.g. `vat`, `dai`, `dog`, ...)
  * IF new contract have concept of `wards` or access control
    * [ ] Ensure `PAUSE_PROXY` address was `relied` (`wards(PAUSE_PROXY)` is `1`)
    * [ ] Ensure that contract deployer address was `denied` (`wards(deployer)` is `0`)
    * [ ] Ensure `MCD_ESM` address is already relied OR being `relied` (`wards(MCD_ESM)` is `1`) in this spell (as approved by Governance Facilitators, in order to allow de-authing the pause proxy during Emergency Shutdown, via `denyProxy`)
  * [ ] Source code matches corresponding github source code (e.g. diffcheck via vscode `code --diff etherscan.sol github.sol`)
  * [ ] Deployer address is included into `addresses_deployers.sol`
* IF core system parameter changes are present in the instructions
  * IF stability fee (`jug.ilk.duty`) is updated
    * [ ] ([`DssExecLib.setIlkStabilityFee(ilk, rate, doDrip)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L792)) is used
    * [ ] Comment matches pattern `// Increase ILK-A Stability Fee by X.XX% from X.XX% to X.XX%`
  * IF Dai Savings Rate (`pot.dsr`) is updated
    * [ ] ([`DssExecLib.setDSR(rate, doDrip)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L455)) is used
    * [ ] Comment matches pattern `// Increase DSR by X.XX% from X.XX% to X.XX%`
    * [ ] Double check that rate match `make rates pct=<pct>` (e.g. pct=0.75, for 0.75%)
    * [ ] Double check that rate match [IPFS](https://ipfs.io/ipfs/QmVp4mhhbwWGTfbh2BzwQB9eiBrQBKiqcPRZCaAxNUaar6) document
  * [ ] IF `spotter.ilk.mat` is updated, ([`DssExecLib.setIlkLiquidationRatio(ilk, pct_bps)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L709)) is used
  * [ ] IF `dog.ilk.hole` is updated, ([`DssExecLib.setIlkMaxLiquidationAmount(ilk, amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L699)) is used
  * [ ] IF `vat.ilk.dust` is updated, ([`DssExecLib.setIlkMinVaultAmount(ilk, amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L676)) is used
  * [ ] IF `dog.ilk.chop` is updated, ([`DssExecLib.setIlkLiquidationPenalty(ilk, pct_bps)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L689)) is used
  * [ ] IF `clip.buf` is updated, ([`DssExecLib.setStartingPriceMultiplicativeFactor(ilk, pct_bps)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L720)) is used
  * [ ] IF `clipperMom.clip.tolerance` is updated, ([`DssExecLib.setLiquidationBreakerPriceTolerance(clip, pct_bps)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L772)) is used
  * [ ] IF `clip.tail` is updated, ([`DssExecLib.setAuctionTimeBeforeReset(ilk, duration)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L731)) is used
  * [ ] IF `clip.cusp` is updated, ([`DssExecLib.setAuctionPermittedDrop(ilk, pct_bps)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L740)) is used
  * [ ] IF `clip.chip` is updated, ([`DssExecLib.setKeeperIncentivePercent(ilk, pct_bps)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L750)) is used
  * [ ] IF `clip.tip` is updated, ([`DssExecLib.setKeeperIncentiveFlatRate(ilk, amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L760)) is used
  * [ ] IF `calc.tau` is updated, ([`DssExecLib.setLinearDecrease(calc, duration)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L811)) is used
  * [ ] IF `calc.cut` or `calc.step` are updated, [`DssExecLib.setStairstepExponentialDecrease(calc, duration, pct_bps)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L822) is used
* IF debt ceiling changes are present in the instructions
  * IF adjusted collateral type (`ilk`) have [AutoLine](https://github.com/makerdao/dss-auto-line/tree/master) enabled (`MCD_IAM_AUTO_LINE`)
    * IF collateral debt ceiling requested to be `0`
      * [ ] Collateral is removed from AutoLine (`MCD_IAM_AUTO_LINE`) via [`DssExecLib.removeIlkFromAutoLine(ilk)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L668)
      * [ ] The instruction to remove from AutoLine (`MCD_IAM_AUTO_LINE`) is present in the Exec Sheet
      * [ ] Collateral debt ceiling is set to `0` via [`DssExecLib.setIlkDebtCeiling(ilk, amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L611)
      * [ ] Global debt ceiling (`vat.Line`) is updated accordingly, UNLESS specifically instructed not to
    * IF `AutoLine` parameters are updated
      * [ ] Either is used, depending on the instruction:
        * [`DssExecLib.setIlkAutoLineDebtCeiling(ilk, amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L658)
        * [`DssExecLib.setIlkAutoLineParameters(ilk, amount, gap, ttl)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L648)
  * IF collateral debt ceiling (`vat.ilk.line`) is updated
    * [ ] Collateral type (`ilk`) have [`AutoLine`](https://github.com/makerdao/dss-auto-line/tree/master) disabled previously or in the spell
    * [ ] Either is used, depending on the instruction:
        * [`DssExecLib.increaseIlkDebtCeiling(ilk, amount, global)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L621C14-L621C36)
        * [`DssExecLib.decreaseIlkDebtCeiling(ilk, amount, global)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L634)
        * [`DssExecLib.setIlkDebtCeiling(ilk, amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L611)
    * [ ] Global debt ceiling (`vat.Line`) is updated accordingly, UNLESS specifically instructed not to, via either:
        * `global` set to `true` in `increaseIlkDebtCeiling`/`decreaseIlkDebtCeiling`
        * [`DssExecLib.setGlobalDebtCeiling(amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L428)
        * [`DssExecLib.increaseGlobalDebtCeiling(amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L436)
        * [`DssExecLib.decreaseGlobalDebtCeiling(amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L445C14-L445C39)
* IF onboarding is present
  * [ ] Insert and follow relevant checklists here
    * [Collateral Onboarding](./collateral-onboarding-checklist.md)
    * [RWA Onboarding](./rwa-onboarding-checklist.md)
    * [Teleport Onboarding](./teleport-onboarding-checklist.md)
* IF collateral offboarding is present in the spell
  * 1st stage collateral offboarding
    * [ ] Collateral type (`ilk`) is removed from AutoLine (`MCD_IAM_AUTO_LINE`) IF currently enabled
    * [ ] Collateral debt ceiling (`vat.ilk.line`) is set to `0`
    * [ ] Global debt ceiling (`vat.Line`) decreased by the total amount of offboarded ilks
  * 2nd stage collateral offboarding
    * [ ] All actions from the 1st stage offboarding are previously taken
    * [ ] Collateral liquidation penalty (`chop`) is set to `0` IF requested by the governance
    * [ ] Flat keeper incentive (`tip`) is set to `0` IF requested by the governance
    * [ ] Relative keeper incentive (`chip`) is set to `0` IF requested by the governance
    * [ ] Max liquidation amount (`hole`) is adjusted via [`DssExecLib.setIlkMaxLiquidationAmount(ilk, amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L699) IF requested by the governance
    * [ ] Relevant clipper contract (`MCD_CLIP_`) is active (i.e. [`stopped`](https://github.com/makerdao/dss/blob/fa4f6630afb0624d04a003e920b0d71a00331d98/src/clip.sol#L97) is `0`)
    * [ ] Liquidations are triggered via (depending on the governance instruction):
      * EITHER liquidation ratio (`spotter.ilk.mat`) being set very high in the spell (using `DssExecLib.setValue(DssExecLib.spotter(), ilk, "mat", ratio)`)
      * OR via enabling linear interpolation ([`DssExecLib.linearInterpolation(name, target, ilk, what, startTime, start, end, duration)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L1096-L1112))
        * [ ] Ensure `name` format matches "XXX-X Offboarding"
        * [ ] Ensure `target` matches `DssExecLib.spotter()` address
        * [ ] Ensure `ilk` format matches collateral type (`ilk`) name (`"XXX-X"`)
        * [ ] Ensure `what` matches string `"mat"`
        * [ ] Ensure `startTime` matches `block.timestamp`
        * [ ] Ensure `start` uses variable `CURRENT_XXX_A_MAT`
        * [ ] Ensure `start` matches current `spotter.ilk.mat` value
        * [ ] Ensure `end` uses variable `TARGET_XXX_A_MAT`
        * [ ] Ensure `end` value matches the instruction
        * [ ] Ensure `end` allows liquidation of all remaining vaults (`end` is bigger than `collateral_type_collateralization_ratio * risk_multiplier_factor`)
        * [ ] Ensure `duration` matches the instruction
    * [ ] Spotter price is updated via [`DssExecLib.updateCollateralPrice(ilk)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L374) IF collateral have no running oracle (i.e. relevant `PIP_` contract have outdated `zzz` value)
    * [ ] Spotter price is updated after all other actions
    * [ ] Offboarding is tested at least via [`_checkIlkClipper` helper](https://github.com/makerdao/spells-mainnet/blob/7400e91c4f211fc24bd4d3a95a86416afc4df9d1/src/DssSpell.t.base.sol#L856)
* IF RWA updates are present
  * IF `doc` is updated
    * [ ] [`_updateDoc` helper](https://github.com/makerdao/spells-mainnet/blob/7400e91c4f211fc24bd4d3a95a86416afc4df9d1/archive/2023-09-27-DssSpell/DssSpell.sol#L76-L87) is copied one-to-one from the archive and defined above `actions`
    * [ ] `_updateDoc(ilk, doc)` is called in the spell
  * IF debt ceiling is updated
    * IF AutoLine update is requested by the Exec Doc
      * [ ] Parameters are set via [`DssExecLib.setIlkAutoLineParameters(ilk, amount, gap, ttl)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L648) or [`DssExecLib.setIlkAutoLineDebtCeiling(ilk, amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L658)
    * IF regular debt ceiling (`vat.ilk.line`) update is requested by the Exec Doc
      * [ ] Collateral type (`ilk`) have [`AutoLine`](https://github.com/makerdao/dss-auto-line/tree/master) disabled previously or in the spell
      * [ ] Debt ceiling (`vat.ilk.line`) is updated, via either:
          * [`DssExecLib.increaseIlkDebtCeiling(ilk, amount, global)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L621C14-L621C36)
          * [`DssExecLib.decreaseIlkDebtCeiling(ilk, amount, global)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L634)
          * [`DssExecLib.setIlkDebtCeiling(ilk, amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L611)
      * [ ] Global debt ceiling (`vat.Line`) is updated accordingly, UNLESS specifically instructed not to, via either:
          * `global` set to `true` in `increaseIlkDebtCeiling`/`decreaseIlkDebtCeiling`
          * [`DssExecLib.setGlobalDebtCeiling(amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L428)
          * [`DssExecLib.increaseGlobalDebtCeiling(amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L436)
          * [`DssExecLib.decreaseGlobalDebtCeiling(amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L445C14-L445C39)
    * [ ] Liquidation oracle price is bumped via `RwaLiquidationOracleLike(MIP21_LIQUIDATION_ORACLE).bump(ilk, val)` pattern
      * [ ] Comment above `bump` explains `val` computation via `// Note: the formula is: "debt_ceiling * [ (1 + rwa_stability_fee ) ^ (minimum_deal_duration_in_years) ] * liquidation_ratio"`
      * [ ] Comment  above `bump` provides locally executable formula (e.g. `// bc -l <<< 'scale=18; 50000000 * e(l(1.07) * (3342/365)) * 1.00' | cast --to-wei`)
        * [ ] The formula matches the example provided above
        * [ ] `debt_ceiling` in the executable formula matches new debt ceiling set in the spell or the maximum possible debt ceiling in case of the enabled AutoLine
        * [ ] `rwa_stability_fee` in the executable formula matches stability fee of the specified RWA found on chain
        * [ ] `minimum_deal_duration_in_years` in the executable formula matches number found in the Exec Doc of the spell containing relevant RWA onboarding
        * [ ] `liquidation_ratio` in the executable formula matches liquidation ratio of the specified RWA found on chain
        * [ ] Executing formula locally provides integer number that matches the `val` in the spell
      * [ ] `val` makes sense in context of the [rate mechanism](https://github.com/makerdao/developerguides/blob/master/mcd/intro-rate-mechanism/intro-rate-mechanism.md)
    * [ ] IF multiple RWA ilks are being combined into one, `val` calculation is done once per ilk and added to make the total, with separate executable formulas provided in comments. The existing `val` value can be retrieved by calling `read()` on `PIP_RWAXX` and converting the result into decimal
    * [ ] Oracle price is updated via `DssExecLib.updateCollateralPrice(ilk)`
    * IF debt ceiling is set to `0` OR soft liquidation explicitely requested to be triggered (`tell`)
        * [ ] `RwaLiquidationOracle.tell(ilk)` call is present
        * [ ] IF `RWAXX_A_INPUT_CONDUIT` is an instance of [`TinlakeMgr`](https://github.com/centrifuge/tinlake-maker-lib/blob/master/src/mgr.sol) (it is a Centrifuge integration), additional `TinlakeMgr.tell()` call is present (in order to prevent further `TIN` redemptions in the Centrifuge pool)
* IF payments are present in the spell
  * IF `MKR` transfers are present
    * [ ] Recipient address in the instruction is in the checksummed format
    * [ ] Recipient address matches Exec Doc
    * [ ] Transfer amount matches Exec Doc
    * [ ] Transfer amount is specified with (at least) 2 decimals using `ether` keyword
    * [ ] IF `ether` keyword is used, comment is present on the same line `// Note: ether is a keyword helper, only MKR is transferred here`
    * [ ] The transfers are tested via `testMKRPayments` test
    * [ ] Sum of all MKR transfers tested in `testMKRPayments` matches number in the Exec Doc
  * IF `DAI` surplus buffer transfers are present
    * [ ] Recipient address in the instruction is in the checksummed format
    * [ ] Recipient address matches Exec Doc
    * [ ] Transfer amount matches Exec Doc
    * [ ] The transfers are tested via `testDAIPayments` test
    * [ ] Sum of all DAI transfers tested in `testDAIPayments` matches number in the Exec Doc
  * IF `MKR` or `DAI` streams (`DssVest`) are created
    * [ ] `VestAbstract` interface is imported from `dss-interfaces/dss/VestAbstract.sol`
    * [ ] `restrict` is used for each stream unless otherwise explicitely stated in the Exec Doc
    * [ ] `usr` (Vest recipient address) matches Exec Doc
    * [ ] `tot` (Total stream amount) matches Exec Doc
    * [ ] IF `ether` keyword is used, comment is present on the same line `// Note: ether is a keyword helper, only MKR is transferred here`
    * [ ] IF vest amount is expressed in 'per year' or similar in the Exec Doc, account for leap days
    * [ ] `bgn` (Vest start timestamp) matches Exec Doc
    * [ ] `tau` is expressed as `bgn - fin` (i.e. `MONTH_DD_YYYY - MONTH_DD_YYYY`)
    * [ ] `fin` (Vest end timestamp) matches Exec Doc
    * [ ] `eta` (Vest cliff duration) matches the following logic
      * IF `eta` is explicitely specified in the Exec Doc, then the values match
      * IF `eta` and `clf` (Cliff end timestamp) are not specified in the Exec Doc, then `eta` is `0`
      * IF `clf` is specified, but `clf <= bgn`, then `eta` is `0`
      * IF `clf` is specified and `clf > bgn`, `eta` is expressed as `clf - bgn` (i.e. `MONTH_DD_YYYY - MONTH_DD_YYYY`)
    * [ ] IF `mgr` (Vest manager address) is specified in the Exec Doc, matches the value, OTHERWISE matches `address(0)`
    * [ ] Ensure that max vesting rate (`cap`) is enough for the new streams
      * The maximum vesting rate (`tot` divided by `tau`) `<=` the maximum vest streaming rate (`cap`)
      * The maximum vesting rate (`tot` divided by `tau`) `>`  the maximum vest streaming rate (`cap`)
      * Calculate new `cap` value equal to 10% greater than the new maximum vesting rate, then round new `cap` up with 2 significant figure precision (i.e. 2446 becomes 2500)
    * IF max vesting rate (`cap`) is changed in the spell
      * [ ] Governance facilitators were notified
      * [ ] Exec Sheet contain explicit instruction
      * [ ] Exec Doc contain explicit instruction
    * IF MKR stream ([DssVestTransferrable](https://github.com/makerdao/dss-vest/blob/master/src/DssVest.sol#L463)) is present
      * [ ] Vest contract's MKR allowance increased by the cumulative `total` (the sum of all `tot` values)
      * [ ] Ensure allowance increase follows archive patterns
  * IF `MKR` or `DAI` vest termination (`Yank`) is present
    * [ ] Yanked stream ID matches Exec Doc
    * [ ] `MCD_VEST_MKR_TREASURY` chainlog address is used for MKR stream `yank`
    * [ ] `MCD_VEST_DAI` chainlog address is used for DAI stream `yank`
  * [ ] Ensure Recipient Addresses match `addresses_wallets.sol`
* IF SubDAO-related content is present
  * IF SubDAO provides SubProxy spell address
    * [ ] SubDAO spell address matches Exec Doc
    * [ ] Executed via `ProxyLike(SUBDAO_PROXY).exec(SUBDAO_SPELL, abi.encodeWithSignature("execute()"));`
    * [ ] Execution is NOT delegate call
    * [ ] IF SubDAO spell deployer is a smart contract (e.g. multisig or factory), ensure the deployer address is in `addresses_deployers.sol` as an entry
    * [ ] Ensure that SubDAO spell have enough gas and does not revert with "out of gas" error inside simulation. Note: low level call gas estimation is not done by our scripts
  * IF SubDAO provides instructions to be executed by the main spell (i.e. that will operate within Pause Proxy `DelegateCall` context)
    * [ ] No SubDAO contract being interacted with is authed on a core contract like `vat`, etc. (Check comprehensively where the risk is high)
    * [ ] SubDAO contract licensing and optimizations generally do not matter (except where they pose a security risk)
    * [ ] SubDAO contracts and all libraries / dependencies have verified source code (Blocking)
    * Upgradable SubDAO contracts
      * [ ] Upgradable contracts have the `PAUSE_PROXY` as their `admin` (i.e. the party that can upgrade)
      * [ ] Any upgradable SubDAO contracts with an `admin` that is not `PAUSE_PROXY` are not authed on any core contracts (Blocking)
    * [ ] All SubDAO content addresses (i.e. provided contract addresses or EOAs) present in the Maker Core spell are present in the Exec Doc and are correct. SubDAO addresses being authed or given any permissions MUST be in the Exec Doc. SubDAO addresses being called must be confirmed by the SubDAO spell team.
    * [ ] IF addresses not PR'ed in by the SubDAO team (use git blame for example), SubDAO content addresses all have inline comment for provenance or source being OKed by SubDAO
    * [ ] SubDAO actions match Exec Doc (only where inline with main spell code) and do not affect core contracts
    * [ ] Core contract knock-on actions (such as offboarding or setting DC to 0) are present in the exec and match the code
    * [ ] External calls for SubDAO content are NOT delegate call
    * [ ] Code does not have untoward behavior within the scope of Maker Core Contracts (e.g. up to the SubDAO proxy)
* IF external contracts calls present (Not SubDAOs, e.g. Starknet)
  * [ ] Target Contract don't block spell execution
  * [ ] External call is NOT delegate call
  * [ ] Target Contract doesn't have permissions on the Vat
  * [ ] Target Contract doesn't do anything untoward (e.g. interacting with unsafe contracts)
  * [ ] Contracts deployed via `CREATE2` (e.g. if it looks like a vanity address) do not have `selfdestruct` in their code
  * [ ] MCD Pause Proxy doesn't give any approvals
  * [ ] All possible actions of the Target Contract are documented
  * [ ] Target contract is not upgradable
  * [ ] Target Contract is included in the ChainLog
  * [ ] Test Coverage is comprehensive
* IF spell interacts with ChainLog
  * [ ] ChainLog version is incremented based on update type
    * Major -> New Vat (++.0.0)
    * Minor -> Core Module (DSS) Update (e.g. Flapper) (0.++.0)
    * Patch -> Collateral addition or addition/modification (0.0.++)
  * [ ] New addresses are added to the `addresses_mainnet.sol`
  * [ ] Changes are tested via `testNewOrUpdatedChainlogValues`
* [ ] Ensure every spell variable is declared as `public`/`internal`
* [ ] Ensure `immutable` visibility is only used when fetching addresses from the `ChainLog` via `DssExecLib.getChangelogAddress(key)` and `constant` is used instead for static addresses
  * [ ] Fetch addresses as type `address` and wrap with `Like` suffix interfaces inline (when making calls) unless archive patterns permit otherwise (Such as `MKR`)
  * [ ] Use the [DssExecLib Core Address Helpers](https://github.com/makerdao/dss-exec-lib/blob/master/src/DssExecLib.sol#L166) where possible (e.g. `DssExecLib.vat()`)
  * [ ] Where addresses are fetched from the `ChainLog`, the variable name must match the value of the ChainLog key for that address (e.g. `MCD_VAT` rather than `vat`), except where the archive pattern differs from this pattern (e.g. MKR)
* [ ] Spell actions match the corresponding Exec Doc
* Tests
  * [ ] Ensure each spell action has sufficient test coverage
    _List actions for which coverage was checked here_
  * [ ] Ensure every test function is declared as public if enabled or private if disabled
  * [ ] Ensure that the `DssExecLib.address` file is not being modified by the spell PR
  * [ ] Check all CI tests are passing as at the latest commit
    _Insert most recent commit hash where CI was passing_
  * [ ] Check all tests are passing locally using `make test`
  * [ ] Ensure that only `ETH_RPC_URL` is being used from env (i.e. no `match`, `block` or similar are active in your env)

```bash
_Insert your local test logs here_
```

## Deployed Stage

* Source code settings
  * [ ] Deployed spell is verified on etherscan
  * [ ] Optimization enabled: No
  * [ ] Default evmVersion
  * [ ] GNU AGPLv3 license
* Source code validity
  * [ ] Deployed spell code matches source on github. (can be checked via `make diff-deployed-spell` or manually)
  * [ ] No new changes are made after previously given "good to deploy"
* Deployed Spell Etherscan Checks
  * Automated checks via `make check-deployed-spell`
    * [ ] Verified
    * [ ] Valid license
    * [ ] Version matches
    * [ ] Optimizations are disabled
    * [ ] dss-exec-lib library address used (under 'Libraries Used') matches the hardcoded local `DssExecLib.address` file
    * [ ] `deployed_spell_created` matches deployment timestamp
    * [ ] `deployed_spell_block` matches deployment block number
  * Manual checks
    * [ ] Ensure `make deploy-info tx=<tx>` matches [config](https://github.com/makerdao/spells-mainnet/blob/master/src/test/config.sol)
      * [ ] `deployed_spell_created` timestamp
      * [ ] `deployed_spell_block` block number
    * [ ] Check again that the PR did not modify the `DssExecLib.address` file (e.g. look under the 'Files Changed' PR tab, etc.)
    * [ ] Ensure Etherscan `Libraries Used` matches DssExecLib [Latest Release](https://github.com/makerdao/dss-exec-lib/releases/latest)
    * [ ] (For your tests to be accurate) git submodule hash matches [dss-exec-lib](https://github.com/makerdao/dss-exec-lib/releases/latest) latest release's tag commit and inspect diffs if doesn't match to ensure expected behaviour (Currently Non-Critical pending the next DssExecLib release, double check that the ExecLib used by the contract matches the latest release)
* Archive checks
  * [ ] `make diff-archive-spell` for current date or `make diff-archive-spell date="YYYY-MM-DD"`
  * [ ] Ensure date corresponds to target Exec Doc date
* Tests
  * [ ] Ensure that the `DssExecLib.address` file is not being modified by the spell PR
  * [ ] Check all CI tests are passing as at the latest commit
    _Insert most recent commit hash where CI was passing_
  * [ ] Check all tests are passing locally using `make test`
  * [ ] Ensure that only `ETH_RPC_URL` is being used from env (i.e. no `match`, `block` or similar are active in your env)

```bash
_Insert your local test logs here_
```

## Handover and Merge Stage

* [ ] Check that the spell address posted by the crafter in [`new-spells`](https://discord.com/channels/893112320329396265/897483518316265553) is correct
* [ ] Confirm the address in the [`new-spells`](https://discord.com/channels/893112320329396265/897483518316265553) channel (via a separate "reply to" message, restating the address to avoid edits)
* [ ] Ensure that no changes were made to the code since the spell was deployed and archived
* [ ] Approve spell PR for merge via 'Approve' review option
