# Goerli Executive Spell Review Checklist

## Goerli YYYY-MM-DD

Spell Actions:

* TODO
* TODO

## Coding Stage

* [ ] Office Hours
  * [ ] Off (default, spell casted after deploying, pause delay 60 sec.)
* [ ] Exec Hash
  * [ ] empty (default)
  * [ ] ensure `description` is `Goerli Spell`
* [ ] 30 Days Expiry
* [ ] `lib` (`git submodule update --init --recursive` to install/update)
  * [ ] `dss-exec-lib`
    * [ ] `DssExecLib.address` is unchanged by the spell PR (if changed, the new address must match the [Latest Release Tag](https://github.com/makerdao/dss-exec-lib/releases/latest) and be approved as a legitimate new release by GovAlpha)
    * [ ] if submodule upgrades are present make sure `dss-exec-lib` is synced as well
    * [ ] git submodule hash (run `git submodule status`) matches the latest release version or newer (NOTE: `dss-exec-lib` as installed locally will use GitHub code more recent than the 0.0.9 release)
  * [ ] `dss-test`
    * [ ] `dss-interfaces`
      * [ ] git submodule hash matches [version used by `dss-test`](https://github.com/makerdao/dss-test/tree/master/lib) (Non-critical)
    * [ ] `forge-std`
      * [ ] git submodule hash matches [version used by `dss-test`](https://github.com/makerdao/dss-test/tree/master/lib) (Non-critical)
* [ ] `dss-interfaces`
  * [ ] used in the current spell
  * [ ] cleanup previous ones
  * [ ] ensure only single import layout is used (e.g. `import "dss-interfaces/dss/VatAbstract.sol";`)
* [ ] Static Interfaces
  * [ ] ensure they match `dss-interfaces` (Where there is a mismatch, use `cast interface` as the source of truth)
  * [ ] check on-chain interface of deployed contract via `cast interface <contract_address>` to ensure correctness
  * [ ] interface naming style should match with `Like` suffix (e.g. `VatLike`), with some [exceptions](https://github.com/makerdao/dss-exec-lib/blob/master/src/DssExecLib.sol#L24)
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
    * [ ] Variable visibility declared as `internal`
    * [ ] State mutability declared as `constant`
  * [ ] Units
    * [ ] `HUNDRED  = 10 ** 2`
    * [ ] `THOUSAND = 10 ** 3`
    * [ ] `MILLION  = 10 ** 6`
    * [ ] `BILLION  = 10 ** 9`
    * [ ] Variable visibility declared as `internal`
    * [ ] State mutability declared as `constant`
    * [ ] Ensure they match with [config](https://github.com/makerdao/spells-mainnet/blob/master/src/test/config.sol)
* [ ] Deployed Contracts (not yet on chainlog or new to chainlog)
  * [ ] Verified on etherscan
  * [ ] Optimizations match Repo
  * [ ] `GNU AGPLv3` license
  * [ ] Constructor args ok (e.g. `vat`, `dai`, `dog`, ...)
    * [ ] Match [ChainLog](https://chainlog.makerdao.com/)
  * [ ] Wards ok (pause proxy relied, deployer denied)
    * [ ] `MCD_ESM` is already relied / being relied this spell (as approved by GovAlpha) in order to allow de-authing the pause proxy during Emergency Shutdown, via `denyProxy`.
  * [ ] Matches corresponding github source code (i.e. diffcheck via vscode `code --diff etherscan.sol github.sol`)
  * [ ] Ensure deployer address is included into `addresses_deployers.sol` (**to keep up to date**)
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
* [ ] Risk Parameter Changes
  * [ ] `dog.ilk.hole` ([setIlkMaxLiquidationAmount](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L699))
  * [ ] `vat.ilk.dust`([setIlkMinVaultAmount](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L676))
  * [ ] `dog.ilk.chop` ([liquidationPenalty](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L689))
  * [ ] `jug.ilk.duty` ([setIlkStabilityFee](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L792))
    * [ ] Comment matches pattern `// Increase ILK-A Stability Fee by X.XX% from X.XX% to X.XX%.`
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
      * [ ] `end` matches Var `TARGET_XXX_A_MAT` (Mach Exec Doc & Risk Computations)
        * [ ] Check IF Target `mat` Covers All Remaining Vaults CR times Risk Multiplier Factor
      * [ ] `duration` matches Exec Doc
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
        * [ ] New `val` is calculated with `line * [(1 + duty) ** years] * mat` and makes sense in context of the [rate mechanism](https://github.com/makerdao/developerguides/blob/master/mcd/intro-rate-mechanism/intro-rate-mechanism.md). Minimum duration is usually in the exec doc of the spell with the RWAXXX ilk onboarding.
        * [ ] Comment explaining `val` formula (`Debt ceiling * [ (1 + RWA stability fee ) ^ (minimum deal duration in years) ] * liquidation ratio`) is present
        * [ ] Accompanying comment above `bump` line in format `// XXMM * 1.XX^X * X.XX as a WAD` corresponding to the `val` calculation formula (e.g. `// 15MM * 1.03^2 * 1.00 as a WAD`) is present along with the calculation formula on the line above
        * [ ] IF combining `val` of multiple RWA ilks being combined, `val` calculation is done once per ilk and added to make the total, with workings provided in code comments. The existing `val` value can be retrieved by calling `read()` on `PIP_RWAXX` and converting the result into decimal.
    * [ ] Poke `spotter` to pull in the new price
  * [ ] Debt Ceiling (`line`) + Liquidation Oracle Price Bump (`val`)
    * [ ] Increase Ilk Debt Ceiling (set DC + increase Global DC)
    * [ ] `bump` `RwaLiquidationOracle` with new computed increased price (`val`)
      * [ ] `val` should enable DAI to be drawn over the loan period while taking into account the configured `ink` amount, interest rate and liquidation ratio (see below)
        * [ ] New `val` is calculated with `line * [(1 + duty) ** years] * mat` and makes sense in context of the [rate mechanism](https://github.com/makerdao/developerguides/blob/master/mcd/intro-rate-mechanism/intro-rate-mechanism.md). Minimum duration is usually in the exec doc of the spell with the RWAXXX ilk onboarding.
        * [ ] Comment explaining `val` formula (`Debt ceiling * [ (1 + RWA stability fee ) ^ (minimum deal duration in years) ] * liquidation ratio`) is present
        * [ ] Accompanying comment above `bump` line in format `// XXMM * 1.XX^X * X.XX as a WAD` corresponding to the `val` calculation formula (e.g. `// 15MM * 1.03^2 * 1.00 as a WAD`) is present along with the calculation formula on the line above
        * [ ] IF combining `val` of multiple RWA ilks being combined, `val` calculation is done once per ilk and added to make the total, with workings provided in code comments. The existing `val` value can be retrieved by calling `read()` on `PIP_RWAXX` and converting the result into decimal.
    * [ ] Poke `spotter` to pull in the new price
* [ ] SubDAO Content
  * [ ] SubDAO SubProxy spell execution
    * [ ] SubDAO spell address matches exec doc
    * [ ] SubDAO spell deployer is in `addresses_deployers`
    * [ ] Executed using `ProxyLike(SUBDAO_PROXY).exec(SUBDAO_SPELL, abi.encodeWithSignature("execute()"));`
    * [ ] Execution is NOT delegate call
    * [ ] Gas cost will not be too high (low level call gas estimation is not done by our scripts)
  * [ ] Maker Core (main spell) SubDAO actions (i.e. operate in Pause Proxy `DelegateCall` context)
    * [ ] No SubDAO contract being interacted with is authed on a core contract like vat, etc. (script this eventually)
    * [ ] SubDAO contract licensing and optimizations do not matter (not strictly)
    * [ ] SubDAO contracts and all libraries / dependencies are verified (Blocking if not true)
    * [ ] Upgradable SubDAO contracts
      * [ ] Any upgradable contracts have the `PAUSE_PROXY` as their `admin` (i.e. the party that can upgrade)
        * [ ] Any upgradable SubDAO contracts with an `admin` that is not `PAUSE_PROXY` are not authed on any core contracts (Blocking)
    * [ ] All SubDAO content addresses (i.e. provided contract addresses or EOAs) present in the Maker Core spell are present in the exec sheet and are correct
    * [ ] SubDAO actions match exec sheet (only where inline with main spell code) and do not affect core contracts
    * [ ] Core contract knock-on actions (such as offboarding or setting DC to 0) are present in the exec and match the code
    * [ ] External calls for SubDAO content are NOT delegate call
    * [ ] Code does not have untoward behavior within the scope of Maker Core Contracts (e.g. up to the SubDAO proxy)
* [ ] ChainLog
  * [ ] Bump ChainLog, accordingly with spec (major, minor, patch)
    * [ ] MAJOR -> New Vat
    * [ ] MINOR -> Core Module (DSS) Update (e.g. Flapper)
    * [ ] PATCH -> Collateral addition or addition/modification
* [ ] `addresses_goerli.sol` matches spell code
* [ ] Ensure every spell variable is declared as public/internal
* [ ] Ensure `immutable` visibility is only used when fetching addresses from the `ChainLog` via `DssExecLib.getChangelogAddress` and `constant` is used instead for static addresses
  * [ ] Fetch addresses as type `address` and wrap with `Like` suffix interfaces inline (when making calls) unless archive patterns permit otherwise (Such as `MKR`)
  * [ ] Use the [DssExecLib Core Address Helpers](https://github.com/makerdao/dss-exec-lib/blob/master/src/DssExecLib.sol#L166) where possible (e.g. `DssExecLib.vat()`)
  * [ ] Variable names of addresses fetched from the `ChainLog` match the corresponding ChainLog key (e.g. `MCD_VAT` rather than `vat`)
* [ ] Spell actions match [GovAlpha Spell Content Sheet](https://docs.google.com/spreadsheets/d/1w_z5WpqxzwreCcaveB2Ye1PP5B8QAHDglzyxKHG3CHw)
* [ ] Local Tests PASS
  * [ ] Ensure Good Coverage
  * [ ] Ensure every test function is declared as public if enabled or private if disabled

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
    * [ ] `deployed_spell_created` matches deployment timestamp
    * [ ] `deployed_spell_block` matches deployment block number
  * [ ] manual checks
      * [ ] Ensure `make deploy-info tx=<tx>` matches [config](https://github.com/makerdao/spells-mainnet/blob/master/src/test/config.sol)
        * [ ] `deployed_spell_created` timestamp
        * [ ] `deployed_spell_block` block number
      * [ ] Ensure Etherscan `Libraries Used` matches DssExecLib [Latest Release](https://github.com/makerdao/dss-exec-lib/releases/latest)
       * [ ] git submodule hash matches [dss-exec-lib](https://github.com/makerdao/dss-exec-lib/releases/latest) latest release's tag commit and inspect diffs if doesn't match to ensure expected behaviour (Currently Non-Critical pending the next DssExecLib release, double check that exec lib used by the contract matches the latest release)
* [ ] Archive matches `src`
  * [ ] `make diff-archive-spell` for current date or or `date="YYYY-MM-DD" make diff-archive-spell` (date as per cast timestamp)
  * [ ] ensure date corresponds to target exec date
* [ ] CI tests are passing
* [ ] Local Tests PASS

_Insert your passing local tests here_

## Cast Stage

* [ ] Spell is Cast (**only on Goerli**)
  * [ ] Check Cast Trace (via [EthTx](https://ethtx.info/))
    * [ ] Ensure no reverts are present that block execution
      * [ ] Inspect low level call reverts if expected
    * [ ] Ensure all actions are executed and no out-of-gas errors are present
* [ ] CI tests are passing
* [ ] Local Tests PASS

_Insert your passing local tests here_