# Node.js 20 Investigation - Quick Summary

## Question
Was Node.js 20 ever used in the GitHub Actions workflows, and if so, when was it removed?

## Answer
**Node.js 20 was NEVER used in this repository.**

## Timeline
- **Before September 2024**: Used Node.js 16 (primary), 14, 12, 4
- **September 23, 2024** (Commit 4e43f1ab4): **Jumped from Node.js 16 → 22**
- **Current**: Uses Node.js 22 (primary) and 16 (compatibility)

## Key Details
- **Commit**: `4e43f1ab4579452722135efc4a910d24a9587275`
- **Date**: September 23, 2024
- **Author**: Matthieu Napoli
- **Message**: "Update Node versions for tests"
- **What Happened**: Upgraded directly from Node.js 16 to 22, skipping 18 and 20 entirely

## Versions Skipped
- ❌ Node.js 18 - Never tested
- ❌ Node.js 20 - Never tested
- ✅ Node.js 22 - Currently tested

## Evidence
- Searched entire git history: **0 references** to Node.js 20
- Analyzed all workflow files across all commits
- Checked documentation, configs, and commit messages
- No mention of Node.js 20 anywhere

## Why Skip?
The team likely chose to skip Node.js 20 to:
1. Adopt the latest LTS (22) immediately
2. Reduce CI/CD overhead (fewer versions to test)
3. Maintain adequate coverage with Node.js 16 and 22
4. Simplify the test matrix

## Package.json
```json
"engines": {
  "node": ">=12.0"
}
```
The code *should* work on Node.js 20, but it was never tested in CI.

---

📄 **For full details, see: [NODEJS_20_INVESTIGATION.md](./NODEJS_20_INVESTIGATION.md)**
