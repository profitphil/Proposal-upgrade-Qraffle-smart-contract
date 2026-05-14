# Proposal: Upgrade Qraffle Smart Contract

Upgrade the existing qraffle Smart Contract on Qubic to the latest reviewed implementation.

## Available Options

- **Option 0:** No, do not approve the qraffle upgrade.
- **Option 1:** Yes, approve the qraffle upgrade.

## Context

qraffle already exists on-chain. This proposal is an upgrade proposal, not an initial deployment proposal.

## Summary of Changes

### Security

- Fixed vote-weight inflation bug where stale vote buffers could match across proposals
- Token raffle deposits now restricted to registered DAO members only (prevents DoS)
- QRAFFLE and QXMR tokens blocked from being used as raffle prizes (reserved for internal use)
- Raffle winner randomness hardened — seed now uses `XOR(prevSpectrumDigest, prevComputerDigest)` hashed via K12, making it impossible for computors to grind/manipulate the outcome

### Fee Accounting

- Token raffle deposit fees now correctly credited to epoch revenue
- `TransferShareManagementRights` now properly handles fee refunds and credits service fees on success
- Received fees via `POST_ACQUIRE_SHARES` are now tracked

### Bug Fixes

- QXMR distribution capped to actual contract balance; only deducts what was actually transferred
- Token raffle skips entirely if winner transfer fails (no partial distribution)
- DAO member count is always recorded, even in epochs with no raffle participants
- Hard-coded `676` replaced with `NUMBER_OF_COMPUTORS` constant

### Performance

- Duplicate checks for QuRaffle and token raffle deposits upgraded from O(N) to O(1) using HashSets and BitArrays
- Vote lookup upgraded from O(N) to O(1)
- State size reduction: **616 MB → 16 MB**

### Tests

- All existing tests fixed and new edge cases added (unregistered user, duplicate deposit, failed QXMR distribution, fee forwarding)

## Technical Implementation

**Developer:** [double-k-3033](https://github.com/double-k-3033)
Core implementation PR: [https://github.com/qubic/core/pull/853/changes](https://github.com/qubic/core/pull/853/changes)
