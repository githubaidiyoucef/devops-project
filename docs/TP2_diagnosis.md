# TP2 diagnosis (from the pipeline log only)

| Defect | Gate that failed | Name in the log | One-line cause |
|---|---|---|---|
| Test 1 | tests | tests/test_api.py::test_the_second_solver_is_not | first_blood is True for the second solver; failed by assertion (assert True is False) |
| Test 2 | tests | tests/test_challenges.py::test_an_empty_document_defines_no_challenges | parse("") raises ChallengeFileError (challenges.py:66) instead of returning []; failed by exception |
| Credential | secret scan | gitleaks: "leaks found: 1" (no details in the log) | a fake secret is committed in the repo; exact file and rule are not shown in the log |
| Dependency | dependency audit | python-multipart 0.0.6 (PYSEC-2024-38 and others) | vulnerable pinned version; fix versions go up to 0.0.31 |
