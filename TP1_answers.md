# DevOps Lab Report - TP1 Answers

**Team Members:**
- AIDI Youcef Abdelkader (`@githubaidiyoucef`)
- HADJI Youcef Houssam (`@Hadji-Youcef`)
- ZERNOUH Lakhdar (`@nearZL17`)

**Repository URL:** https://github.com/githubaidiyoucef/devops-project

---

## Deliverables & Merged Pull Requests

| Team Member | GitHub Username | Merged & Reviewed Pull Request |
| :--- | :--- | :--- |
| **AIDI Youcef Abdelkader** | [`@githubaidiyoucef`](https://github.com/githubaidiyoucef) | [PR Link](https://github.com/githubaidiyoucef/devops-project/pulls?q=is%3Apr+author%3Agithubaidiyoucef+is%3Amerged) |
| **HADJI Youcef Houssam** | [`@Hadji-Youcef`](https://github.com/Hadji-Youcef) | [PR Link](https://github.com/githubaidiyoucef/devops-project/pulls?q=is%3Apr+author%3AHadji-Youcef+is%3Amerged) |
| **ZERNOUH Lakhdar** | [`@nearZL17`](https://github.com/nearZL17) | [PR Link](https://github.com/githubaidiyoucef/devops-project/pulls?q=is%3Apr+author%3AnearZL17+is%3Amerged) |

---

## Exercise 1: Identity and Import (Part A)
- **(a) Hidden Files:** The two dotfiles present in the project root (excluding `.` and `..`) are `.env.example` and `.gitignore`.
- **(b) Shell Wildcard Behavior:** Running `cp -r ./ctf-scoreboard/*` fails to copy hidden files because standard POSIX shell wildcard patterns do not expand to filenames starting with a dot `.`. Using `cp -r /path/to/ctf-scoreboard/.` copies all directory contents including dotfiles.
- **(c) Untracking Indexed Files:** Adding a file to `.gitignore` only prevents untracked files from being indexed. If `.env` is already committed, Git continues tracking its changes. To stop tracking without deleting the local file, run `git rm --cached .env`.

---

## Exercise 2: Branching and Merge Conflicts (Part B)
- **(a) Cause of Merge Conflict:** Both `main` and `tune/lower` modified the exact same line (`points: 500`) under the `the-long-game` entry in `data/challenges.yaml` with conflicting values (`600` vs `450`), causing Git to pause the merge for manual resolution.
- **(b) Merging Distinct Line Modifications:** When changes affect different items or non-overlapping lines in the same file, Git's 3-way merge algorithm automatically integrates both sets of changes without requiring manual intervention.
- **(c) Automated Conflict Marker Checks:** Automated checks for `<<<<<<<` markers can run locally via Git hooks (pre-commit/pre-push) or remotely as a step in a CI/CD pipeline (e.g., GitHub Actions).

---

## Exercise 3: Finding Faulty Commits with Bisect (Part C)
- **(a) Evaluation Steps:** `git bisect` required **5** steps to isolate the faulty commit out of 31 total commits.
- **(b) Theoretical Bound Comparison:** $\lceil \log_2(31) \rceil = \lceil 4.954 \rceil = 5$. The experimental step count matches the binary search upper bound exactly.
- **(c) Isolation of Test Script:** Storing `check.sh` inside the repository causes Git to delete or modify the script when checking out older historical commits prior to its creation. Placing it in `/tmp/check.sh` guarantees persistence across checkouts.
- **(d) Effect of Squashing:** Squashing all 31 commits into a single commit forces `git bisect` to flag that single monolithic commit as bad, preventing exact isolation of the line change.

---

## Exercise 4: Secret Scanning in Git History (Part D)
- **(a) Scan Exit Codes:** 
  - Working tree scan (`gitleaks dir`): `exit = 0` (no leak in active workspace).
  - Git history scan (`gitleaks git`): `exit = 1` (secret detected in historical commits).
- **(b) Matched Rule Name:** `aws-access-token`.
- **(c) Persistence in Git DAG:** Git commit history forms an append-only Directed Acyclic Graph (DAG) of immutable snapshots. Removing a secret in a new commit creates a new snapshot pointing to the previous commit parent, keeping the secret permanently accessible in the commit graph unless rewritten.
- **(d) Incident Response Procedure:**
  1. **Revoke / Rotate Credential Immediately:** Neutralize security risk at the cloud provider level.
  2. **Purge Secret from History:** Rewrite history using `git-filter-repo` or BFG and force push.
  - *Rationale:* Exposed keys are assumed compromised immediately; revoking stops exploitation while history cleanup is performed.

---

## Exercise 5: Shared & Protected Repository (Part E)
- **(a) Branch Protection as Security Control:** Branch protection serves as an access control boundary preventing unvetted, untrusted, or malicious code from reaching production. Enforcing mandatory peer reviews and blocking direct force pushes mitigates insider threats and establishes an immutable audit trail.
- **(b) Prevented Attack Types:** Reduces the risk of **Supply Chain Attacks** (unauthorized commits or dependency hijacking) and **Repository Tampering** (destructive history rewrites via force-push).
- **(c) GitHub Free Tier Requirement:** On GitHub Free accounts, advanced branch protection features (mandatory pull request approvals and ruleset enforcement) are restricted to **public repositories**.