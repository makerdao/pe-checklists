# RWA Onboarding Checklist
  * [ ] Deployed Contracts
    * [ ] `RwaToken` (Token Used as Collateral In Adapter)
      * [ ] deployed via [`RwaTokenFactory`](https://github.com/makerdao/rwa-toolkit/blob/master/src/tokens/RwaTokenFactory.sol)
        * [ ] Fab matches [chainlog](https://chainlog.makerdao.com/)
        * [ ] ensure source matches [GitHub code](https://github.com/makerdao/rwa-toolkit/blob/92c79aac24ef7645902ce4be57ba41b19e6c7dd5/src/tokens/RwaToken.sol) and it's consistent with previous RWA onboarding
        * [ ] Rwa Token transferred to MCD Pause Proxy
        * [ ] `createRwaToken` parameters are correct
         * [ ] `name` is in `RWA-XXX` format
         * [ ] `symbol` is in `RWAXXX` format
         * [ ] `recipient` matches MCD Pause Proxy
    * [ ] `AuthGemJoin` (Join Adapter)
      * [ ] deployed via [JoinFab](https://github.com/makerdao/JoinFab/blob/master/src/JoinFab.sol)
        * [ ] Fab matches [chainlog](https://chainlog.makerdao.com/)
        * [ ] ensure source matches [GitHub code](https://github.com/makerdao/dss-gem-joins/blob/8ca0a7fdd5edc6ed3da68c3ffdfadfb9540c83f7/src/join-auth.sol) and it's consistent with previous RWA onboarding (NOTE: Etherscan may have a bug where it displays additional code for `AuthGemJoin`. In this case, using `cast interface` or otherwise, ensure that this code cannot be executed)
        * [ ] `newAuthGemJoin` parameters are correct
          * [ ] `owner` matches `PAUSE_PROXY`
          * [ ] `ilk` is the `bytes32` representation of "RWAXXX-A"
            * [ ] `cast --to-ascii <bytes32>` matches ASCII Ilk
            * [ ] `cast --to-bytes32 $(cast --from-ascii "RWAXXX-A")` matches `bytes32` (to check the amount of padding zeroes is correct)
          * [ ] `gem` matches `RwaToken` deployed contract
        * [ ] check `wards`
          * [ ] `MCD_PAUSE_PROXY` is relied
          * [ ] `MCD_ESM` is being relied in the spell (as approved by GovAlpha)
          * [ ] deployer is denied
        * [ ] no other address has been relied
    * [ ] `RwaUrn2`
      * [ ] contract is verified on etherscan
        * [ ] ensure `0.6.12` solc version is used
        * [ ] ensure optimizations are off
        * [ ] ensure license is specified (SPDX in code or otherwise)
        * [ ] ensure source matches [GitHub code](https://github.com/makerdao/rwa-toolkit/blob/92c79aac24ef7645902ce4be57ba41b19e6c7dd5/src/urns/RwaUrn2.sol) and it's consistent with previous RWA onboardings
      * [ ] constructor args are correct and match [Chainlog](https://chainlog.makerdao.com/)
        * [ ] `vat` matches `MCD_VAT`
        * [ ] `jug` matches `MCD_JUG`
        * [ ] `gemJoin` matches `MCD_JOIN_RWAXXX_A` (Per spell code)
        * [ ] `daiJoin` matches `MCD_JOIN_DAI`
        * [ ] `outputConduit` matches `RwaOutputConduit<2,3>` (Per spell code)
      * [ ] check `wards`
        * [ ] `MCD_PAUSE_PROXY` is relied
        * [ ] `MCD_ESM` is being relied in the spell (as approved by GovAlpha)
        * [ ] deployer is denied
        * [ ] no other address has been relied
    * [ ] `RwaJar`
      * [ ] contract is verified on etherscan
        * [ ] ensure `0.6.12` solc version is used
        * [ ] ensure optimization are off
        * [ ] ensure license is specified (SPDX in code or otherwise)
        * [ ] ensure source matches [GitHub code](https://github.com/makerdao/rwa-toolkit/blob/92c79aac24ef7645902ce4be57ba41b19e6c7dd5/src/jars/RwaJar.sol) and it's consistent with previous RWA onboarding
      * [ ] constructor arg matches [`ChainLog`](https://chainlog.makerdao.com/)
        * [ ] `chainlog`
      * [ ] no `wards`
    * [ ] `RwaInputConduit<2,3>`
      * [ ] contract is verified on etherscan
        * [ ] ensure `0.6.12` solc version is used
        * [ ] ensure optimization are off
        * [ ] ensure license is specified (SPDX in code or otherwise)
        * [ ] ensure source matches [GitHub code](https://github.com/makerdao/rwa-toolkit/blob/92c79aac24ef7645902ce4be57ba41b19e6c7dd5/src/conduits/RwaSwapInputConduit2.sol) and it's consistent with previous RWA onboarding
      * [ ] constructor args are correct and match [chainlog](https://chainlog.makerdao.com/)
        * [ ] `dai`
        * [ ] `to` (`RwaUrn`, `RwaJar`)
        * [ ] `gov` (`v1`)
        * [ ] `psm`, `gem` (`v3`)
      * [ ] check `wards`
        * [ ] `MCD_PAUSE_PROXY` is relied
        * [ ] `MCD_ESM` is being relied in the spell (as approved by GovAlpha)
        * [ ] deployer is denied
        * [ ] no other address has been relied
    * [ ] `RwaOutputConduit<2,3>`
      * [ ] contract is verified on etherscan
        * [ ] ensure `0.6.12` solc version is used
        * [ ] ensure optimization are off
        * [ ] ensure license is specified (SPDX in code or otherwise)
        * [ ] ensure source matches [GitHub code](https://github.com/makerdao/rwa-toolkit/blob/92c79aac24ef7645902ce4be57ba41b19e6c7dd5/src/conduits/RwaSwapOutputConduit.sol) and it's consistent with previous RWA onboarding
      * [ ] constructor args are correct and match [chainlog](https://chainlog.makerdao.com/)
        * [ ] `dai`
        * [ ] `gov` (`v1`)
        * [ ] `psm`, `gem` (`v3`)
      * [ ] check `wards`
        * [ ] `MCD_PAUSE_PROXY` is relied
        * [ ] `MCD_ESM` is being relied in the spell (as approved by GovAlpha)
        * [ ] deployer is denied
        * [ ] no other address has been relied
    * [ ] Risk Parameters Match Doc
      * [ ] `duty`(stability fee)
      * [ ] `line`(debt ceiling)
      * [ ] `mat` (liquidation ratio)
      * [ ] `val` (oracle price)
      * [ ] `tau` (pre-agreed remediation period)
      * [ ] `doc` (IPFS Hash)
    * [ ] Onboarding Actions
      * [ ] `ilk` matches the format `RWAXXX-A`
      * [ ] Sanity Checks (constructor args, public vars, ... via `require` condition)
      * [ ] `oracle.init`
      * [ ] `vat.init.ilk`
      * [ ] `jug.init.ilk` (this will set `duty` to `RAY` (zero pct) by default)
      * [ ] `vat` rely `join`
      * [ ] Increase Ilk Debt Ceiling (set DC + increase Global DC)
      * [ ] File Ilk `pip` in the `spotter`
      * [ ] File Ilk `mat` in the `spotter`
      * [ ] Poke `spotter` to pull in the price
      * [ ] `join` rely `urn`
      * [ ] Access Control
        * [ ] `hope` (operator, pause proxy)
        * [ ] `mate` (operator, pause proxy as fallback if required)
        * [ ] `kiss` (whitelist for `urn` destination address `who`)
     * [ ] Filable (`RwaOutputConduit` and `RwaInputConduit`)
        * [ ] `quitTo` (`file` the appropriate address for conduits per technical assessment)
     * [ ] New Chainlog Entries
        * [ ] `RWAXXX`
        * [ ] `PIP_RWAXXX` precomputed via `cast compute-address $MIP21_LIQUIDATION_ORACLE`
          * [ ] Note that a nonce change for `MIP21_LIQUIDATION_ORACLE` prior to casting will cause this to be incorrect
        * [ ] `MCD_JOIN_RWAXXX_A`
        * [ ] `RWAXXX_A_URN`
        * [ ] `RWAXXX_A_JAR`
        * [ ] `RWAXXX_A_INPUT_CONDUIT`
        * [ ] `RWAXXX_A_OUTPUT_CONDUIT`
      * [ ] Chainlog Bump 
        * [ ] Patch `x.x.1`
      * [ ] Add Ilk to `IlkRegistry` 
        * [ ] `put` is used
        * [ ] `class` is 3
        * [ ] `name` matches forum post (e.g. "RWA007-A: Monetalis Clydesdale")
      * [ ] Ensure `DssExecLib` is used for the Onboarding (e.g. `DssExecLib.vat`)
      * [ ] Test Coverage (Follow Previous Test Patterns)
        * [ ] `testNewChainlogValues`
        * [ ] `testNewIlkRegistryValues`
        * [ ] `testRWAXXX_INTEGRATION_CONDUITS_SETUP`
        * [ ] `testRWAXXX_INTEGRATION_BUMP`
        * [ ] `testRWAXXX_INTEGRATION_TELL`
        * [ ] `testRWAXXX_INTEGRATION_TELL_CURE_GOOD`
        * [ ] `testFailRWAXXX_INTEGRATION_CURE_BEFORE_TELL`
        * [ ] `testRWAXXX_INTEGRATION_TELL_CULL`
        * [ ] `testRWAXXX_PAUSE_PROXY_OWNS_RWAXXX_TOKEN_BEFORE_SPELL`
        * [ ] `testRWAXXX_SPELL_LOCK_OPERATOR_DRAW_WIPE_FREE`
        * [ ] `testFailRWAXXX_DRAW_ABOVE_LINE`
        * [ ] `testFailRWAXXX_OUTPUT_CONDUIT_PUSH_ABOVE_BALANCE`
        * [ ] `testRWAXXX_OPERATOR_LOCK_DRAW_CAGE`
        * [ ] `testRWAXXX_SPELL_LOCK`
        * [ ] `addresses_<mainnet, goerli>.sol`
        * [ ] `config.sol`
