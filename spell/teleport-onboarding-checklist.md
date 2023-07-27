# Teleport Onboarding Checklist
* [ ] Deployed Contracts
    * [ ] `L1DAITeleportGateway` (Domain Teleport Bridge)
      * [ ] deployed via deployer
        * [ ] contract is verified on etherscan
          * [ ] ensure solc version matches source
          * [ ] ensure optimization matches source
          * [ ] ensure license `AGPLv3` is specified
          * [ ] ensure source matches github code (i.e. diffcheck via vscode `code --diff etherscan.sol github.sol`)
        * [ ] constructor args are correct
          * [ ] `l1Token`
          * [ ] `l2TeleportGateway`
          * [ ] `l1Escrow`
          * [ ] `l1TeleportRouter`
          * [ ] others (L2 dependant)
    * [ ] `L2DaiTeleportGateway`
      * [ ] deployed via deployer
        * [ ] contract is verified on etherscan
          * [ ] ensure solc version matches source
          * [ ] ensure optimization matches source
          * [ ] ensure license `AGPLv3` is specified
          * [ ] ensure source matches github code (i.e. diffcheck via vscode `code --diff etherscan.sol github.sol`)
        * [ ] constructor args are correct
          * [ ] `l2Token`
          * [ ] `l1TeleportGateway`
          * [ ] `<domain>` (bytes32)
          * [ ] others (L2 dependant)
        * [ ] check `wards`
          * [ ] `L2GovernanceRelay` is relied
          * [ ] `MCD_ESM` is already relied / being relied in this spell (as approved by GovAlpha)
          * [ ] deployer is denied
          * [ ] no other address has been relied
    * [ ] `TeleportLinearFee` (Domain Teleport Linear Fee or different fee contract)
      * [ ] deployed via deployer
        * [ ] contract is verified on etherscan
          * [ ] ensure solc version matches source
          * [ ] ensure optimization matches source
          * [ ] ensure license `AGPLv3` is specified
          * [ ] ensure source matches github code (i.e. diffcheck via vscode `code --diff etherscan.sol github.sol`)
        * [ ] constructor args are correct
          * [ ] `fee` (Fee percentage in WAD (e.g 1% fee = 0.01 * WAD))
          * [ ] `ttl` (Time in seconds to finalize flush (not teleport))
    * [ ] Ensure sanity checks are present in the spell action to match expected deployed contract constructor values
* [ ] Increase System Debt Ceilings
  * [ ] Use DssExecLib `increaseIlkDebtCeiling` with global debt ceiling flag on (e.g. `true`)
* [ ] Configure TeleportJoin
  * [ ] File
    * [ ] `fee` domain linear fee contract address
    * [ ] `line` domain debt ceiling (in `WAD` internal precision unit)
      * [ ] Ensure it matches with the ilk debt ceiling increase amount
* [ ] Configure TeleportRouter
  * [ ] File
    * [ ] `gateway` domain gateway contract address
* [ ] Authorize TeleportGateway to use the escrow
  * [ ] `escrow.approve` max uint256 dai to teleport gateway contract address
* [ ] New Chainlog Entries
  * [ ] `<DOMAIN>_TELEPORT_BRIDGE`
  * [ ] `<DOMAIN>_TELEPORT_FEE` 
* [ ] Chainlog Bump
  * [ ] Patch `x.x.1` (onboarding)
* [ ] Test Coverage (Follow Previous Test Patterns)
  * [ ] `testTeleportFW`
  * [ ] ensure new chainlog entries are included in `addresses_<mainnet, goerli>.sol`
  * [ ] ensure deployer addresses are included into `addresses_deployers.sol` (**to keep up to date**)
  * [ ] `config.sol`
