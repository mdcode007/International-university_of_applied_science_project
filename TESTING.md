# TESTING — TRACKK Habit Tracker

This document describes the test suite for TRACKK, including what is covered, how to run the tests, and how the tests are structured.

---

## Overview

The test suite is located at `habit/tests.py` and is written using Django's built-in `TestCase` framework. It covers the full application across **7 test classes** and **45+ individual test cases**, targeting model logic, ORM managers, view behaviour, authentication controls, and the completion API.

Tests use an in-memory SQLite database — no environment variables or external services are required.

---

## Running the Tests

### Prerequisites

```bash
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

### Run the full test suite

```bash
python manage.py test
```

### Run with verbose output

```bash
python manage.py test --verbosity=2
```

### Run a specific test class

```bash
python manage.py test habit.tests.CurrentStreakTests
python manage.py test habit.tests.AuthenticationTests
python manage.py test habit.tests.HabitViewCRUDTests
```

### Run a single test method

```bash
python manage.py test habit.tests.CurrentStreakTests.test_daily_three_consecutive_days
```

### Preserve the test database between runs

```bash
python manage.py test --keepdb
```

---

## Test Classes

### HabitCompletionCheckTests
Tests `is_completed_on()` and `is_completed_in_week()` — verifies completions are date-isolated and do not bleed across days or weeks.

### CurrentStreakTests
Tests the `current_streak` property for daily and weekly habits — covers zero, single, consecutive, and broken streaks.

### LongestStreakTests
Tests the `longest_streak` property — verifies correct identification of the longest historical run across multiple gaps.

### StreakStartedTests
Tests the `streak_started` property — verifies the correct start date is returned for active streaks.

### HabitManagerTests
Tests the custom ORM manager — `for_user()`, `daily_for_user()`, `weekly_for_user()` — verifies user-scoped filtering and no cross-user leakage.

### AuthenticationTests
Tests access control — unauthenticated users are redirected (302), and cross-user attempts on delete, edit, and detail views return 403.

### HabitViewCRUDTests
Tests full CRUD flows — dashboard, create, edit, delete, daily view, weekly view, detail view, and public home page.

### HabitCompletionAPITests
Tests the complete/undo endpoints — 200 responses, database writes, idempotency, streak values in response, 404 on bad undo, and 405 on GET.

---

## Design Notes

- All tests use Django `TestCase` — each test is wrapped in a transaction and rolled back after.
- Helper functions (`make_user`, `make_habit`, `complete_on`) eliminate repetition.
- No fixtures loaded — all data is created programmatically.
- No `.env` required — tests default to SQLite automatically.
