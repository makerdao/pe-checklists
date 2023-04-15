# Mom Onboarding Checklist
  * [ ] Deployed Contracts
    * [ ] `Mom`
      * [ ] deployed via deployer
        * [ ] ensure different deployer is used for mainnet and testnet to avoid contracts with same address and different sources
        * [ ] contract is verified on etherscan
          * [ ] ensure solc version matches source
          * [ ] ensure optimization are off
          * [ ] ensure license `AGPLv3` is specified
          * [ ] ensure source matches github code (i.e. diffcheck via vscode `code --diff etherscan.sol github.sol`)
        * [ ] constructor args are correct
        * [ ] check `owner`
          * [ ] `MCD_PAUSE_PROXY` is set as `owner` via `setOwner` (that will remove the deployer as `owner`)
        * [ ] ensure deployer is included in `addresses_deployer.sol` if not already present 
    * [ ] Onboarding Actions
      * [ ] authorize `Mom` on the circuit breaker target modules (e.g. `vat.rely(LineMom)`)
      * [ ] ensure `owner` is Pause Proxy via sanity check (e.g. `require(MomLike(MOM).owner() == PAUSE_PROXY);`)
      * [ ] file eventual fileables
      * [ ] set `authority` the `chief` (e.g. `MomLike(MOM).setAuthority(CHIEF);`)
      * [ ] consider other spell actions required to complete the onboarding like adding target contracts to local mom list (e.g. `LineMom`).
    * [ ] New Chainlog Entry
        * [ ] via `DssExecLib.setChangelogAddres("MOM", MOM);`
    * [ ] Chainlog Bump
      * [ ] Patch `x.x.+1`
      * [ ] via `DssExecLib.setChangelogVersion("1.14.x");`
    * [ ] Test Coverage (Follow Previous Test Patterns)
      * [ ] `testMom`
      * [ ] ensure new chainlog entries are included in `addresses_<mainnet, goerli>.sol`
      * [ ] ensure deployer addresses are included into `addresses_deployers.sol` (**to keep up to date**)
      * [ ] `config.sol` chainlog version bump
