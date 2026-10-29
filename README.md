# Week 3 Courses API Weekend Activity – Unit and API Testing

## DESCRIPTION

A Courses API for creating and retrieving course information.

## TECHNOLOGIES USED

- FastAPI for the API
- Uvicorn for running the API
- SQLAlchemy for working with the database using Python
- SQLite for the database
- pytest for automated testing

## INSTRUCTIONS

### Create the Virtual Environment

**MacOS/Linux:**

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### Install Packages

Inside terminal command:

```bash
python -m pip install -r requirements.txt
```

### Activate the API

Inside terminal command:

```bash
uvicorn main:app --reload
```

## CHECKLIST

### 1) Unit Tests and API Tests

**Unit Tests (`tests/test_rules.py`)**

**What is being tested?**

The `enrolment_is_open()` function is being tested before, exactly at, and after a timestamp.

**API Tests (`tests/test_api.py`)**

**What is being tested?**

The FastAPI project's database interactions, course creation, validation, and responses.

### 2) Fresh Test Data

In `conftest.py`, the `tmp_path` parameter (passed into the `app_factory` function) provides a fresh, temporary directory where an empty database is created every time a test function runs.

The `app_factory` function itself creates a FastAPI application that connects to the temporary database by overriding `get_db`.

This way, the original `courses.db` is preserved and tests do not depend on their execution order.

### 3) Boundary Failure

In `rules.py`, temporarily changing `<` to `<=` in the `enrolment_is_open()` function causes a test to fail. This is because the change allows a student to enrol at the exact prescribed closing time.

The closing time is defined in `tests/test_rules.py`:

```python
closes_at = datetime(2026, 11, 1, 12, tzinfo=timezone.utc)
```

**Failed test result:**

```text
FAILED tests/test_rules.py::test_enrolment_boundary[0-False] - assert True is False
============================== 1 failed, 2 passed in 0.03s ==============================
```

Changing `<=` back to `<` in `rules.py` and rerunning the tests results in all three unit tests passing.

### 4) Test Command and Results

**Terminal command:**

```bash
python -m pytest -v
```

**RESULT: 11 PASSED**

```text
tests/test_api.py::test_create_and_retrieve PASSED                         [  9%]
tests/test_api.py::test_duplicate_does_not_add_row PASSED                  [ 18%]
tests/test_api.py::test_invalid_input_does_not_add_row PASSED              [ 27%]
tests/test_api.py::test_missing_course PASSED                              [ 36%]
tests/test_api.py::test_starts_empty PASSED                                [ 45%]
tests/test_api.py::test_data_survives_new_application PASSED               [ 54%]
tests/test_api.py::test_title_omitted PASSED                               [ 63%]
tests/test_api.py::test_writes_allowed_after_failed_request PASSED         [ 72%]
tests/test_rules.py::test_enrolment_boundary[-1-True] PASSED               [ 81%]
tests/test_rules.py::test_enrolment_boundary[0-False] PASSED                [ 90%]
tests/test_rules.py::test_enrolment_boundary[1-False] PASSED                [100%]
```

### LIMITATIONS

Whilst the tests check a range of invalid inputs, they do not cover all possible scenarios or edge cases.