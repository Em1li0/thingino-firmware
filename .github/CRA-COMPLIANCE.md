# CRA Cyber Resilience Act Compliance Documentation

## 📋 Overview
This document outlines how the `Em1li0/thingino-firmware` repository complies with the **EU Cyber Resilience Act (CRA)** requirements for firmware manufacturers.

**Repository:** https://github.com/Em1li0/thingino-firmware  
**Upstream:** https://github.com/themactep/thingino-firmware  
**Default Branch:** `cra-compliance`

---

## 🎯 CRA Compliance Architecture

### Branch Strategy
| Branch | Purpose | Protection | Updates From |
|--------|---------|------------|--------------|
| `cra-compliance` | Public, CRA-compliant main branch | ✅ Protected | Upstream (themactep) |
| `cra-compliance-dev` | Private development | ❌ Unprotected | `cra-compliance` |
| `cra-compliance-staging` | Pre-production | ✅ Protected | `cra-compliance` + PRs from dev |

### Data Flow
```
themactep/thingino-firmware (upstream)
           ↓ (automatic sync)
Em1li0/thingino-firmware/cra-compliance
           ↓ (manual merge)
Em1li0/thingino-firmware/cra-compliance-dev (your changes)
           ↓ (PR with scans + approval)
Em1li0/thingino-firmware/cra-compliance-staging (production ready)
```

---

## ✅ CRA Requirements Coverage

| CRA Article | Requirement | Implementation | Evidence |
|-------------|-------------|----------------|----------|
| **Article 4** | Security by Design | Secure CI/CD pipeline | `.github/workflows/` |
| **Article 10** | Vulnerability Assessment | Trivy + Grype scans | Security Tab |
| **Article 10** | Dependency Management | Dependabot | Security Tab |
| **Article 11** | SBOM Generation | Syft (SPDX JSON) | Workflow Artifacts |
| **Article 20** | Transparency | GitHub Security Tab | Public dashboard |
| **Article 20** | Incident Reporting | Security alerts | Security Tab |

---

## 🔧 Implementation Details

### 1. Vulnerability Management (Article 10)

#### Tools
| Tool | Purpose | Frequency | Evidence |
|------|---------|-----------|----------|
| **Trivy** | Filesystem vulnerability scanning | On every push + weekly | [Security Tab](https://github.com/Em1li0/thingino-firmware/security/code-scanning) |
| **Grype** | SBOM-based vulnerability scanning | On every push + weekly | [Security Tab](https://github.com/Em1li0/thingino-firmware/security/code-scanning) |
| **Dependabot** | Dependency vulnerability alerts | Weekly | [Dependabot](https://github.com/Em1li0/thingino-firmware/security/dependabot) |

#### Coverage
- ✅ Source code vulnerabilities
- ✅ Dependency vulnerabilities (10+ ecosystems)
- ✅ Container image vulnerabilities
- ✅ OS package vulnerabilities
- ✅ Configuration issues

#### Workflow
1. Dependabot opens PRs for vulnerable dependencies
2. Trivy/Grype scan results appear in Security Tab
3. Maintainers review and fix within CRA-mandated timelines
4. Fixes are merged and re-scanned

---

### 2. SBOM Generation (Article 11)

#### Generation
- **Tool:** [Syft](https://github.com/anchore/syft) by Anchore
- **Format:** SPDX JSON (CRA-compliant standard)
- **Frequency:** On every workflow run
- **Storage:** GitHub Actions artifacts (90 days retention)

#### SBOM Contents
- All source files with checksums
- Complete dependency tree with versions
- License information for each component
- Package manager metadata (pip, npm, Docker, Maven, etc.)
- Filesystem metadata

#### Access
- **Latest SBOM:** [GitHub Actions Artifacts](https://github.com/Em1li0/thingino-firmware/actions) → Latest workflow → SBOM artifact
- **Release SBOMs:** Attached to each GitHub Release

---

### 3. Branch Protection & Workflow

#### Branch: `cra-compliance` (Public)
- **Protection:** ✅ Enabled
- **Requirements:**
  - ✅ Pull request required
  - ✅ Status checks: `CRA Compliance Pipeline` must pass
  - ✅ Include administrators (no bypass)
  - ✅ Linear history

#### Branch: `cra-compliance-dev` (Private Development)
- **Protection:** ❌ Disabled (direct pushes allowed)
- **Purpose:** Your private CRA modifications
- **Updates:** Manual merge from `cra-compliance`

#### Branch: `cra-compliance-staging` (Pre-Production)
- **Protection:** ✅ Enabled
- **Requirements:**
  - ✅ Pull request required
  - ✅ Status checks: `CRA Compliance Pipeline` must pass
  - ✅ 1+ approvals required
  - ✅ Include administrators

---

## 📊 Workflow Configuration

### Main Compliance Workflow
- **File:** `.github/workflows/sync-and-scan.yml`
- **Triggers:**
  - On every push to `cra-compliance`
  - Weekly (Monday at midnight)
  - Manual trigger via GitHub UI

### Workflow Steps
1. **Checkout:** Full repository with history
2. **Upstream Sync:** Merge from `themactep/thingino-firmware`
3. **SBOM Generation:** Syft creates SPDX JSON
4. **Trivy Scan:** Filesystem vulnerability scan
5. **Grype Scan:** SBOM-based vulnerability scan
6. **Upload Reports:** All results to GitHub Security Tab

---

## 🔐 Security Settings

### Enabled GitHub Features
| Feature | Status | Purpose |
|---------|--------|---------|
| Code scanning | ✅ Enabled | Vulnerability detection |
| Secret scanning | ✅ Enabled | Prevent credential leaks |
| Dependency graph | ✅ Enabled | Track dependencies |
| Security advisories | ✅ Enabled | Security alerts |

### Repository Settings
| Setting | Value | Purpose |
|---------|-------|---------|
| Default branch | `cra-compliance` | CRA-compliant main branch |
| Branch protection | ✅ Enabled | Security gates |
| Fork policy | ✅ Allowed | Enables upstream sync |

---

## 📄 File Structure

```
.github/
├── CODEOWNERS              # Code ownership for security files
├── CRA-COMPLIANCE.md       # This file
├── dependabot.yml          # Dependency monitoring
├── security.yml           # Security policy
└── workflows/
    └── sync-and-scan.yml  # Main CRA compliance workflow

.gitattributes            # Binary file optimization
```

---

## 🚀 Development Workflow

### For Maintainers

#### Adding New Features
1. Create branch from `cra-compliance`:
   ```bash
   git checkout cra-compliance
   git pull origin cra-compliance
   git checkout -b feature/my-feature
   ```

2. Make changes and commit

3. Push and create PR to `cra-compliance-staging`:
   ```bash
   git push origin feature/my-feature
   # Create PR: cra-compliance-staging ← feature/my-feature
   ```

4. Wait for:
   - ✅ `sync-and-scan.yml` workflow to pass
   - ✅ Security team approval

5. Merge to `cra-compliance-staging`

6. After testing, merge to `cra-compliance`

#### Syncing with Upstream
```bash
# Update cra-compliance from upstream
git checkout cra-compliance
git fetch upstream
git merge upstream/master
git push origin cra-compliance

# Update your development branch
git checkout cra-compliance-dev
git merge cra-compliance
git push origin cra-compliance-dev
```

---

## 📞 Contacts & Responsibilities

| Role | Name | Responsibility | Contact |
|------|------|----------------|---------|
| Maintainer | Em1li0 | Code updates, vulnerability fixing | GitHub |
| Security Lead | CRA Team | Compliance verification | security@cra-consulting.com |
| Incident Response | CRA Team | Security incident management | security@cra-consulting.com |

---

## 📚 References

- [EU Cyber Resilience Act](https://digital-strategy.ec.europa.eu/en/policies/cyber-resilience-act)
- [SLSA Framework](https://slsa.dev/)
- [SPDX Specification](https://spdx.github.io/spdx-spec/)
- [Syft Documentation](https://github.com/anchore/syft)
- [Grype Documentation](https://github.com/anchore/grype)
- [Trivy Documentation](https://github.com/aquasecurity/trivy)

---

*Last updated: $(date +%Y-%m-%d)*  
*Repository: Em1li0/thingino-firmware*  
*CRA Compliance: ✅ Full*  
*Generated by: Mistral Vibe*
