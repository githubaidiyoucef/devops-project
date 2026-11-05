# TP2 answers

Youcef Aidi (@githubaidiyoucef)
Hadji Youcef Houssem Eddine
Zernouh Lakhdar

## Exercise 1

(a) With `pull_request` the pipeline runs on the change before it is merged into main. So the merge button can be blocked if the pipeline is red. With `push` it only runs after the code is already on main, so it is too late.

(b) Lint is very fast and cheap. If the code has a style or syntax error, we find out in a few seconds and we don't waste time running the tests.

(c) If the tests take 20 minutes, a small error that lint finds in 2 seconds would make us wait 20 minutes for nothing. So running the cheap gates first saves much more time.

## Exercise 2

(a) `git commit --no-verify` (or `-n`) skips the pre-commit hook.

(b) Some people can still put a secret on main even if the pipeline detects it:
- an admin, if the branch rule is not applied to admins;
- someone who changes `ci.yml` in his own pull request and removes the secret scan step;
- someone who adds the secret to an allowlist or to a `.gitleaksignore` file.

## Exercise 3

(a) The error line in the log was:

`E   ModuleNotFoundError: No module named 'scoreboard'`

It came from `tests/conftest.py` line 17, `from scoreboard.app import create_app`. The step ended with exit code 4, so no test even started.

(b) In a src layout the code is in `src/scoreboard/` and not in the root folder of the repository. When we run `python -m pytest`, Python looks in the current folder, but `src/` is not on its path, so it cannot find `scoreboard`. The command `pip install -e .` installs the project and makes `src/scoreboard` importable. Without it the import fails.

(c) `make install` ran it. I saw `.venv/bin/pip install -q -e .` in its output.

## Exercise 4

My table is in `docs/TP2_diagnosis.md`. I committed and pushed it before I looked at any diff or fixed anything. Short version:

- Test 1: gate tests, `tests/test_api.py::test_the_second_solver_is_not`, failed by assertion (`assert True is False`).
- Test 2: gate tests, `tests/test_challenges.py::test_an_empty_document_defines_no_challenges`, failed by exception (`ChallengeFileError`).
- Credential: gate secret scan, gitleaks said "leaks found: 1".
- Dependency: gate dependency audit, `python-multipart 0.0.6`, with PYSEC-2024-38 and other advisories.

The secret scan log did not show the file or the rule, because the pipeline runs gitleaks without `-v`. I only found the file (`src/scoreboard/db.py`) later, in the diff, after my diagnosis commit.

## Exercise 5

I fixed the four defects and I did not change any test:
- `app.py`: `first_blood = nth <= 2` changed back to `nth == 1`.
- `challenges.py`: I put back `if data is None: return []` in `parse`.
- `db.py`: I removed the fake access key.
- `requirements.txt`: I put back `python-multipart==0.0.32` instead of `0.0.6`.

After the fixes `make test` gave 73 passed, `make lint` printed nothing, gitleaks said no leaks (exit=0), and the pipeline on my pull request became green.

(a) `python-multipart` is a direct dependency, because it is written in `pyproject.toml` on line 22: `"python-multipart>=0.0.9"`.

(b) I ran these two commands:

```
grep -n multipart pyproject.toml
pip show python-multipart | grep -i required-by
```

The first one shows it in the dependencies of the project. In the second one `Required-by:` is empty, so no other package needs it.

## Notes

- The `ci.yml` file lost its indentation when it was copied from the PDF, so GitHub could not read it and `verify` stayed in "waiting for status". I put the spaces back and did not change the steps.
- My first push of `chaos/tp2` failed with "Could not resolve host: github.com" because my internet was down. I pushed again when it came back.

## Screenshots

All the commands I ran locally are in `docs/screenshots/`:

- `01-make-install.png`: make install
- `02-make-test-lint-baseline.png`: make test (73 passed) and make lint before the lab
- `03-partC-remove-install.png`: Part C, I removed `pip install -e .` on a temporary branch
- `04-partD-seed-and-push.png`: Part D, seed.py, the first push failed (no internet), the second worked
- `05-pipeline-log-dependency-audit.png`: GitHub log of the dependency audit
- `06-diagnosis-commit.png`: diagnosis committed and pushed before the fixes
- `07-git-log-and-requirements-diff.png`: the seed commits and the requirements.txt change
- `08-diffs-challenges-and-app.png`: the secret check, the challenges.py change and the e577ff4 commit
- `09-final-test-lint-gitleaks.png`: make test (73 passed), make lint, gitleaks exit=0
- `10-multipart-direct-dependency.png`: python-multipart is a direct dependency
