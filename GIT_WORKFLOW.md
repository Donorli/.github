# Source Control Policy & Procedures
## DonorEze Engineering Team

**Version:** 1.0

**Effective Date:** June 2026

**Owner:** Wm. Dexter Jones, fCTO

---

## 1. Why Source Control Matters

Source control is how a development team manages changes to code over time. Think of it like Google Docs version history — but purpose-built for software, with rules about who can change what and when. Without it, developers overwrite each other's work, bugs get introduced silently, and there is no safe way to "roll back" a bad change.

As DonorEze scales from Alpha to a fundable Beta, establishing a disciplined source control practice now will:

- Protect the production product from unstable code changes
- Give QA and testers a stable, predictable environment to test against
- Create a clear audit trail for every code change (critical for investor and compliance readiness)
- Onboard offshore developers safely with well-defined guardrails

---

## 2. Our Approach: Simplified Git Flow

We use **Git** as our source control system and follow a **Simplified Git Flow** model. Classic Git Flow was designed for large teams shipping boxed software releases. We have adapted it to fit a fast-moving startup without sacrificing structure or safety.

### 2.1 The Branch Hierarchy

We maintain four types of branches. Think of them as a "ladder" — code can only move *up* the ladder, never skip a rung, and the top rung (`main`) is always production-ready.

```
main        ← PRODUCTION. Always live, always stable.
  ↑
develop     ← INTEGRATION. What QA tests against.
  ↑
feature/*   ← DEVELOPMENT. Where all new work happens.

hotfix/*    ← EMERGENCY ONLY. Patches applied directly to main.
```

### 2.2 Branch Descriptions

| Branch | Purpose | Who Works Here | Protected? |
|---|---|---|---|
| `main` | Live production code | No one directly | ✅ Yes — fCTO approval required |
| `develop` | Integration & QA testing environment | PM/QA team tests here | ✅ Yes — no direct commits |
| `feature/*` | Active development of a single feature or fix | Caston, Dawid, offshore devs | No |
| `hotfix/*` | Emergency production patch only | Caston or offshore dev, with fCTO awareness | No |

---

## 3. The Development Lifecycle (Step by Step)

### Step 1 — Create a Feature Branch

Before writing any code, the developer creates a new branch off of `develop`.

**Naming convention:**
```
feature/[ticket-id]-short-description
```

**Examples:**
```
feature/DON-42-donor-profile-page
feature/DON-87-fix-email-validation
feature/DON-101-onboarding-flow-redesign
```

> **Rule:** One feature branch = one task or ticket. Do not combine unrelated changes on the same branch.

---

### Step 2 — Develop & Commit Regularly

The developer works on the feature branch, committing changes frequently with clear commit messages.

**Good commit message format:**
```
[DON-42] Add donor profile page layout
[DON-42] Connect donor profile to API endpoint
[DON-42] Fix avatar upload validation
```

**Rules:**
- Commit at logical stopping points — not "dump everything at end of day"
- Never commit broken code that prevents the app from running
- Never commit directly to `develop` or `main`

---

### Step 3 — Open a Pull Request (PR)

When the feature is ready for review, the developer opens a **Pull Request** (PR) — a formal request to merge their branch into `develop`.

**PR checklist (developer's responsibility):**
- [ ] Self-reviewed the code diff before submitting
- [ ] Feature works locally as expected
- [ ] No console errors or obvious bugs
- [ ] UI changes include a screenshot or short video in the PR description
- [ ] PR is linked to the relevant ticket in the project management tool

**PR title format:**
```
[DON-42] Donor profile page
```

---

### Step 4 — Code Review

Every PR must be reviewed by **at least one other person** before merging. This is not bureaucracy — it is how we catch bugs, share knowledge, and maintain quality.

| Reviewer | Reviews What |
|---|---|
| Caston | Logic, backend correctness, database changes |
| Dawid | UI/UX implementation, accessibility, visual accuracy |
| fCTO (Dexter) | Architecture decisions, security-sensitive changes, offshore PRs |
| Walt (Tech Advisor) | Consulted on major architectural PRs as needed |

**Review etiquette:**
- Reviews should be completed within **1 business day**
- Comments should be specific and constructive — explain *why*, not just *what*
- Approve only when you would be comfortable with this code in production
- The PR author resolves feedback; the reviewer confirms resolution before final approval

---

### Step 5 — Merge to `develop`

Once approved, the developer (or reviewer) merges the PR into `develop`. This is now available for QA testing.

- Use **"Squash and merge"** for small features (keeps history clean)
- Use **"Merge commit"** for large features (preserves the full commit history)
- Delete the feature branch after merging

---

### Step 6 — QA Testing on `develop`

The QA team (manual and automated testers) tests the feature on `develop`. This is the only environment they test against.

**QA process:**
1. PM/QA Manager assigns test cases linked to the ticket
2. Manual testers execute test cases and log defects
3. Automated test suite runs against `develop`
4. If a defect is found: developer creates a **new branch** off `develop` to fix it — do not reopen the original feature branch

---

### Step 7 — Merge to `main` (Release to Production)

When QA signs off, the PM/QA Manager notifies the fCTO. The fCTO (or designated lead) merges `develop` into `main`.

**Pre-merge checklist:**
- [ ] All test cases passed
- [ ] No open critical or high-severity defects
- [ ] fCTO has reviewed and approved
- [ ] Deployment notes documented (what changed, any config changes needed)

> **Rule:** Only the fCTO or explicitly delegated lead may approve merges to `main`.

---

## 4. Hotfix Procedure (Emergency Patches)

A hotfix is used **only** when a critical bug is discovered in production that cannot wait for the normal development cycle.

```
main
 ↓ (branch off)
hotfix/DON-XXX-critical-bug-description
 ↓ (fix committed)
 → merge to main  (fCTO approval required)
 → merge to develop  (keep branches in sync)
```

**Steps:**
1. Developer branches off `main` using the naming convention `hotfix/[ticket]-description`
2. Fix is made and tested locally
3. PR opened targeting `main` — tagged as **HOTFIX** in the title
4. fCTO reviews and approves
5. After merging to `main`, the same fix is **immediately** merged into `develop` to prevent regression

> Hotfixes bypass the normal QA cycle — the fCTO accepts this risk consciously. Use sparingly.

---

## 5. Offshore Developer Guidelines

When the offshore team joins, they follow the same branching model with these additional expectations:

**Onboarding requirements:**
- Complete a Git Flow orientation session before writing any production code
- First 2–3 PRs require review by both Caston and the fCTO, regardless of size
- All work must be ticketed before branching — no untracked code changes

**Communication standards:**
- PR descriptions must be written in English
- Any blocker or question must be posted in the team communication channel within 4 hours of being stuck
- End-of-day status update posted in the project channel: what was completed, what is in progress, any blockers

**Access controls:**
- Offshore developers have write access to feature branches only
- They cannot merge their own PRs — a core team member must approve and merge
- No direct access to `main` or production infrastructure

---

## 6. Branch Protection Rules (Technical Configuration)

The following rules must be configured in GitHub (or equivalent) by the fCTO or tech lead:

**For `main`:**
- Require pull request reviews before merging: **1 approval minimum**
- Require approval from fCTO or designated lead
- No force pushes
- No direct commits

**For `develop`:**
- Require pull request reviews before merging: **1 approval minimum**
- Automated test suite must pass before merge is allowed
- No direct commits

---

## 7. Roles & Responsibilities Summary

| Role | Git Responsibilities |
|---|---|
| **fCTO (Dexter)** | Approves merges to `main`; reviews security/architecture PRs; owns this policy; onboards offshore team |
| **Lead Developer (Caston)** | Day-to-day code review; maintains `develop` health; mentors offshore devs |
| **UI/UX Developer (Dawid)** | Reviews UI PRs; maintains frontend branch hygiene |
| **Tech Advisor (Walt)** | Consulted on major architectural decisions; not in day-to-day flow |
| **PM / QA Manager** | Manages test cycles on `develop`; coordinates release readiness; notifies fCTO when QA passes |
| **Testers (4)** | Execute test cases against `develop`; log defects with reproduction steps |
| **Offshore Developers** | Work only on assigned feature branches; PRs reviewed by core team before merge |

---

## 8. Quick Reference Card

```
Starting new work:
  git checkout develop
  git pull
  git checkout -b feature/DON-XX-my-feature

Committing:
  git add .
  git commit -m "[DON-XX] Clear description of what changed"
  git push origin feature/DON-XX-my-feature

Finishing work:
  → Open Pull Request on GitHub targeting 'develop'
  → Assign a reviewer
  → Address feedback
  → Reviewer approves and merges
  → Delete the feature branch
```

---

## 9. Policy Governance

This policy is owned by the fCTO and reviewed quarterly or when the team composition changes significantly. Exceptions require fCTO sign-off and must be documented.

Questions? Reach out to Dexter Jones or raise in the #engineering channel.

---

*DonorEze Engineering — Source Control Policy v1.0 — June 2026*
