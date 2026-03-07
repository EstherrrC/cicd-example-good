# cicd-example-good

A simple Python calculator project used to demonstrate the B-Team CI/CD system.

This repository contains:
- A working Python project with passing tests
- A valid pipeline definition (`default`) that succeeds end-to-end
- An invalid pipeline definition (`broken-validation`) to demonstrate validation failure

---

## Prerequisites

Make sure you have the B-Team CI/CD system installed and running before proceeding.
Refer to the main repository's README for installation instructions.

---

## Demo Commands

### 1. Verify a valid pipeline (should pass)
```bash
cicd verify .pipelines/default.yaml
```

Expected output:

.pipelines/default.yaml: OK

---

### 2. Verify an invalid pipeline (should fail)
```bash
cicd verify .pipelines/invalid.yaml
```

Expected output:

.pipelines/invalid.yaml:18:12: ERROR, job 'nonexistent-job' listed in needs of 'test-job' is not defined in stage 'test'

---

### 3. Dry run — preview execution order
```bash
cicd dryrun .pipelines/default.yaml
```

Expected output:
```yaml
lint:
  lint-check:
    image: python:3.12-slim
    script:
      - pip install pyflakes --quiet
      - python -m pyflakes src/calculator.py
test:
  run-tests:
    image: python:3.12-slim
    script:
      - pip install pytest --quiet
      - pytest tests/ -v
```

---

### 4. Run the pipeline (should succeed)
```bash
cicd run --name default
```

Expected output:

[Pipeline: default] Starting run #1
[Stage: lint] Running job: lint-check ... ✓ passed
[Stage: test] Running job: run-tests  ... ✓ passed
[Pipeline: default] Run #1 completed: SUCCESS

---

### 5. View pipeline report

After running the pipeline at least once:
```bash
cicd report --pipeline default
```
```bash
cicd report --pipeline default --run 1
```
```bash
cicd report --pipeline default --run 1 --stage test
```
```bash
cicd report --pipeline default --run 1 --stage test --job run-tests
```

---

## Additional Explanation

- The error in `invalid.yaml` is that `needs` references a job that does not exist in the same stage, which corresponds to validation rule #4 in the requirements.
- Both pipelines use `python:3.12-slim`, which is a public image on Docker Hub and will be pulled automatically by the system.