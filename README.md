# cicd-example-success

A simple Python calculator project used to demonstrate the B-Team CI/CD system.

This repository contains:
- A working Python project with passing tests
- A valid pipeline definition (`example_good_default.yaml`) that succeeds end-to-end

---

## Prerequisites

Make sure you have the B-Team CI/CD system installed and running before proceeding.
Refer to the main repository's README for installation instructions.

---

## Demo Commands

### 1. Verify a valid pipeline (should pass)

```bash
cicd verify .pipelines/example_good_default.yaml
```

Expected output:
```
✓ '.pipelines/example_good_default.yaml' is valid
```

Verify all pipeline files in the folder at once:

```bash
cicd verify .pipelines/
```

Expected output:
```
✓ All 1 pipeline configuration file(s) in '.pipelines/' are valid
```

---

### 2. Dry run — preview execution order

```bash
cicd dryrun .pipelines/example_good_default.yaml
```

Expected output:
```yaml
build:
  install-deps:
    image: python:3.11
    script:
    - pip install -r requirements.txt
  compile:
    image: python:3.11
    needs:
    - install-deps
    script:
    - python -m py_compile src/main.py
test:
  unit-tests:
    image: python:3.11
    script:
    - pip install -r requirements.txt
    - pytest tests/
```

---

### 3. Run the pipeline (should succeed)

```bash
cicd run --name example_good_default
```

Expected output:
```
Submitting pipeline 'example_good_default' for execution...
✓ Pipeline queued successfully (run ID: 1)
Waiting for pipeline to complete...

✓ Pipeline completed successfully!
Total time: 10s
Run ID: 1

Stage Summary:
  ✓ build
  ✓ test
```

---

### 4. View pipeline report

After running the pipeline at least once:

All runs for a pipeline:
```bash
cicd report --pipeline example_good_default
```

Expected output:
```yaml
pipeline:
  name: example_good_default
  runs:
  - run-no: 1
    status: success
    git-repo: https://github.com/EstherrrC/cicd-example-good.git
    git-branch: main
    git-hash: 32d9c458bf8a194af39649417469b5c9407c7e6f
    start: '2026-03-10T22:27:28.143608+00:00'
    end: '2026-03-10T22:27:34.194895+00:00'
```

Details for a specific run:
```bash
cicd report --pipeline example_good_default --run 1
```

Details for a specific stage:
```bash
cicd report --pipeline example_good_default --run 1 --stage build
```

Details for a specific job:
```bash
cicd report --pipeline example_good_default --run 1 --stage build --job compile
```

---

## Additional Notes

- All pipeline jobs use `python:3.11`, a public Docker Hub image pulled automatically by the system.
- The `compile` job uses `needs: [install-deps]` to ensure dependencies are installed before compilation — both jobs are in the same `build` stage.