* [ ] RWA Onboarding
  * [ ] Deployed Contracts
    * [ ] `RwaToken` (Token Used as Collateral In Adapter)
      * [ ] deployed via `RwaTokenFactory`
        * [ ] Fab matches [chainlog](https://chainlog.makerdao.com/)
        * [ ] Rwa Token transferred to MCD Pause Proxy
        * [ ] `createRwaToken` parameters are correct
         * [ ] `name` is in `RWA-XXX` format
         * [ ] `symbol` is in `RWAXXX` format
         * [ ] `recipient` matches MCD Pause Proxy
    * [ ] `AuthGemJoin` (Join Adapter)
      * [ ] deployed via [JoinFab](https://github.com/makerdao/JoinFab/blob/master/src/JoinFab.sol)
        * [ ] Fab matches [chainlog](https://chainlog.makerdao.com/)
        * [ ] `newAuthGemJoin` parameters are correct
          * [ ] `owner` matches MCD Pause Proxy
          * [ ] `ilk` is the `bytes32` representation of "RWAXXX-A"
            * [ ] `seth --to-ascii <bytes32>` matches ASCII Ilk
            * [ ] `seth --to-bytes32 $(seth --from-ascii "RWAXXX-A")` matches `bytes32`
          * [ ] `gem` matches `RwaToken` deployed contract
    * [ ] `RwaUrn2`
      * [ ] contract is verified on etherscan
        * [ ] ensure `0.6.12` solc version is used
        * [ ] ensure optimization are off
        * [ ] ensure license is specified
        * [ ] ensure source matches github code and it's consistent with previous RWA onboarding
      * [ ] constructor args are correct and match [chainlog](https://chainlog.makerdao.com/)
        * [ ] `vat`
        * [ ] `jug`
        * [ ] `gemJoin`
        * [ ] `daiJoin`
        * [ ] `outputConduit` matches `RwaOutputConduit<2,3>`
      * [ ] check `wards`
        * [ ] `MCD_PAUSE_PROXY` is relied
        * [ ] deployer is denied
        * [ ] no other address has been relied
    * [ ] `RwaJar`
      * [ ] contract is verified on etherscan
        * [ ] ensure `0.6.12` solc version is used
        * [ ] ensure optimization are off
        * [ ] ensure license is specified
        * [ ] ensure source matches github code and it's consistent with previous RWA onboarding
      * [ ] constructor arg matches [chainlog](https://chainlog.makerdao.com/)
        * [ ] `chainlog`
      * [ ] no `wards`
    * [ ] `RwaInputConduit<2,3>`
      * [ ] contract is verified on etherscan
        * [ ] ensure `0.6.12` solc version is used
        * [ ] ensure optimization are off
        * [ ] ensure license is specified
        * [ ] ensure source matches github code and it's consistent with previous RWA onboarding
      * [ ] constructor args are correct and match [chainlog](https://chainlog.makerdao.com/)
        * [ ] `dai`
        * [ ] `to` (`RwaUrn`, `RwaJar`)
        * [ ] `gov` (`v1`)
        * [ ] `psm`, `gem` (`v3`)
      * [ ] check `wards`
        * [ ] `MCD_PAUSE_PROXY` is relied
        * [ ] deployer is denied
        * [ ] no other address has been relied
    * [ ] `RwaOutputConduit<2,3>`
      * [ ] contract is verified on etherscan
        * [ ] ensure `0.6.12` solc version is used
        * [ ] ensure optimization are off
        * [ ] ensure license is specified
        * [ ] ensure source matches github code and it's consistent with previous RWA onboarding
      * [ ] constructor args are correct and match [chainlog](https://chainlog.makerdao.com/)
        * [ ] `dai`
        * [ ] `gov` (`v1`)
        * [ ] `psm`, `gem` (`v3`)
      * [ ] check `wards`
        * [ ] `MCD_PAUSE_PROXY` is relied
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
      * [ ] `vat.init.ilk` (this will set `duty` to `RAY` (zero ptc) by default)
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
     * [ ] Filable
        * [ ] `quitTo`
     * [ ] New Chainlog Entries
        * [ ] `RWAXXX`
        * [ ] `PIP_RWAXXX` precomputed via `cast compute-address $MIP21_LIQUIDATION_ORACLE`
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
        * [ ] `testRWAXXX_OPERATOR_LOCK_DRAW_CAGE`
        * [ ] `testFailRWAXXX_PUSH_ABOVE_LINE`
        * [ ] `testRWAXXX_SPELL_LOCK`
        * [ ] `addresses_<mainnet, goerli>.sol`
        * [ ] `config.sol`
