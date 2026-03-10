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
✓ '.pipelines/default.yaml' is valid
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
Submitting pipeline 'default' for execution...
✓ Pipeline queued successfully (run ID: 1)
Waiting for pipeline to complete...

✓ Pipeline completed successfully!
Total time: 38s
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
  name: default
  runs:
  - run-no: 1
    status: success
    git-repo: https://github.com/EstherrrC/cicd-example-success.git
    git-branch: main
    git-hash: d97f8f8f7a394d4c60310dbfcd2d1af762604dc0
    start: '2026-03-09T02:40:39.294923+00:00'
    end: '2026-03-09T02:40:51.690628+00:00'
```

Details for a specific run:
```bash
cicd report --pipeline example_good_default --run 1
```

Expected output:
```yaml
pipeline:
  name: default
  run-no: 1
  status: success
  start: '2026-03-09T02:40:39.294923+00:00'
  end: '2026-03-09T02:40:51.690628+00:00'
  stages:
  - name: build
    status: success
    start: '2026-03-09T02:40:39.301356+00:00'
    end: '2026-03-09T02:40:49.382020+00:00'
  - name: test
    status: success
    start: '2026-03-09T02:40:39.303668+00:00'
    end: '2026-03-09T02:40:51.689057+00:00'
```

Details for a specific stage:
```bash
cicd report --pipeline example_good_default --run 1 --stage build
```

Expected output:
```yaml
pipeline:
  name: default
  run-no: 1
  status: success
  start: '2026-03-09T02:40:39.294923+00:00'
  end: '2026-03-09T02:40:51.690628+00:00'
  stage:
  - name: build
    status: success
    start: '2026-03-09T02:40:39.301356+00:00'
    end: '2026-03-09T02:40:49.382020+00:00'
    jobs:
    - name: install-deps
      status: success
      start: '2026-03-09T02:40:39.304897+00:00'
      end: '2026-03-09T02:40:49.376926+00:00'
    - name: compile
      status: success
      start: '2026-03-09T02:40:39.304897+00:00'
      end: '2026-03-09T02:40:49.376926+00:00'
```

Details for a specific job:
```bash
cicd report --pipeline example_good_default --run 1 --stage build --job compile
```

Expected output:
```yaml
pipeline:
  name: default
  run-no: 1
  status: success
  start: '2026-03-09T02:40:39.294923+00:00'
  end: '2026-03-09T02:40:51.690628+00:00'
  stage:
  - name: build
    status: success
    start: '2026-03-09T02:40:39.301356+00:00'
    end: '2026-03-09T02:40:49.382020+00:00'
    job:
    - name: compile
      status: success
      start: '2026-03-09T02:40:39.304897+00:00'
      end: '2026-03-09T02:40:49.376926+00:00'
```

---

## Additional Notes

- All pipeline jobs use `python:3.11`, a public Docker Hub image pulled automatically by the system.
- The `compile` job uses `needs: [install-deps]` to ensure dependencies are installed before compilation — both jobs are in the same `build` stage.