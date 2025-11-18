# Node.js 20 Investigation - Quick Summary

## Question
Was Node.js 20 ever used in the GitHub Actions workflows, and if so, when was it removed?

## Answer
**Node.js 20 was NEVER used in this repository.**

## Important Context
This repository (`oss-serverless/serverless`) is a **community-maintained fork** of Serverless Framework v3, created as an open-source alternative after [Serverless Inc. announced Framework V4 would adopt a paid model](https://www.serverless.com/blog/serverless-framework-v4-a-new-model). The fork is maintained by [Bref](https://bref.sh) contributors.

## Timeline
- **Before September 2024**: Used Node.js 16 (primary), 14, 12, 4 *(inherited from Serverless Framework v3)*
- **2024**: Serverless V4 announcement → oss-serverless fork created
- **September 23, 2024** (Commit 4e43f1ab4): **Jumped from Node.js 16 → 22** *(oss-serverless decision)*
- **Current**: Uses Node.js 22 (primary) and 16 (compatibility)

## Key Details
- **Commit**: `4e43f1ab4579452722135efc4a910d24a9587275`
- **Date**: September 23, 2024
- **Author**: Matthieu Napoli (Bref maintainer)
- **Message**: "Update Node versions for tests"
- **What Happened**: Upgraded directly from Node.js 16 to 22, skipping 18 and 20 entirely

## Versions Skipped
- ❌ Node.js 18 - Never tested
- ❌ Node.js 20 - Never tested
- ✅ Node.js 22 - Currently tested

## Evidence
- Searched entire git history: **0 references** to Node.js 20
- Analyzed all workflow files across all commits (both Serverless v3 and oss-serverless fork)
- Checked documentation, configs, and commit messages
- No mention of Node.js 20 anywhere

## Why Skip?
The **oss-serverless fork maintainers** chose to skip Node.js 20 to:
1. Adopt the latest LTS (22) immediately for modernization
2. Reduce CI/CD overhead (fewer versions to test) - important for community-maintained projects
3. Maintain adequate coverage with Node.js 16 and 22
4. Simplify the test matrix while focusing on stability
5. Support the practical user base (those on 16 or upgrading to 22)

## Package.json
```json
"engines": {
  "node": ">=12.0"
}
```
The code *should* work on Node.js 20, but it was never tested in CI.

---

📄 **For full details, see: [NODEJS_20_INVESTIGATION.md](./NODEJS_20_INVESTIGATION.md)**
