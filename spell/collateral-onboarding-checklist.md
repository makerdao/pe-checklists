* [ ] Collateral Onboarding
  * [ ] Deployed Contracts
    * [ ] `PIP` (Oracle)
      * [ ] contract is verified on etherscan
        * [ ] ensure solc version matches source
        * [ ] ensure optimization matches source
        * [ ] ensure license `AGPLv3` is specified
        * [ ] ensure source matches github code
      * [ ] constructor args are correct
        * [ ] `src`
        * [ ] `wat`, `orb0`, `orb1` or `orbs` (LPs)
      * [ ] deployed via Factory
        * [ ] Fab matches [chainlog](https://chainlog.makerdao.com/)
      * [ ] check `wards`
        * [ ] `MCD_PAUSE_PROXY` is relied
        * [ ] deployer is denied
        * [ ] no other address has been relied
    * [ ] `Join` (Join Adapter)
      * [ ] contract is verified on etherscan
        * [ ] ensure `0.5.12` solc version is used
        * [ ] ensure optimization are off
        * [ ] ensure license `AGPLv3` is specified
        * [ ] ensure source matches github code
      * [ ] constructor args are correct and match [chainlog](https://chainlog.makerdao.com/)
        * [ ] `vat`
        * [ ] `ilk` is the `bytes32` representation of "RWAXXX-A"
          * [ ] `seth --to-ascii <bytes32>` matches ASCII Ilk
          * [ ] `seth --to-bytes32 $(seth --from-ascii "RWAXXX-A")` matches `bytes32`
        * [ ] `gem` matches `RwaToken` contract deployed
      * [ ] deployed via [JoinFab](https://github.com/makerdao/JoinFab/blob/master/src/JoinFab.sol)
        * [ ] Fab matches [chainlog](https://chainlog.makerdao.com/)
        * [ ] Source is AutoVerified
      * [ ] check `wards`
        * [ ] `MCD_PAUSE_PROXY` is relied
        * [ ] deployer is denied
        * [ ] no other address has been relied
    * [ ] `Clip`
      * [ ] contract is verified on etherscan
        * [ ] ensure `0.6.12` solc version is used
        * [ ] ensure optimization are off
        * [ ] ensure license `AGPLv3` is specified
        * [ ] ensure source matches github code
      * [ ] constructor args are correct and match [chainlog](https://chainlog.makerdao.com/)
        * [ ] `vat`
        * [ ] `spotter`
        * [ ] `dog`
        * [ ] `gemJoin`
      * [ ] deployed via [ClipFab](https://github.com/makerdao/dss-deploy/blob/master/src/DssDeploy.sol)
        * [ ] Fab matches [chainlog](https://chainlog.makerdao.com/)
        * [ ] Source is AutoVerified
      * [ ] check `wards`
        * [ ] `MCD_PAUSE_PROXY` is relied
        * [ ] deployer is denied
        * [ ] no other address has been relied
    * [ ] `Calc`
      * [ ] contract is verified on etherscan
        * [ ] ensure `0.6.12` solc version is used
        * [ ] ensure optimization are off
        * [ ] ensure license `AGPLv3` is specified
        * [ ] ensure source matches github dss `abaci` code
      * [ ] no constructor arguments
      * [ ] deployed via [ClipFab](https://github.com/makerdao/dss-deploy/blob/master/src/DssDeploy.sol)
        * [ ] Fab matches [chainlog](https://chainlog.makerdao.com/)
        * [ ] Source is AutoVerified
      * [ ] check `wards`
        * [ ] `MCD_PAUSE_PROXY` is relied
        * [ ] deployer is denied
        * [ ] no other address has been relied
    * [ ] Risk Parameters
      * [ ] `vat.ilk.line` ([setIlkDebtCeiling](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L611))
        * [ ] Autoline Enabled?
          * [ ] `vat.ilk.line` should be set to DC-IAM `gap` value
      * [ ] `dog.ilk.hole` ([setIlkMaxLiquidationAmount](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L699))
      * [ ] `ilk.hole` >= `ilk.dust` invariant satisfied
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
      * [ ] `abaci`
        * [ ] [LinearDecrease](https://github.com/makerdao/dss/blob/v0.0.9/src/abaci.sol#L29)
          * [ ] `calc.tau` ([setLinearDecrease](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L811))
        * [ ] [StairstepExponentialDecrease](https://github.com/makerdao/dss/blob/v0.0.9/src/abaci.sol#L91)
          * [ ] [setStairstepExponentialDecrease](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L822)
          * [ ] `calc.cut`
          * [ ] `calc.step`
    * [ ] Autoline ([setIlkAutoLineParameters](https://github.com/makerdao/dss-exec-lib/blob/v0.0.9/src/DssExecLib.sol#L648))
      * [ ] `ilk`
      * [ ] `line`
      * [ ] `gap`
      * [ ] `ttl`
    * [ ] Test Coverage (Follow Previous Test Patterns)
      * [ ] `testCollateralIntegrations`
      * [ ] `testNewChainlogValues`
      * [ ] `testNewIlkRegistryValues`
      * [ ] `addresses_<mainnet, goerli>.sol`
      * [ ] `config.sol`
