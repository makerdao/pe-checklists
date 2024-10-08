# PSM Checklists

## PSM Migration Checklist

- [ ] IF LitePSM MOM is being on-boarded in the current spell, execute the full [LitePSM MOM Onboarding Checklist](#litepsm-mom-onboarding-checklist)
- [ ] IF LitePSM contracts are being on-boarded in the current spell, execute the full [LitePSM Onboarding Checklist](#litepsm-onboarding-checklist)
- [ ] IF a PSM is deprecated in the migration, execute the full [PSM Offboarding Checklist](#psm-offboarding-checklist)
- [ ] IF present, max amount of gems to move (`dstWant`) matches the Exec Sheet
- [ ] IF present, min amount of gems to keep (`srcKeep`) matches the Exec Sheet
- Source PSM:
    - [ ] ilk matches source PSM
    - [ ] IF present, `tin` matches the value in the Exec Sheet
    - [ ] IF present, `tout` matches the value in the Exec Sheet
    - [ ] `AutoLine` is updated according to the Exec Sheet
- Destination PSM:
    - [ ] ilk matches destination PSM
    - [ ] IF present, `tin` matches the value in the Exec Sheet
    - [ ] IF present, `tout` matches the value in the Exec Sheet
    - [ ] `AutoLine` is updated according to the Exec Sheet
    - [ ] `buf` matches the value in the Exec Sheet
    - [ ] `fill` is called conditionally (`if(rush() > 0)`) to ensure liquidity is immediately available after spell execution
- Test coverage:
    - [ ] Collateral amount that is fetched from the source PSM matches the collateral is added to the destination PSM
        - [ ] `vat.urns(DST_ILK, dstPsm).art` is increased **at least** by the moved amount
        - [ ] `gem` balance of `dstPocket` is increased **exactly** by the moved amount
        - [ ] Dai balance of `dstPsm` is equal to `dstBuf`, UNLESS limited by the debt ceiling (`line`)
        - [ ] `vat.urns(SRC_ILK, srcPsm).art` is decreased **exactly** by the moved amount
        - [ ] `vat.urns(SRC_ILK, srcPsm).ink` is decreased **exactly** by the moved amount
        - [ ] `gem` balance of `srcPsm.gemJoin()` is decreased **exactly** by the moved amount
    - [ ] No bad debt is left behind
        - [ ] `vat.vice()` is unchanged after the spell execution
        - [ ] `vat.sin(MCD_PAUSE_PROXY)` is unchanged after the spell execution

## LitePSM MOM Onboarding Checklist

- Deployed Contracts
    - `DssLitePsmMom`
        - [ ] Optimizer is **enabled**
        - [ ] Optimize runs is set to `200`
        - [ ] Contract has been reviewed by minimum 2 specialized [Active Ecosystem Actors](https://mips.makerdao.com/mips/details/MIP101#2-8-2-active-ecosystem-actors)
        - [ ] Contract has been audited by an external party
            - [ ] Audit report source could be confirmed (i.e. found on auditor's website)
            - [ ] All issues found were fixed or acknowledged
            - [ ] No code changes are done after final audit report, based on the commit hash found in the report + 
              - [ ] EITHER code diff check for each individual file
              - [ ] OR bytecode verification tool such as `forge verify-bytecode`
        - [ ] Deployed contract matches the code in the [repo](https://github.com/makerdao/dss-lite-psm/)
        - [ ] Deployer is no longer the `owner`
        - [ ] `MCD_PAUSE_PROXY` is the `owner` of the contract (i.e. `require(MomLike(MOM).owner() == MCD_PAUSE_PROXY`))
- Initialization:
    - `DssLitePsmMom`:
        - [ ] The chief (`MCD_ADM`) is set as the `authority` (i.e. `MomLike(MOM).setAuthority(MCD_ADM)`)
- Test Coverage:
    - `DssLitePsmMom`:
        - [ ] The chief (`MCD_ADM`) is set as the `authority` (i.e. `MomLike(MOM).setAuthority(MCD_ADM)`)

## LitePSM Onboarding Checklist

- Deployed Contracts
    - `DssLitePsm`
        - [ ] Optimizer is **enabled**
        - [ ] Optimize runs is set to `200`
        - [ ] Contract has been reviewed by minimum 2 specialized [Active Ecosystem Actors](https://mips.makerdao.com/mips/details/MIP101#2-8-2-active-ecosystem-actors)
        - [ ] Contract has been audited by an external party
            - [ ] Audit report source could be confirmed (i.e. found on auditor's website)
            - [ ] All issues found were fixed or acknowledged
            - [ ] No code changes are done after final audit report, based on the commit hash found in the report + 
              - [ ] EITHER code diff check for each individual file
              - [ ] OR bytecode verification tool such as `forge verify-bytecode`
        - [ ] Deployed contract matches the code in the [repo](https://github.com/makerdao/dss-lite-psm/)
        - Constructor params:
            - [ ] ilk is named `LITE-PSM-{TOKEN_SYMBOL}-A` (i.e. `LITE-PSM-USDC-A`)
            - [ ] `gem` matches the expected token address
            - [ ] `daiJoin` matches `MCD_JOIN_DAI` from the chainlog
            - [ ] `pocket` matches the pocket address in the Exec Sheet
        - [ ] Deployer no longer has privileged access (`wards(deployer) == 0`)
        - [ ] `MCD_PAUSE_PROXY` has been authed (i.e. `require(WardsLike({LITE_PSM}).wards(MCD_PAUSE_PROXY) == 1)`)
        - [ ] Sanity check: `DssLitePsm` has `type(uint256).max` approval to spend `gem` on behalf of `pocket`
    - `LitePsmJob`
        - [ ] Uses the default Solidity version in [`dss-cron`](https://github.com/makerdao/dss-cron)
        - [ ] Optimizer is **enabled**
        - [ ] Deployed contract matches the code in the [repo](https://github.com/makerdao/dss-cron)
        - Constructor params:
            - [ ] `_sequencer` matches `CRON_SEQUENCER` address from the chainlog
            - [ ] `_litePsm` matches `{LITE_PSM}` address
            - [ ] `_rushThreshold` matches the Exec Sheet (it might be named as "fill threshold")
            - [ ] `_gushThreshold` matches the Exec Sheet (it might be named as "trim threshold")
            - [ ] `_cutThreshold` matches the Exec Sheet (it might be named as "chug threshold")
- Initialization
    - `DssLitePsm`:
        - [ ] `buf` is set to the value specified in the Exec Sheet
        - [ ] `vow` is set to `MCD_VOW` from the chainlog
        - [ ] `MCD_PAUSE_PROXY` is whitelisted to execute swaps with no fees (i.e. `KissLike({LITE_PSM}).kiss(MCD_PAUSE_PROXY)`)
        - [ ] `DssLitePsmMom` is authed (i.e. `RelyLike({LITE_PSM}).rely(MOM)`)
    - `LitePsmJob`:
        - [ ] Job is added to `CRON_SEQUENCER`
    - `MCD_VAT`:
        - [ ] New ilk is initialized
    - `MCD_JUG`:
        - [ ] New ilk is initialized
    - `MCD_SPOT`:
        - [ ] Sanity check: Dai parity (`par`) current value is 1 (`1 * RAY`)
        - [ ] Collateralization ratio (`mat`) is set to 100% (`1 * RAY`) for the ilk 
        - [ ] IF a new `pip` is being used:
            - [ ] `pip` is set for the ilk
            - [ ] `pip` is a `DSValue` instance
            - [ ] The value on `pip` is set to 1 (`1 * WAD`)
            - [ ] `pip` deployer is no longer the `owner`
            - [ ] `MCD_PAUSE_PROXY` is the `owner` on `pip`
        - [ ] OTHERWISE when reusing an existing `pip`:
            - [ ] `pip` is set for the ilk
            - [ ] `pip` in the chainlog matches `gem` symbol (i.e. `PIP_USDC` for `USDC`)
    - `ILK_REGISTRY`:
        - [ ] New ilk is added to the registry
    - `CHAINLOG`:
        - [ ] `DssLitePsm` is added to the chainlog and `addresses_mainnet.sol` according to the Exec Sheet
        - [ ] `pocket` is added to the chainlog and `addresses_mainnet.sol` according to the Exec Sheet
        - [ ] `DssLitePsmMom` is added to the chainlog and `addresses_mainnet.sol` according to the Exec Sheet
        - [ ] `LitePsmJob` is added to the chainlog and `addresses_mainnet.sol` according to the Exec Sheet
        - [ ] IF a new `pip` is being added, it is added to the chainlog and `addresses_mainnet.sol` as `PIP_{TOKEN_SYMBOL}` (i.e. `PIP_USDC`)
- Test coverage:
    - `DssLitePsm`:
        - [ ] `vow` is set to `MCD_VOW`
        - [ ] `buf` matches the Exec Sheet
        - [ ] `MCD_PAUSE_PROXY` is whitelisted to execute swaps with no fees (i.e. `KissLike({LITE_PSM}).bud(MCD_PAUSE_PROXY) == 1`)
        - [ ] `DssLitePsmMom` is authed (i.e. `WardsLike({LITE_PSM}).wards(MOM) == 1`)
        - E2E tests:
            - [ ] `buyGem` works as expected
            - [ ] `sellGem` works as expected
            - [ ] `buyGemNoFee` works as expected
            - [ ] `sellGemNoFee` works as expected
    - `DssLitePsmMom`:
        - E2E tests:
            - [ ] `halt` prevents new swaps in `{LITE_PSM}`
    - `LitePsmJob`: 
        - [ ] Job is added to `CRON_SEQUENCER` (i.e.: `SequencerLike(CRON_SEQUENCER).hasJob(LITE_PSM_JOB) == true`)
        - [ ] `sequencer` matches `CRON_SEQUENCER` address from the chainlog
        - [ ] `litePsm` matches `{LITE_PSM}` address
        - [ ] `rushThreshold` matches the Exec Sheet (it might be named as "fill threshold")
        - [ ] `gushThreshold` matches the Exec Sheet (it might be named as "trim threshold")
        - [ ] `cutThreshold` matches the Exec Sheet (it might be named as "chug threshold")
        - E2E tests:
            - `work` has the desired effect:
                - [ ] IF `rush() > _rushThreshold`, `fill` is called
                - [ ] OTHERWISE IF `cut() > _cutThreshold`, `chug` is called
                - [ ] OTHERWISE IF `gush() > _gushThreshold`, `trim` is called
                - [ ] OTHERWISE it reverts
    - `MCD_VAT`:
        - [ ] `urns[ilk][{LITE_PSM}].ink` is set to the max value that will not cause an overflow (`int256(type(uint256).max / RAY)`)
    - `MCD_JUG`:
        - [ ] Stability fee (`duty`) is set to 0% (`1 * RAY`) for the ilk (:information_source: covered in `config.sol`)
    - `MCD_SPOT`:
        - [ ] `spotter(DST_ILK).mat` is set to 100% (`1 * RAY`) (:information_source: covered in `config.sol`)
        - [ ] `spotter(DST_ILK).pip` is set correctly 
    - `CHAINLOG`:
        - [ ] Version is bumped: `{x}.{y}.{z+1}` (:information_source: covered in `config.sol`)
    - `ILK_REGISTRY`:
        - [ ] New ilk is added to the registry
        - [ ] `name` matches `gem` name
        - [ ] `symbol` matches `gem` symbol
        - [ ] `class` for join-less ilks is used (`REG_CLASS = 6`)
        - [ ] `gem` matches the configured gem
        - [ ] `pip` matches the configured pip
        - [ ] `gemJoin` is set to `address(0)`
        - [ ] `clip` is set to `address(0)`

## PSM Offboarding Checklist

- [ ] ilk is removed from `AutoLine`
- [ ] ilk `line` is set to `0`
- [ ] `ink` for the PSM is set to 0 on `MCD_VAT` after execution, UNLESS there has been a donation
- [ ] Global `Line` is adjusted accordingly
- [ ] ilk is removed from `ILK_REGISTRY`
- [ ] PSM contracts (`Psm`, `GemJoin` and others, as applicable) are being removed from the chainlog
- [ ] Chainlog version is bumped: `{x}.{y+1}.0`
- [ ] PSM contracts (`Psm`, `GemJoin` and others, as applicable) are being scuttled
    - [ ] `MCD_PAUSE_PROXY` is de-authed from all applicable contracts
