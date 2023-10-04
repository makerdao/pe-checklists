# Goerli Executive Spell Review Checklist goerli

## Goerli YYYY-MM-DD

Spell Actions (Per Exec Sheet):

* _Read spell actions and instructions from the Exec Sheet_
* _List the actions being performed in this spell_

## Development Stage

* [ ] Office Hours
  * [ ] OFF (On Goerli the deployed spell is cast with a pause delay of 60 sec.)
* [ ] 30 days spell expiry in constructor (`block.timestamp + 30 days`)
* [ ] No Exec Doc Hash on Goerli
* [ ] Spell Description
  * [ ] Ensure `description` exactly matches '`Goerli Spell`'
* [ ] Local Environment Actions
  * [ ] Update Foundry by running `foundryup`
  * [ ] Reinstall libraries by running `rm -r lib && git submodule update --init --recursive`
      ```
      Insert checked out submodule paths here
      ```
  * [ ] Library Checks
    * [ ] `dss-exec-lib`
      * [ ] If submodule upgrades are present make sure `dss-exec-lib` is synced as well
      * [ ] (Non-critical) Submodule hash matches the latest release version (NOTE: `dss-exec-lib` as installed locally will use GitHub code more recent than the 0.0.9 release until the 0.0.10 release)
    * [ ] `dss-test`
      * [ ] `dss-interfaces`
        * [ ] (Non-critical) Submodule hash matches [version used by `dss-test`](https://github.com/makerdao/dss-test/tree/master/lib)
        * [ ] (Non-critical) Submodule hash matches [version used by `dss-exec-lib`](https://github.com/makerdao/dss-test/tree/master/lib)
      * [ ] `forge-std`
        * [ ] (Non-critical) Submodule hash matches [version used by `dss-test`](https://github.com/makerdao/dss-test/tree/master/lib)
        * [ ] (Non-critical) Submodule hash matches [version used by `dss-exec-lib`](https://github.com/makerdao/dss-test/tree/master/lib)
* [ ] Rate constants used are correct
  * [ ] Manual check 1: Calculate rates using `make rates pct=<pct>` (e.g. pct=0.75, for 0.75%)
  * [ ] Manual check 2: Compare rates used against [IPFS rate list](https://ipfs.io/ipfs/QmVp4mhhbwWGTfbh2BzwQB9eiBrQBKiqcPRZCaAxNUaar6)
  * [ ] Variable name conforms to `X_PT_Y_Z_PCT_RATE` (e.g. `ZERO_PT_SEVEN_FIVE_PCT_RATE` for 0.75%, `SIX_PCT_RATE` for 6.00%)
  * [ ] Visibility is `internal`
  * [ ] State mutability is `constant`
* [ ] Constants Match
  * [ ] Precision unit constants used match their defined values
    * [ ] `WAD = 10 ** 18`
    * [ ] `RAY = 10 ** 27`
    * [ ] `RAD = 10 ** 45`
    * [ ] Visibility is `internal`
    * [ ] State mutability is `constant`
    * [ ] (Non-critical) Ensure they match with [ds-math](https://github.com/dapphub/ds-math/blob/master/src/math.sol) and the [Numerical Ranges](https://github.com/makerdao/dss/wiki/Numerical-Ranges#notation)
  * [ ] Math unit constants used match their defined values
    * [ ] `HUNDRED  = 10 ** 2`
    * [ ] `THOUSAND = 10 ** 3`
    * [ ] `MILLION  = 10 ** 6`
    * [ ] `BILLION  = 10 ** 9`
    * [ ] Visibility is `internal`
    * [ ] State mutability is `constant`
    * [ ] (Non-critical) Ensure they match with [config](https://github.com/makerdao/spells-mainnet/blob/master/src/test/config.sol)
* [ ] Address Declaration
  * [ ] Address Constants
    * [ ] Address is not in the ChainLog
    * [ ] Variable name matches corresponding entry in an `addresses.sol` file
    * [ ] Address value matches corresponding entry in an `addresses.sol` file
    * [ ] Visibility is `internal`
    * [ ] State mutability is `constant`
  * [ ] Address Immutables
    * [ ] Use the [DssExecLib Core Address Helpers](https://github.com/makerdao/dss-exec-lib/blob/master/src/DssExecLib.sol#L166) instead of `getChangelogAddress()` whenever possible (e.g. `DssExecLib.vat()`)
    * [ ] Follow pattern `address internal immutable KEY_NAME = DssExecLib.getChangelogAddress(KEY_NAME)`
    * [ ] Variable name matches ChainLog key name
      * [ ] Exceptions must follow archive patterns (e.g. `MKR` for `MCD_GOV`)
    * [ ] Instantiate as `address` type
      * [ ] Exceptions must follow archive patterns (e.g. `GemLike` for `MKR`)
    * [ ] Visibility is `internal`
    * [ ] State mutability is `immutable`
* [ ] String Constants
  * [ ] IPFS hashes or DAO resolutions
      * [ ] IPFS hashes and DAO resolutions match the exec doc.
      * [ ] The variable is named `dao_resolutions`
      * [ ] if multiple dao resolutions are present
          * [ ] they are located in the same string constant and are comma separated.
          * [ ] The comment above the the variable states: `Comma-separated list of DAO resolutions IPFS hashes.`
  * [ ] Must be TOP LEVEL (or ds pause will reject)
  * [ ] Name of the variable matches purpose per EXEC DOC
  * [ ] Visibility is `public`
  * [ ] State mutability MUST BE `constant`
* [ ] Deployed Contracts (not yet on chainlog or new to chainlog)
  * [ ] Verified on etherscan
  * [ ] Optimizations match Repo
  * [ ] `GNU AGPLv3` license
  * [ ] Constructor args ok (e.g. `vat`, `dai`, `dog`, ...)
    * [ ] Match [ChainLog](https://chainlog.makerdao.com/)
  * [ ] Wards ok (pause proxy relied, deployer denied)
    * [ ] `MCD_ESM` is already relied / being relied in this spell (as approved by GovAlpha) in order to allow de-authing the pause proxy during Emergency Shutdown, via `denyProxy`.
  * [ ] Matches corresponding github source code (i.e. diffcheck via vscode `code --diff etherscan.sol github.sol`)
  * [ ] Ensure deployer address is included into `addresses_deployers.sol` (**to keep up to date**)
* [ ] Core System Parameter Changes
  * [ ] Stability Fee `jug.ilk.duty` ([setIlkStabilityFee](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L792))
    * [ ] Comment matches pattern `// Increase ILK-A Stability Fee by X.XX% from X.XX% to X.XX%.`
  * [ ] Dai Savings Rate `pot.dsr` ([setDSR](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L455))
    * [ ] Double check that `PCT_RATE` is correct
      * [ ] Check manually via `make rates pct=<pct>` (e.g. pct=0.75, for 0.75%)
      * [ ] Compare against [IPFS](https://ipfs.io/ipfs/QmVp4mhhbwWGTfbh2BzwQB9eiBrQBKiqcPRZCaAxNUaar6)
  * [ ] `spotter.ilk.mat`  ([liquidationRatio](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L709))
  * [ ] `dog.ilk.hole` ([setIlkMaxLiquidationAmount](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L699))
  * [ ] `vat.ilk.dust`([setIlkMinVaultAmount](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L676))
  * [ ] `dog.ilk.chop` ([liquidationPenalty](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L689))
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
* [ ] Debt Ceiling Changes
  * [ ] `vat.ilk.line` changes (per ilk)
    * [ ] Collateral type (ilk) is not RWAXXX (For RWA refer to the designated section of the checklist)
    * [ ] Either is used depending on the exec sheet/doc contents:
        * [ ] [`setIlkDebtCeiling`](https://github.com/makerdao/dss-exec-lib/blob/c0d3c6c6244468ddab9767de6f853122723fafda/src/DssExecLib.sol#L611)
            * [ ] `ilk`
            * [ ] `line`
        * [ ] [`increaseIlkDebtCeiling`](https://github.com/makerdao/dss-exec-lib/blob/c0d3c6c6244468ddab9767de6f853122723fafda/src/DssExecLib.sol#L621C14-L621C36)
            * [ ] `ilk`
            * [ ] `amount`
            * [ ] `global`
        * [ ] [`decreaseIlkDebtCeiling`](https://github.com/makerdao/dss-exec-lib/blob/c0d3c6c6244468ddab9767de6f853122723fafda/src/DssExecLib.sol#L634)
            * [ ] `ilk`
            * [ ] `amount`
            * [ ] `global`
  * [ ] `vat.Line` changes (Global Line)
      * [ ] Either is used:
          * [ ] [`setGlobalDebtCeiling`](https://github.com/makerdao/dss-exec-lib/blob/c0d3c6c6244468ddab9767de6f853122723fafda/src/DssExecLib.sol#L428)
              * [ ] `amount`
          * [ ] [`increaseGlobalDebtCeiling`](https://github.com/makerdao/dss-exec-lib/blob/c0d3c6c6244468ddab9767de6f853122723fafda/src/DssExecLib.sol#L436)
              * [ ] `amount`
          * [ ] [`decreaseGlobalDebtCeiling`](https://github.com/makerdao/dss-exec-lib/blob/c0d3c6c6244468ddab9767de6f853122723fafda/src/DssExecLib.sol#L445C14-L445C39)
              * [ ] `amount`
  * [ ] Autoline Changes
    * [setIlkAutoLineDebtCeiling](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L658)
      * [ ] `ilk`
      * [ ] `line`
    * [setIlkAutoLineParameters](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L648)
      * [ ] `ilk`
      * [ ] `line`
      * [ ] `gap`
      * [ ] `ttl`
* [ ] Onboarding (insert relevant checklists inline here)
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
      * [ ] `end` matches Var `TARGET_XXX_A_MAT` (Match Exec Sheet & Risk Computations)
        * [ ] Check IF Target `mat` Covers All Remaining Vaults CR times Risk Multiplier Factor
      * [ ] `duration` matches Exec Sheet
* [ ] RWA Updates
  * [ ] `doc` (Using the [`_updateDoc` helper](https://github.com/makerdao/spells-goerli/blob/88413f576d14628a3488a096d9da6775bef46eaa/archive/2022-11-14-DssSpell/Goerli-DssSpell.sol#L59) or otherwise)
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
      * [ ] `val` should enable DAI to be drawn over the loan period while taking into account the configured `ink` amount, interest rate and liquidation ratio (see below)
        * [ ] New `val` is calculated with `line * [(1 + duty) ** years] * mat` - rounded up - and makes sense in context of the [rate mechanism](https://github.com/makerdao/developerguides/blob/master/mcd/intro-rate-mechanism/intro-rate-mechanism.md). Minimum duration is usually in the Exec Doc of the spell with the RWAXXX ilk onboarding.
        * [ ] Comment explaining `val` formula (`Debt ceiling * [ (1 + RWA stability fee ) ^ (minimum deal duration in years) ] * liquidation ratio`) is present
        * [ ] Accompanying comment above `bump` line in format `// XXM * 1.XX^X * X.XX as a WAD` corresponding to the `val` calculation formula (e.g. `// 15M * 1.03^2 * 1.00 as a WAD`) is present along with the calculation formula on the line above
        * [ ] IF combining `val` of multiple RWA ilks being combined, `val` calculation is done once per ilk and added to make the total, with workings provided in code comments. The existing `val` value can be retrieved by calling `read()` on `PIP_RWAXX` and converting the result into decimal.
    * [ ] Poke `spotter` to pull in the new price
  * [ ] Payments
    * [ ] Payments are not crafted or reviewed on Goerli (comments may be present)
  * [ ] Debt Ceiling (`line`) + Liquidation Oracle Price Bump (`val`)
    * [ ] Increase Ilk Debt Ceiling (set DC + increase Global DC)
    * [ ] `bump` `RwaLiquidationOracle` with new computed increased price (`val`)
      * [ ] `val` should enable DAI to be drawn over the loan period while taking into account the configured `ink` amount, interest rate and liquidation ratio (see below)
        * [ ] New `val` is calculated with `line * [(1 + duty) ** years] * mat` - rounded up - and makes sense in context of the [rate mechanism](https://github.com/makerdao/developerguides/blob/master/mcd/intro-rate-mechanism/intro-rate-mechanism.md). Minimum duration is usually in the Exec Doc of the spell with the RWAXXX ilk onboarding.
        * [ ] Comment explaining `val` formula (`Debt ceiling * [ (1 + RWA stability fee ) ^ (minimum deal duration in years) ] * liquidation ratio`) is present
        * [ ] Accompanying comment above `bump` line in format `// XXM * 1.XX^X * X.XX as a WAD` corresponding to the `val` calculation formula (e.g. `// 15M * 1.03^2 * 1.00 as a WAD`) is present along with the calculation formula on the line above
        * [ ] IF combining `val` of multiple RWA ilks being combined, `val` calculation is done once per ilk and added to make the total, with workings provided in code comments. The existing `val` value can be retrieved by calling `read()` on `PIP_RWAXX` and converting the result into decimal.
    * [ ] Poke `spotter` to pull in the new price
* [ ] SubDAO Content
  * [ ] SubDAO SubProxy spell execution
    * [ ] SubDAO spell address matches Exec Sheet (comment or otherwise)
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
    * [ ] All SubDAO content addresses (i.e. provided contract addresses or EOAs) present in the Maker Core spell are present in the Exec Sheet and are correct. SubDAO addresses being authed or given any permissions and addresses being called must be confirmed by the SubDAO spell team.
    * [ ] SubDAO actions match Exec Sheet (only where inline with main spell code) and do not affect core contracts
    * [ ] Core contract knock-on actions (such as offboarding or setting DC to 0) are present in the Exec Sheet and match the code
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
* [ ] `addresses_goerli.sol` matches spell code
* [ ] Ensure every spell variable is declared as public/internal
* [ ] Ensure every spell variable is used in the spell (no unused variables)
* [ ] Spell actions match the corresponding [Exec Sheet](https://docs.google.com/spreadsheets/d/1w_z5WpqxzwreCcaveB2Ye1PP5B8QAHDglzyxKHG3CHw)
* [ ] Tests
  * [ ] Ensure each spell action has sufficient test coverage
  _List actions for which coverage was checked here_
  * [ ] Ensure every test function is declared as public if enabled or private if disabled
  * [ ] Ensure that the `DssExecLib.address` file is not being modified by the spell PR
  * [ ] Check all CI tests are passing as at the latest commit
    _Insert most recent commit hash where CI was passing_
  * [ ] Check all tests are passing locally using `make test`
    * [ ] Ensure that only `ETH_RPC_URL` is being used from env (i.e. no `match`, `block` or similar are active in your env)

_Insert your passing local tests here_

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
  * [ ] `make diff-archive-spell` for current date or or `date="YYYY-MM-DD" make diff-archive-spell` (date as per cast timestamp)
  * [ ] Ensure date corresponds to target Exec Sheet date
* [ ] Tests
  * [ ] Ensure that the `DssExecLib.address` file is not being modified by the spell PR
  * [ ] Check all CI tests are passing as at the latest commit
    _Insert most recent commit hash where CI was passing_
  * [ ] Check all tests are passing locally using `make test`
    * [ ] Ensure that only `ETH_RPC_URL` is being used from env (i.e. no `match`, `block` or similar are active in your env)

_Insert your passing local tests here_

## Cast and Merge Stage

* [ ] Spell is Cast
  * [ ] Check Cast Trace (via [EthTx](https://ethtx.info/))
    * [ ] Ensure no reverts are present that block execution
      * [ ] Inspect low level call reverts if expected
    * [ ] Ensure all actions are executed and no out-of-gas errors are present
* [ ] Ensure that no commits or changes have occurred since the spell was deployed and archived
* [ ] Approve spell PR for merge via 'Approve' review option
