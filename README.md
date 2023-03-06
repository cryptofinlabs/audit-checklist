# CryptoFin's Solidity Auditing Checklist
In the last year, hundreds of millions of dollars worth of crypto have been lost by faulty smart contracts.

Beyond devastating those who lose funds, these blunders harm the ecosystem as a whole by signaling the immaturity of smart contracts and reinforcing people’s wariness. Better due diligence processes could’ve prevented many of the issues found, saving a ton of headaches, money, and stifled progress.

At CryptoFin, we’ve audited many production-ready Solidity contracts. We’ve compiled a list of common findings, and issues to watch out for when auditing a contract for a mainnet launch.

We hope this checklist is useful to the community and will raise the bar for contract quality!

## Checklist
### Core Checks
These are basic checks to undertake with any contract. Our checklist reflects Solidity v0.4.24.

- [ ] Prevent overflow and underflow
  - [ ] Use [SafeMath](https://github.com/OpenZeppelin/openzeppelin-solidity/blob/master/contracts/math/SafeMath.sol)
- [ ] Function Visibility
  - [ ] Ensure that all relevant functions are marked with the correct visibility
- [ ] Fix compiler warnings
- [ ] Avoid using problematic features - If you must, be aware of their many nuances
  - [ ] send ([nuances](https://ethereum.stackexchange.com/a/38642/3118))
  - [ ] Low level functions (`call`, `delegatecall`, `callcode`, inline assembly)
  - [ ] var
- [ ] External Calls - Every external contract call is a risk
  - [ ] Check for [reentrancy](https://dasp.co/#item-1) - and ensure state committed before external call
    - [ ] Check for "short circuits" (external contract calls that can fail or be manipulated to fail, causing a denial of service of a function)
      - This is often overlooked for ERC20, which can [fail unexpectedly due to freezing](https://blog.cryptofin.io/what-we-learned-from-auditing-the-top-20-erc20-token-contracts-7526ef3b6fb1)
      - [Callstack depth](https://solidity.readthedocs.io/en/v0.4.24/security-considerations.html?highlight=callstack#callstack-depth) can cause this as well
- [ ] Dependencies
  - [ ] Use audited and trustworthy dependencies
  - [ ] Ensure newly written code is minimized by using libraries
- [ ] Time Manipulation - Timestamps can theoretically be manipulated by malicious miners by up to a few minutes
  - [ ] Ensure important mechanisms aren't overly sensitive to timestamps
- [ ] Rounding Errors
  - [ ] Check that truncation doesn't produce unexpected behavior (eg. incorrect results, locked funds)
- [ ] Randomness
  - [ ] Don't rely on pseudo-randomness for important mechanisms (eg. keccak with a deterministic seed like blockhash, blocknumber, etc.)
- [ ] Validate inputs of external/public functions
  - [ ] Ensure requires to bound and check presence of arguments
- [ ] Prevent unbounded loops
- [ ] Appropriate use of push payments
- [ ] Change old Solidity constructs
  - [ ] selfdestruct vs suicide
  - [ ] keccak256 vs sha3
- [ ] Don't use tx.origin as an authentication mechanism
- [ ] Verify changes in the most recent Solidity version (if upgrading from an older version)

### Testing and Software Engineering
- [ ] Test Coverage
  - [ ] Have 100% branch test coverage
- [ ] Unit Tests
  - [ ] Cover all critical edge cases with unit tests
- [ ] Integration Tests
  - [ ] Have extensive integration tests
- [ ] Code Freeze
  - [ ] Don't deploy recently written code, especially when written under a tight deadline

### Resilience
We always check for code that will mitigate risk when (not if) a contract fails. When a contract doesn’t have this, it’s often a warning sign.

- [ ] What failure states would be most disastrous?
- [ ] Are there assert checks for critical values? (e.g., individual balances total to sum)
- [ ] Speed Bumps
- [ ] Does the contract have a speed bump? (e.g., delay in withdrawing funds, like the DAO)
- [ ] Circuit Breakers
  - [ ] Does the contract have a circuit breaker? (preventing critical functions in an emergency mode)

### Auditing
Auditing helps catch many bugs, but shouldn’t also be seen as a magic bullet. Your system still needs to handle failure gracefully.

- [ ] Audits
  - [ ] Have code audited by (preferably) multiple external parties (in series)
- [ ] Time Management
  - [ ] Allocate comfortable time after the audit to address issues

### High Risk Areas
When performing an audit, CryptoFin pays special attention to the these areas which require greater scrutiny, as they often add bugs.

- external and public functions
- Assembly code and other low level calls
- Superuser privileges
- Any areas that are affected by timing and/or network congestion
- Areas dealing with value transfer and payable functions
- Push payments (rather than pull)
- Code written most recently

### Security Resources
- [Ethereum Security Guide](https://ethereum.org/en/security/)
- [Consensys Ethereum Smart Contract Security Best Practices](https://consensys.github.io/smart-contract-best-practices/)
- [Decentralized Application Security Project](https://dasp.co/)

### Where to go from here
Many of these checklist items are well-suited for a linter. We welcome any contributors who want to take a crack at it.
