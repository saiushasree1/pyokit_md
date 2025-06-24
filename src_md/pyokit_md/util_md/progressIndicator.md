The `progressIndicator.py` file defines a `ProgressIndicator` class designed to display task progress to the user, typically via `stderr`.

**Core Functionality:**

*   **`ProgressIndicator` Class:**
    *   **Initialization (`__init__`)**: Takes `totalToDo` (the total number of items to process), an optional `messagePrefix`, and an optional `messageSuffix`. It initializes internal counters (`done`), flags (`finished`), and stores the message components.
    *   **`showProgress` Method**:
        *   Calculates the completion percentage based on `done` and `totalToDo`.
        *   Constructs a progress message including the prefix, percentage, and suffix.
        *   Uses `\r` (carriage return) to overwrite the previous line, creating an in-place progress update.
        *   Writes the message to the specified stream (defaults to `sys.stderr`) and flushes it.
        *   Crucially, it only writes a new message if it differs from the previously displayed one, reducing unnecessary output.
        *   When 100% is reached, it sets `finished` to `True` and prints a newline character, indicating completion.
*   **Error Handling:** Defines `ProgressIndicatorError` which inherits from `PyokitError`, providing a specific exception type for this module.

**Structure and Important Details:**

*   **File Header:** Includes standard licensing (GNU GPLv3) and author information.
*   **Imports:** Uses `math` for `ceil`, `sys` for `stderr`, `unittest` for testing, and `StringIO` for capturing output during tests. It also imports `PyokitError` from `pyokit.common`.
*   **Unit Tests (`TestProgressIndicator`):**
    *   The `test_basic` method verifies that the `ProgressIndicator` outputs exactly 100 distinct percentage updates (from 1% to 100%) and then a newline, regardless of the total number of increments. It achieves this by writing to an in-memory `StringIO` buffer and comparing the output.
*   **Main Entry Point:** If the script is run directly, `unittest.main()` is called to execute the defined unit tests.

In essence, `ProgressIndicator` provides a simple, console-based progress bar that efficiently updates a single line in the terminal to show the percentage completion of a task.