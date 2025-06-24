The `pyokitMain.py` script serves as the main dispatch mechanism for the `pyokit` suite of bioinformatics tools. It acts as a central entry point, parsing command-line arguments to determine which specific `pyokit` script (e.g., `fdr`, `index`, `join`) should be executed.

**Key Functionality:**

*   **Script Dispatching:** The core function `dispatch(args)` takes command-line arguments, identifies the requested script name, and then calls the appropriate handler function for that script.
*   **Module Loading:** It dynamically imports various `pyokit.scripts` modules based on their functionality.
*   **`rpy2` Dependency Handling:** It checks for the presence and functionality of the `rpy2` library, which is required for certain scripts like `fdr`. If `rpy2` is not available, the `fdr` script is disabled, and an informative error message is displayed to the user.
*   **Error Handling:** The `main()` function provides robust error handling for `IOError`, `PyokitIOError`, and general `PyokitError` exceptions, printing informative messages to `stderr` and exiting with a non-zero status code upon error.
*   **Unit Testing:** Includes a `PyokitSmokeTests` class that performs basic "smoke tests" by dispatching various `pyokit` scripts with a `-h` (help) argument to ensure they can be invoked without immediate crashes. This uses the `mock` library to suppress `stdout` and `stderr` during testing.

**Structure:**

1.  **Header and License:** Standard copyright and GPLv3 licensing information.
2.  **Imports:**
    *   Standard Python modules (`sys`, `unittest`).
    *   `mock` for testing.
    *   Conditional import of `rpy2.robjects.r` to check for its availability.
    *   `pyokit` exceptions (`PyokitIOError`, `PyokitError`).
    *   `pyokit` script modules (e.g., `fdr`, `index`, `conservationProfile`).
3.  **Exception Classes:** Defines `NoSuchScriptError` as a custom exception for when an unknown script is requested.
4.  **Dispatch Handlers:** A series of `dispatch_` functions (e.g., `dispatch_fdr`, `dispatch_index`) that encapsulate the call to the `main` or `_main` function of their respective `pyokit` script module. These handlers also manage `rpy2`-specific behavior for the `fdr` script.
5.  **`dispatchers` Dictionary:** A dictionary mapping script names (strings) to their corresponding dispatch handler functions. This is crucial for dynamic dispatch.
6.  **Main Program Logic (`dispatch` and `main` functions):**
    *   `dispatch(args)`: The core logic for identifying the script and calling its handler.
    *   `main()`: The entry point for the entire application, handling command-line argument parsing and error catching.
7.  **Unit Tests (`PyokitSmokeTests`):** A `unittest.TestCase` subclass to verify basic functionality.
8.  **Main Entry Point (`if __name__ == "__main__":`):** Standard Python idiom to run `unittest.main()` when the script is executed directly.

**Important Details:**

*   **Command-line Interface:** The script expects the first argument after `pyokit` to be the name of the desired script (e.g., `pyokit fdr`, `pyokit index`). Subsequent arguments are passed to the individual script's `main` function.
*   **Error Handling Philosophy:** The script aims to catch common errors and provide user-friendly messages, distinguishing between I/O errors and other `pyokit`-specific errors.
*   **Conditional `rpy2` Dependency:** The explicit check for `rpy2` is a good practice for external dependencies, making the `pyokit` suite more robust even if not all optional dependencies are met.
*   **Internal vs. External APIs:** Some dispatch functions call `script.main(args, "prog_name")` while others call `script._main(args, "prog_name")`. The use of `_main` suggests these might be considered more internal or helper functions within their respective modules, though they are still exposed through this dispatch mechanism.