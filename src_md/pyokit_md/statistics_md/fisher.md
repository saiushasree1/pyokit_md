The `fisher.py` script provides a Python wrapper for performing Fisher's exact test using R's statistical capabilities through the `rpy2` library.

**Core Functionality:**

*   **`fisherExactTest(a, b, c, d, alternative="two.sided")`**: This is the primary function. It takes four integer arguments (`a`, `b`, `c`, `d`) representing a 2x2 contingency table. It constructs an R matrix with these values and then calls R's `fisher.test` function. The `alternative` parameter allows specifying the type of hypothesis ("two.sided", "greater", or "less"). It returns a tuple containing the p-value and the odds ratio from the Fisher's exact test.

**Structure and Important Details:**

*   **Dependency on `rpy2`**: The script critically depends on the `rpy2` library to interact with R.
*   **Graceful Degradation**: If `rpy2` cannot be imported, the `fisherExactTest` function (and consequently, the entire module's core functionality) is disabled. A message is written to `sys.stderr` indicating the failure. This design ensures the script doesn't crash if the dependency isn't met, but rather informs the user that the specific functionality is unavailable.
*   **Unit Tests (`FisherTests` class)**: The script includes a `unittest.TestCase` class named `FisherTests` to verify the `fisherExactTest` function.
    *   **Conditional Testing**: The unit tests are designed to skip if the `rpy2` module is disabled, preventing test failures in environments without `rpy2`.
    *   **Example Test Case**: `testFisherExact` performs a "greater" alternative Fisher's exact test with specific values and asserts the p-value and odds ratio against expected, pre-calculated results.
*   **Command-Line Execution**: When executed as a standalone script (`if __name__ == "__main__":`), it runs the defined unit tests using `unittest.main()`.
*   **Licensing**: The code is distributed under the GNU General Public License v3 (or any later version).
*   **Documentation**: The code includes docstrings for functions and classes, although the `fisherExactTest` summary is marked with "...".