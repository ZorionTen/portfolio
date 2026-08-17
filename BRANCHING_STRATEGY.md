# Branching Strategy

## Overview
```
develop → (PR + review) → staging → (manual promote) → main → (auto-deploy)
```

## Branch Purposes

| Branch | Purpose | Deploys To | Protection |
|--------|---------|------------|------------|
| `develop` | Integration branch, ongoing work | None (local/test only) | No force push, require PR |
| `staging` | Pre-production validation | Staging environment (future) | Require PR from develop, require review |
| `main` | Production-ready code | Production (Render, GitHub Pages) | Require PR from staging, require review, status checks |

## Workflow

### 1. Feature Development
```bash
git checkout develop
git pull origin develop
git checkout -b feature/xyz
# work, commit
git push origin feature/xyz
# Create PR: feature/xyz → develop
# Review → Merge
```

### 2. Staging Release
```bash
# When develop has features ready for testing
git checkout develop
git pull origin develop
git checkout -b release/vX.Y.Z
# Version bump, changelog
git push origin release/vX.Y.Z
# Create PR: release/vX.Y.Z → staging
# Review → Merge to staging
# Deploy staging (manual) → Test
```

### 3. Production Release
```bash
# After staging validated
git checkout staging
git pull origin staging
git checkout -b release/vX.Y.Z-prod
# Final checks
git push origin release/vX.Y.Z-prod
# Create PR: release/vX.Y.Z-prod → main
# Review → Merge to main
# Auto-deploys to production
```

## Repository-Specific Notes

### portfolio-backend
- **main** → CI → Native Image → GHCR → Render (via deploy hook)
- **develop/staging** → CI only (tests, no deploy)

### portfolio-ai
- **main** → Render auto-deploy (Docker build)
- **develop/staging** → No auto-deploy (configure if needed)

### portfolio-frontend
- **main** → GitHub Pages deploy (`.github/workflows/deploy-pages.yml`)
- **develop/staging** → No deploy

### portfolio (monorepo root)
- Coordinates submodules
- No direct deploy

## GitHub Branch Protection Rules (Recommended)

### develop
- Require PR reviews: 1
- Require status checks: CI
- No force pushes

### staging
- Require PR reviews: 1
- Require status checks: CI
- Require linear history
- No force pushes

### main
- Require PR reviews: 1
- Require status checks: CI, native-image (backend)
- Require linear history
- No force pushes
- No deletions

## Emergency Hotfix
```bash
git checkout main
git pull origin main
git checkout -b hotfix/critical-fix
# Fix, commit
git push origin hotfix/critical-fix
# PR: hotfix/critical-fix → main (expedited review)
# After merge: cherry-pick to staging, develop
```

## Version Tagging
```bash
# On main after release merge
git tag -a vX.Y.Z -m "Release vX.Y.Z"
git push origin vX.Y.Z
```

## Submodule Updates
```bash
# In portfolio/ root
git submodule update --remote --merge
# Review changes, commit
git commit -am "chore: update submodules"
git push origin develop
```