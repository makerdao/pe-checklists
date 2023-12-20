# Mainnet Executive Spell Review Checklist

## Development Stage

* Exec Doc
  * [ ] Record target date for the executive spell
    _Insert taget date here in the YYYY-MM-DD format_
  * [ ] Exec Sheet for the specified date is found in the ["Executive Vote Implementation Process" google sheet](https://docs.google.com/spreadsheets/d/1w_z5WpqxzwreCcaveB2Ye1PP5B8QAHDglzyxKHG3CHw)
  * [ ] Exec Doc for the specified date is found in the [`makerdao/community` GitHub repo](https://github.com/makerdao/community/tree/master/governance/votes)
  * [ ] Exec Doc file name follows the format `Executive vote - Month DD, YYYY.md`
  * [ ] Extract permanent URL to the raw markdown file and paste it below
    _Insert your Raw Exec Doc URL here_
  * [ ] Using URL from the above, generate Exec Doc Hash via `make exec-hash $URL`
    _Insert your Exec Doc Hash here_
  * [ ] Using URL from the above, generate Exec Doc Hash via `cast keccak -- "$(curl '$URL' -o - 2>/dev/null)"`
    _Insert your Exec Doc Hash here_
  * [ ] Ensure that both hashes match
  * [ ] Using URL from the above, read spell instructions from the Exec Doc and list them below
    _List all actions announced in the Exec Doc_
  * [ ] Ensure that announced actions listed in the Exec Doc match instructions in the Exec Sheet
* Base checks
  * [ ] Current solc version `0.8.16`
  * [ ] Office hours is `true` IF spell introduces a major change that can affect external parties (e.g.: keepers are affected in case of collateral offboarding) OTHERWISE explicitely set to `false`
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
  * [ ] git submodule hashes of `lib/dss-interfaces` matches submodules inside `lib/dss-test`. Can be checked by comparing outputs of `(cd lib/dss-exec-lib && git submodule status)` and `(cd lib/dss-test && git submodule status)`
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
    * [ ] Variable naming matches `MONTH_DD_YYYY` (e.g. `MAY_01_2023` for 2023-05-01)
    * [ ] Time of day makes logical sense in the context of timestamp usage (i.e. `23:59:59 UTC` for the final day of something, `00:00:00 UTC` for the first day of something)
    * [ ] Each variable visibility declared as `internal`
    * [ ] Each variable state mutability declared as `constant`
* IF new contract is present in the spell (not yet on chainlog or new to chainlog)
  * [ ] Source code is verified on etherscan
  * [ ] Compilation optimizations match deployment settings defined in the source code repo
  * [ ] `GNU AGPLv3` license
  * [ ] Every maker-related constructor argument matches chainlog (e.g. `vat`, `dai`, `dog`, ...)
  * IF new contract have concept of `wards` or access control
    * [ ] Ensure `PAUSE_PROXY` address was `relied`
    * [ ] Ensure that contract deployer address was `denied`
    * [ ] Ensure `MCD_ESM` address is already relied OR being relied in this spell (as approved by Governance Facilitators, in order to allow de-authing the pause proxy during Emergency Shutdown, via `denyProxy`)
  * [ ] Source code matches corresponding github source code (e.g. diffcheck via vscode `code --diff etherscan.sol github.sol`)
  * [ ] Ensure deployer address is included into `addresses_deployers.sol`
* IF core system parameter changes are present in the instructions
  * IF stability fee (`jug.ilk.duty`) is updated
    * [ ] ([`DssExecLib.setIlkStabilityFee`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L792)) is used
    * [ ] Comment matches pattern `// Increase ILK-A Stability Fee by X.XX% from X.XX% to X.XX%`
  * IF Dai Savings Rate (`pot.dsr`) is updated
    * [ ] ([`DssExecLib.setDSR`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L455)) is used
    * [ ] Comment matches pattern `// Increase DSR by X.XX% from X.XX% to X.XX%`
    * [ ] Double check that rate match `make rates pct=<pct>` (e.g. pct=0.75, for 0.75%)
    * [ ] Double check that rate match [IPFS](https://ipfs.io/ipfs/QmVp4mhhbwWGTfbh2BzwQB9eiBrQBKiqcPRZCaAxNUaar6) document
  * [ ] IF `spotter.ilk.mat` is updated, ([`DssExecLib.setIlkLiquidationRatio`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L709)) is used
  * [ ] IF `dog.ilk.hole` is updated, ([`DssExecLib.setIlkMaxLiquidationAmount`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L699)) is used
  * [ ] IF `vat.ilk.dust` is updated, ([`DssExecLib.setIlkMinVaultAmount`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L676)) is used
  * [ ] IF `dog.ilk.chop` is updated, ([`DssExecLib.setIlkLiquidationPenalty`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L689)) is used
  * [ ] IF `clip.buf` is updated, ([`DssExecLib.setStartingPriceMultiplicativeFactor`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L720)) is used
  * [ ] IF `clipperMom.clip.tolerance` is updated, ([`DssExecLib.setLiquidationBreakerPriceTolerance`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L772)) is used
  * [ ] IF `clip.tail` is updated, ([`DssExecLib.setAuctionTimeBeforeReset`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L731)) is used
  * [ ] IF `clip.cusp` is updated, ([`DssExecLib.setAuctionPermittedDrop`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L740)) is used
  * [ ] IF `clip.chip` is updated, ([`DssExecLib.setKeeperIncentivePercent`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L750)) is used
  * [ ] IF `clip.tip` is updated, ([`DssExecLib.setKeeperIncentiveFlatRate`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L760)) is used
  * [ ] IF `calc.tau` is updated, ([`DssExecLib.setLinearDecrease`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L811)) is used
  * [ ] IF `calc.cut` or `calc.step` are updated, [`DssExecLib.setStairstepExponentialDecrease`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L822) is used
* IF debt ceiling changes are present in the spell
  * IF collateral type (`ilk`) is not RWA (for RWA refer to the designated section of the checklist)
    * IF collateral type (`ilk`) have [AutoLine](https://github.com/makerdao/dss-auto-line/tree/master) enabled (`MCD_IAM_AUTO_LINE`)
      * IF collateral debt ceiling (`vat.ilk.line`) is set to `0`
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
      * [ ] Global debt ceiling (`vat.Line`) is updated accordingly, UNLESS specifically instructed not to. Either via:
          * `global` set to `true` in `increaseIlkDebtCeiling`/`increaseIlkDebtCeiling`
          * [`DssExecLib.setGlobalDebtCeiling(amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L428)
          * [`DssExecLib.increaseGlobalDebtCeiling(amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L436)
          * [`DssExecLib.decreaseGlobalDebtCeiling(amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L445C14-L445C39)
* IF onboarding is present in the spell
  * [ ] Insert and follow relevant checklists here
    * [Collateral Onboarding](./collateral-onboarding-checklist.md)
    * [RWA Onboarding](./rwa-onboarding-checklist.md)
    * [Teleport Onboarding](./teleport-onboarding-checklist.md)
* IF collateral offboarding is present in the spell
  * 1st stage collateral offboarding
    * [ ] Collateral type (`ilk`) is removed from AutoLine (`MCD_IAM_AUTO_LINE`) IF currently enabled
    * [ ] Current collateral debt ceiling (`vat.ilk.line`) is saved into local variable (`line`)
    * [ ] Collateral debt ceiling (`vat.ilk.line`) is set to `0`
    * [ ] Global debt ceiling (`vat.Line`) decreased by the total amount of offboarded ilks using local variable (`line`)
  * 2nd stage collateral offboarding
    * [ ] All actions from the 1st stage offboarding are previously taken
    * [ ] Collateral liquidation penalty (`chop`) is set to `0`
    * [ ] Flat keeper incentive (`tip`) is set to `0`
    * [ ] Relative keeper incentive (`chip`) is set to `0` IF requested by the governance
    * [ ] Relevant clipper contract (`MCD_CLIP_`) is not stopped ([`stopped`](https://github.com/makerdao/dss/blob/fa4f6630afb0624d04a003e920b0d71a00331d98/src/clip.sol#L97) is `0`)
    * [ ] Max liquidation amount (`hole`) is adjusted via [`DssExecLib.setIlkMaxLiquidationAmount(ilk, amount)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L699) IF requested by the governance
    * [ ] Liquidations are triggered via (depending on the governance instruction):
      * EITHER liquidation ratio (`spotter.ilk.mat`) being set very high in the spell (using `DssExecLib.setValue(DssExecLib.spotter(), ilk, "mat", ratio)`)
      * OR via enabling linear interpolation ([`DssExecLib.linearInterpolation`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L1096-L1112))
    * IF linear interpolation is used
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
    * [ ] Spotter price is updated via [`DssExecLib.updateCollateralPrice(ilk)`](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L374) IF collateral have no running oracle or is stablecoin
    * [ ] Offboarding is tested at least via [`_checkIlkClipper` helper](https://github.com/makerdao/spells-mainnet/blob/7400e91c4f211fc24bd4d3a95a86416afc4df9d1/src/DssSpell.t.base.sol#L856)
* [ ] RWA Updates
  * [ ] `doc` (Using the [`_updateDoc` helper](https://github.com/makerdao/spells-goerli/blob/88413f576d14628a3488a096d9da6775bef46eaa/archive/2022-11-14-DssSpell/Goerli-DssSpell.sol#L59) or otherwise)
    * [ ] `init` the `RwaLiquidationOracle` to reset the `doc`
    * [ ] Sanity Check `pip` must be set (not the zero address)
    * [ ] `ilk` follows format "RWAXXX-A"
    * [ ] `val` price ignored (`0`) if `init` has already been called
    * [ ] `doc` new legal document (IPFS HASH) matches Exec Doc
    * [ ] `tau` parameter used is the old `tau` value
  * [ ] Debt Ceiling changes (`line`)
    * [ ] Autoline (`line`) + Liquidation Oracle Price Bump (`val`)
      * [ ] Enable Autoline
        * [ ] `ilk` follows format "RWAXXX-A"
        * [ ] `line` (max debt ceiling)
        * [ ] `gap`
        * [ ] `ttl`
    * [ ] Debt Ceiling (`line`) + Liquidation Oracle Price Bump (`val`)
      * [ ] Increase Ilk Debt Ceiling (set DC + increase Global DC)
    * [ ] `bump` `RwaLiquidationOracle` with new computed increased price (`val`)
      * [ ] ensure `val` is set accordingly with autoline max debt ceiling (`line`)
      * [ ] `val` should enable DAI to be drawn over the loan period while taking into account the configured `ink` amount, interest rate and liquidation ratio (see below)
        * [ ] New `val` is calculated with `line * [(1 + duty) ** years] * mat` - rounded up - and makes sense in context of the [rate mechanism](https://github.com/makerdao/developerguides/blob/master/mcd/intro-rate-mechanism/intro-rate-mechanism.md). Minimum duration is usually in the Exec Doc of the spell with the RWAXXX ilk onboarding.
        * [ ] Comment explaining `val` formula (`Debt ceiling * [ (1 + RWA stability fee ) ^ (minimum deal duration in years) ] * liquidation ratio`) is present
        * [ ] Accompanying comment above `bump` line in format `// XXM * 1.XX^X * X.XX as a WAD` corresponding to the `val` calculation formula (e.g. `// 15M * 1.03^2 * 1.00 as a WAD`) is present along with the calculation formula on the line above
        * [ ] IF combining `val` of multiple RWA ilks being combined, `val` calculation is done once per ilk and added to make the total, with workings provided in code comments. The existing `val` value can be retrieved by calling `read()` on `PIP_RWAXX` and converting the result into decimal.
    * [ ] Poke `spotter` to pull in the new price
  * [ ] Soft Liquidation (`tell`)
      * [ ] Call `RwaLiquidationOracle.tell(ilk)`
      * [ ] IF `RWAXX_A_INPUT_CONDUIT` is an instance of [`TinlakeMgr`](https://github.com/centrifuge/tinlake-maker-lib/blob/master/src/mgr.sol) (it is a Centrifuge integration)
        * [ ] Call `TinlakeMgr.tell()` to prevent further `TIN` redemptions in the Centrifuge pool.
* [ ] Payments
  * [ ] MKR transfers
    * [ ] Recipient addresses match Exec Doc
    * [ ] Transfer values match Exec Doc
        * [ ] All values are specified with at least 2 decimals
        * [ ] Sum of all values matches Exec Doc
    * [ ] Follows archive patterns
  * [ ] DAI Surplus Buffer transfers
    * [ ] Recipient addresses match Exec Doc
    * [ ] Transfer values match Exec Doc
        * [ ] All values are specified with 2 decimals
        * [ ] Sum of all values matches Exec Doc
    * [ ] Follows previous patterns
  * [ ] Create New MKR/DAI Streams (`DssVest`)
    * [ ] `DssVestLike` interface is correct
    * [ ] Check for all streams (MKR, DAI)
      * [ ] `restrict` is used for each stream unless otherwise stated in the Exec Doc
      * [ ] `usr` (Vest recipient address) matches Exec Doc
      * [ ] `tot` (Total stream amount) matches Exec Doc
        * [ ] Use `ether` to express decimal (non-integer) values of `tot`
        * [ ] Where vest amount is expressed in 'per year' or similar in the Exec Doc, account for leap days
      * [ ] `bgn` (Vest start timestamp) matches Exec Doc
      * [ ] `tau` (Vest total duration) matches Exec Doc
        * [ ] `tau` is expressed as '`bgn` - `fin`' (i.e. `MONTH_DD_YYYY - MONTH_DD_YYYY`)
        * [ ] `fin` (Vest end timestamp) matches Exec Doc
      * [ ] `eta` (Vest cliff duration) matches Exec Doc
        * [ ] If the Exec Doc does not specify a cliff date (`clf`), `eta` is 0
        * [ ] `clf` (Cliff end timestamp) matches Exec Doc
        * [ ] If `clf <= bgn`, `eta` is 0
        * [ ] If `clf > bgn`
          * [ ] `eta` is expressed as '`clf` - `bgn`' (i.e. `MONTH_DD_YYYY - MONTH_DD_YYYY`)
      * [ ] `mgr` (Vest manager address) matches Exec Doc
        * [ ] If the Exec Doc does not specify a manager address, `mgr` is `address(0)`
    * [ ] Max vesting rate (`cap`) check (MKR, DAI)
      * [ ] The maximum vesting rate (`tot` divided by `tau`) `<=` the maximum vest streaming rate (`cap`)
      * [ ] The maximum vesting rate (`tot` divided by `tau`) `>`  the maximum vest streaming rate (`cap`)
        * [ ] Calculate new `cap` value equal to 10% greater than the new maximum vesting rate
        * [ ] Round new `cap` up with 2 significant figure precision (i.e. 2446 becomes 2500)
        * [ ] Notify Governance Facilitators for addition in Exec Doc AND Exec Sheet
        * [ ] Ensure that `cap` change is noted in the Exec Doc
        * [ ] New `cap` value matches Exec Doc
    * [ ] MKR Streams ([DssVestTransferrable](https://github.com/makerdao/dss-vest/blob/master/src/DssVest.sol#L463))
      * [ ] Increase vest contract's MKR allowance by the cumulative `tot` (total of new MKR vest amounts)
      * [ ] Ensure allowance increase follows archive patterns
  * [ ] Terminate Existing MKR/DAI Streams (`Yank`)
    * [ ] Yanked stream ID matches Exec Doc
    * [ ] Use `MCD_VEST_MKR_TREASURY` for MKR `yank`
    * [ ] Use `MCD_VEST_DAI` for DAI `yank`
  * [ ] Ensure Recipient Addresses match `addresses_wallets.sol`
* [ ] SubDAO Content
  * [ ] SubDAO SubProxy spell execution
    * [ ] SubDAO spell address matches Exec Doc
    * [ ] IF SubDAO spell deployer is a smart contract (e.g. multisig or factory)
      * [ ] Ensure the deployer address is in `addresses_deployers.sol` as an entry
    * [ ] Executed using `ProxyLike(SUBDAO_PROXY).exec(SUBDAO_SPELL, abi.encodeWithSignature("execute()"));`
    * [ ] Execution is NOT delegate call
    * [ ] Reviewer Note: Gas cost may be very high as SubDAO spells execute within the main `cast` execution. (Also note that low level call gas estimation is not done by our scripts)
  * [ ] Maker Core (main spell) SubDAO actions (i.e. operate in Pause Proxy `DelegateCall` context)
    * [ ] No SubDAO contract being interacted with is authed on a core contract like vat, etc. (Check comprehensively where the risk is high)
    * [ ] SubDAO contract licensing and optimizations generally do not matter (except where they pose a security risk)
    * [ ] SubDAO contracts and all libraries / dependencies are verified (Blocking if not true)
    * [ ] Upgradable SubDAO contracts
      * [ ] Any upgradable contracts have the `PAUSE_PROXY` as their `admin` (i.e. the party that can upgrade)
        * [ ] Any upgradable SubDAO contracts with an `admin` that is not `PAUSE_PROXY` are not authed on any core contracts (Blocking)
    * [ ] All SubDAO content addresses (i.e. provided contract addresses or EOAs) present in the Maker Core spell are present in the Exec Doc and are correct. SubDAO addresses being authed or given any permissions MUST be in the Exec Doc. SubDAO addresses being called must be confirmed by the SubDAO spell team.
      * [ ] IF addresses not PR'ed in by the SubDAO team (use git blame for example), SubDAO content addresses all have inline comment for provenance or source being OKed by SubDAO
    * [ ] SubDAO actions match Exec Doc (only where inline with main spell code) and do not affect core contracts
    * [ ] Core contract knock-on actions (such as offboarding or setting DC to 0) are present in the exec and match the code
    * [ ] External calls for SubDAO content are NOT delegate call
    * [ ] Code does not have untoward behavior within the scope of Maker Core Contracts (e.g. up to the SubDAO proxy)
* [ ] External Contracts Calls (Not SubDAOs, e.g. Starknet)
  * [ ]  Target Contract don't block spell execution
  * [ ]  External call is NOT delegate call
  * [ ]  Target Contract doesn't have permissions on the Vat
  * [ ]  Target Contract doesn't do anything untoward (e.g. interacting with unsafe contracts)
  * [ ]  Contracts deployed via `CREATE2` (e.g. if it looks like a vanity address) do not have `selfdestruct` in their code
  * [ ]  MCD Pause Proxy doesn't give any approvals
  * [ ]  All possible actions of the Target Contract are documented
  * [ ]  Target contract is not upgradable
  * [ ]  Target Contract is included in the ChainLog
  * [ ]  Test Coverage is comprehensive
* [ ] ChainLog
  * [ ] Increment ChainLog version based on update type
    * [ ] Major -> New Vat (++.0.0)
    * [ ] Minor -> Core Module (DSS) Update (e.g. Flapper) (0.++.0)
    * [ ] Patch -> Collateral addition or addition/modification (0.0.++)
* [ ] `addresses_mainnet.sol` matches spell code
* [ ] Ensure every spell variable is declared as `public`/`internal`
* [ ] Ensure `immutable` visibility is only used when fetching addresses from the `ChainLog` via `DssExecLib.getChangelogAddress` and `constant` is used instead for static addresses
  * [ ] Fetch addresses as type `address` and wrap with `Like` suffix interfaces inline (when making calls) unless archive patterns permit otherwise (Such as `MKR`)
  * [ ] Use the [DssExecLib Core Address Helpers](https://github.com/makerdao/dss-exec-lib/blob/master/src/DssExecLib.sol#L166) where possible (e.g. `DssExecLib.vat()`)
  * [ ] Where addresses are fetched from the `ChainLog`, the variable name must match the value of the ChainLog key for that address (e.g. `MCD_VAT` rather than `vat`), except where the archive pattern differs from this pattern (e.g. MKR)
* [ ] Spell actions match the corresponding Exec Doc
* [ ] Tests
  * [ ] Ensure each spell action has sufficient test coverage
    _List actions for which coverage was checked here_
  * [ ] Ensure every test function is declared as public if enabled or private if disabled
  * [ ] Ensure that the `DssExecLib.address` file is not being modified by the spell PR
  * [ ] Check all CI tests are passing as at the latest commit
    _Insert most recent commit hash where CI was passing_
  * [ ] Check all tests are passing locally using `make test`
    * [ ] Ensure that only `ETH_RPC_URL` is being used from env (i.e. no `match`, `block` or similar are active in your env)

```
_Insert your passing local tests here_
```

## Deployed Stage

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
      * [ ] Check again that the PR did not modify the `DssExecLib.address` file (e.g. look under the 'Files Changed' PR tab, etc.)
    * [ ] `deployed_spell_created` matches deployment timestamp
    * [ ] `deployed_spell_block` matches deployment block number
  * [ ] manual checks
      * [ ] Ensure `make deploy-info tx=<tx>` matches [config](https://github.com/makerdao/spells-mainnet/blob/master/src/test/config.sol)
        * [ ] `deployed_spell_created` timestamp
        * [ ] `deployed_spell_block` block number
      * [ ] Ensure Etherscan `Libraries Used` matches DssExecLib [Latest Release](https://github.com/makerdao/dss-exec-lib/releases/latest)
       * [ ] (For your tests to be accurate) git submodule hash matches [dss-exec-lib](https://github.com/makerdao/dss-exec-lib/releases/latest) latest release's tag commit and inspect diffs if doesn't match to ensure expected behaviour (Currently Non-Critical pending the next DssExecLib release, double check that the ExecLib used by the contract matches the latest release)
* [ ] Archive matches `src`
  * [ ] `make diff-archive-spell` for current date or or `date="YYYY-MM-DD" make diff-archive-spell` (date as per target Exec Doc date)
  * [ ] Ensure date corresponds to target Exec Doc date
* [ ] Tests
  * [ ] Ensure that the `DssExecLib.address` file is not being modified by the spell PR
  * [ ] Check all CI tests are passing as at the latest commit
    _Insert most recent commit hash where CI was passing_
  * [ ] Check all tests are passing locally using `make test`
    * [ ] Ensure that only `ETH_RPC_URL` is being used from env (i.e. no `match`, `block` or similar are active in your env)

```
_Insert your passing local tests here_
```

## Handover and Merge Stage

* [ ] Check that the spell address posted by the crafter in [`new-spells`](https://discord.com/channels/893112320329396265/897483518316265553) is correct
  * [ ] Confirm the address in [`new-spells`](https://discord.com/channels/893112320329396265/897483518316265553) (via a thumbs up message reaction, )
* [ ] Ensure that no commits or changes have occurred since the spell was deployed and archived
* [ ] Approve spell PR for merge via 'Approve' review option
