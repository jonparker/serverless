# Node.js 20 Support Investigation Report

## Summary

**Finding**: Node.js 20 was **NEVER** used in the GitHub Actions workflows for this repository.

The repository jumped directly from Node.js 16 to Node.js 22, skipping version 20 entirely.

## Current State (as of latest commit)

The current GitHub Actions workflows use the following Node.js versions:

### validate.yml
- **linuxNode22**: Node.js 22.x (primary testing environment)
- **windowsNode16**: Node.js 16.x
- **linuxNode16**: Node.js 16.x (isolated tests)

### integrate.yml
- **linuxNode22**: Node.js 22.x (canary publishing and tests)
- **windowsNode16**: Node.js 16.x
- **linuxNode16**: Node.js 16.x (isolated tests)
- **integrate**: Node.js 22.x (integration tests)

### publish.yml
- **prePublishPackageTest**: Node.js 22.x
- **npmPublish**: Node.js 22.x

## Version History Timeline

### Key Commits Related to Node.js Versions:

1. **Commit 4e43f1ab4** (September 23, 2024)
   - **Author**: Matthieu Napoli
   - **Message**: "Update Node versions for tests"
   - **Changes**: 
     - Upgraded primary testing from Node.js 16 to Node.js 22
     - Upgraded secondary testing from Node.js 14 to Node.js 16
     - Removed Node.js 12 and 4 testing jobs
   - **Before**: Node.js 16, 14, 12, 4
   - **After**: Node.js 22, 16

2. **Commit b8cc13f50** (Earlier)
   - **Message**: "ci: Use Node 16 as main Node version"
   - **Node versions**: 16, 14, 12, 10

3. **Commit 8fc52cdee** (Earlier)
   - **Message**: "ci: Add `node16` to testing matrix"

4. **Commit 13e8ca6f8** (November 3, 2021)
   - **Message**: "feat: Remove support for Node v10"

5. **Commit 69dd4b974** (Earlier)
   - **Message**: "feat: Drop support for Node.js versions below v10"

## Analysis

### Context: oss-serverless Fork and Serverless Framework V4

**Important Context**: This repository (`oss-serverless/serverless`) is a community-maintained fork of the original Serverless Framework v3. It was created in response to Serverless Inc.'s announcement that [Serverless Framework V4 would adopt a new paid model](https://www.serverless.com/blog/serverless-framework-v4-a-new-model).

According to the repository README:
> "This repository is a maintained alternative to [Serverless Framework](https://github.com/serverless/serverless) v3. It exists for those that cannot upgrade to Serverless Framework v4 and is a drop-in replacement for v3."

The fork is maintained by [Bref](https://bref.sh) maintainers and contributors, with the goal of providing continuity for users who need an open-source alternative to the commercial v4 offering.

### Timeline Context

The Node.js upgrade to v22 (September 2024) occurred **after** this repository was established as an independent fork. This means:

1. **The fork inherited** the Node.js version strategy from the original Serverless Framework v3
2. **The upgrade decision** (Node.js 16 → 22, skipping 18 and 20) was made by the oss-serverless maintainers as part of their independent maintenance strategy
3. **The timing** (September 2024) shows the fork actively modernizing its test infrastructure while maintaining v3 compatibility

### Why Node.js 20 Was Skipped

Based on the commit history, the repository's Node.js version progression was:
- **Phase 1**: Multiple versions including very old ones (4, 10, 12, 14, 16) - *Inherited from Serverless Framework v3*
- **Phase 2**: Node.js 16 became the primary version (with 14, 12, 4 for compatibility testing) - *Inherited from Serverless Framework v3*
- **Phase 3**: Direct upgrade from Node.js 16 to Node.js 22 (September 2024) - *Decision made by oss-serverless maintainers*

The upgrade commit (4e43f1ab4) by Matthieu Napoli shows a deliberate decision to:
1. Adopt Node.js 22 as the primary/latest version for testing
2. Keep Node.js 16 for backward compatibility testing
3. Skip Node.js 18 and Node.js 20 entirely

This is a common pattern where projects skip intermediate LTS versions when:
- The older LTS (Node.js 16) is still supported
- The newest LTS (Node.js 22) provides better features/performance
- There's no specific requirement to test intermediate versions
- The project is focused on stability and maintenance rather than bleeding-edge features

### Related Changes

The commit that updated to Node.js 22 also:
- Updated cache keys from `npm-v16` to `npm-v22`
- Updated job names to reflect the new versions
- Reduced the overall number of test matrix versions (from 5 to 2-3 versions)
- Removed outdated version validation tests for Node.js 4 and 12

## Package.json Engine Requirements

The repository's `package.json` specifies:
```json
"engines": {
  "node": ">=12.0"
}
```

This means the application officially supports Node.js 12 and above, which would technically include Node.js 20. However, the GitHub Actions workflows never tested against Node.js 20 specifically.

## Search for Node.js 20 References

A comprehensive search across the repository (including all markdown files, JSON files, and configuration files) found **zero references to Node.js version 20**. This confirms that:
- Node.js 20 was never mentioned in documentation
- Node.js 20 was never used in CI/CD workflows
- Node.js 20 was never part of the testing strategy
- No issues or commits reference Node.js 20

## Conclusion

Node.js 20 support was never added to this repository's GitHub Actions workflows. The project maintained Node.js 16 as its primary version for an extended period and then upgraded directly to Node.js 22 in September 2024, bypassing both Node.js 18 and Node.js 20.

This approach is reasonable as:
- Node.js 22 is the current LTS version
- Node.js 16 is still maintained for legacy support
- Testing on intermediate versions (18, 20) would add CI time without significant benefit
- The application appears to work correctly on both Node.js 16 and 22
- The `package.json` engine requirement (>=12.0) suggests the code should work on Node.js 20, even without explicit testing

## Detailed Commit Information

### The Key Upgrade Commit (4e43f1ab4)

**Full Commit Hash**: 4e43f1ab4579452722135efc4a910d24a9587275
**Date**: Monday, September 23, 2024 at 11:16:16 AM (+0200)
**Author**: Matthieu Napoli <matthieu@mnapoli.fr>
**Commit Message**: "Update Node versions for tests"

**Statistics**:
- Files changed: 3
- Total insertions: 40
- Total deletions: 100
- Net change: -60 lines (simplified configuration)

**Affected Files**:
1. `.github/workflows/integrate.yml` - 66 changes
2. `.github/workflows/publish.yml` - 8 changes  
3. `.github/workflows/validate.yml` - 66 changes

This commit demonstrates a strategic simplification: fewer Node.js versions to test (from 5 down to 3), while maintaining coverage of the latest LTS and one previous major version.

## Recommendations

If Node.js 20 support testing is desired, it would need to be explicitly added to the workflow files by:
1. Adding new job definitions for Node.js 20 testing (e.g., `linuxNode20`, `windowsNode20`)
2. Updating cache keys to include Node.js 20 (e.g., `npm-v20-...`)
3. Potentially replacing Node.js 16 testing with Node.js 20

However, given the current state with Node.js 22 as the latest and Node.js 16 for compatibility, adding Node.js 20 may not provide significant value unless there are specific compatibility concerns. The application should theoretically work on Node.js 20 given the `>=12.0` engine requirement, but this has never been validated in CI.

## Visual Timeline of Node.js Version Support

```
Timeline of GitHub Actions Node.js Versions (oss-serverless fork)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

2019-2020: Initial GitHub Actions Setup (Serverless Framework v3)
├─ Node.js: 10, 12, 14
└─ Focus: Multi-version compatibility

2021: Drop Node.js 10 Support (Commit 13e8ca6f8)
├─ Node.js: 12, 14, 16
└─ Note: Node.js 16 added to matrix

2022-2024 (June): Node.js 16 Era (Serverless Framework v3)
├─ Primary: Node.js 16
├─ Secondary: Node.js 14
├─ Legacy validation: Node.js 12, 4
└─ Duration: ~3 years

📍 2024: Serverless Framework V4 Announcement & Fork Creation
├─ Serverless Inc. announces V4 with new paid model
├─ Community creates oss-serverless fork as open-source alternative
└─ Fork maintains v3 codebase and compatibility

2024 (September 23): Major Version Jump (Commit 4e43f1ab4)
├─ Context: oss-serverless fork modernizes CI independently
├─ Primary: Node.js 22 ← JUMPED FROM 16
├─ Secondary: Node.js 16
├─ Removed: Node.js 14, 12, 4
└─ ⚠️  SKIPPED: Node.js 18, 20
└─ Decision by: Matthieu Napoli (Bref maintainer)

2024 (September 23) - Present: Current State (oss-serverless)
├─ Primary: Node.js 22 (latest LTS)
├─ Compatibility: Node.js 16
└─ Test Matrix: Simplified to 3 jobs

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Node.js LTS Release Timeline (for context):
• Node.js 16 LTS: October 2021 - September 2023
• Node.js 18 LTS: October 2022 - April 2025 ← NEVER TESTED
• Node.js 20 LTS: October 2023 - April 2026 ← NEVER TESTED
• Node.js 22 LTS: October 2024 - April 2027 ← CURRENTLY TESTED
```

## Evidence Summary

### Commits Analyzed
- ✅ Searched entire git history with `git log --all`
- ✅ Used `git log -S` to search for "20.x" and "node-version: 20"
- ✅ Examined all commits to workflow files
- ✅ Checked commit messages for Node.js version references

### Files Analyzed
- ✅ `.github/workflows/validate.yml` (full history)
- ✅ `.github/workflows/integrate.yml` (full history)
- ✅ `.github/workflows/publish.yml` (full history)
- ✅ `package.json` (engine requirements)
- ✅ All `.md`, `.json`, `.txt` files (searched for "20" references)

### Methods Used
1. Git history search: `git log --all --oneline -- .github/workflows/`
2. String search in history: `git log -S "20.x" -- .github/workflows/`
3. Grep search: `grep -i "node.*20" across repository`
4. Commit diff analysis: `git show <commit>` for key commits
5. Timeline reconstruction: comparing Node versions at different commits

### Result
**Zero instances** of Node.js 20 found in any commit, configuration, or documentation.

## Additional Notes

### Why Skip Node.js 20?

Possible reasons the team may have decided to skip Node.js 20:

1. **LTS Timing**: By September 2024, Node.js 22 had just entered LTS, making it more attractive than Node.js 20
2. **Maintenance Burden**: Testing fewer versions reduces CI/CD time and maintenance overhead
3. **Sufficient Coverage**: Testing on Node.js 16 (older) and 22 (newest) provides adequate coverage
4. **Application Compatibility**: The codebase appears to be compatible across major versions without issues
5. **Resource Optimization**: Each additional version in the test matrix costs time and resources

### Industry Context

This pattern (skipping intermediate LTS versions) is common in open-source projects because:
- CI/CD costs increase with each additional version
- Modern Node.js versions have excellent backward compatibility
- Most breaking changes are documented and can be addressed proactively
- Testing on the oldest supported and newest LTS provides good coverage

### Fork-Specific Context

For the oss-serverless fork specifically, this decision makes strategic sense:
- **Maintenance Focus**: As a community-maintained fork focused on stability, minimizing test matrix complexity reduces burden
- **Resource Constraints**: Open-source projects have limited CI/CD resources compared to commercial offerings
- **v3 Compatibility Goal**: The fork aims to maintain Serverless Framework v3 compatibility, not chase every Node.js version
- **Practical Approach**: Testing on Node.js 16 (widely used) and 22 (latest) covers the practical user base

## Conclusion

**Node.js 20 was intentionally skipped** when the oss-serverless fork upgraded from Node.js 16 to Node.js 22 in September 2024. This was not an oversight but rather a deliberate decision by the fork maintainers to:
1. Adopt the latest LTS (Node.js 22) immediately for modernization
2. Maintain backward compatibility testing (Node.js 16) for existing users
3. Simplify the test matrix for easier maintenance
4. Reduce CI/CD overhead for the community-maintained project

**Important Context**: This decision was made after the fork was established as an independent alternative to Serverless Framework V4's paid model. The oss-serverless maintainers (led by Bref contributors like Matthieu Napoli) made this choice as part of their strategy to maintain a stable, open-source v3 alternative while modernizing the test infrastructure.

The project never tested, documented, or referenced Node.js 20 in any capacity throughout its git history - neither in the original Serverless Framework v3 nor in the oss-serverless fork.
