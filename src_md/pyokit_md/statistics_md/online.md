The provided Python code, `online.py`, is designed to compute statistics on data streams. Currently, it includes a class for calculating a rolling mean and a corresponding set of unit tests.

**Core Functionality:**

*   **`RollingMean` Class:** This class tracks the running mean of values added to it.
    *   `__init__()`: Initializes the internal `_mean` to `None` and `_vals_added` to 0.
    *   `add(v)`: Incorporates a new value `v` into the rolling mean calculation. It updates the mean incrementally using the formula `mean = mean + ((v - mean) / float(vals_added))`, which is an efficient way to calculate a running average without storing all previous values.
    *   `mean` (property): A read-only property to retrieve the current calculated mean.

**Structure and Organization:**

*   The code is structured with clear section headers using comments to delineate the class definition and unit tests.
*   It includes standard boilerplate for a Python module, including licensing information (GNU General Public License v3 or later) and authorship details.
*   The `unittest` module is used for testing.

**Important Details:**

*   **Online Computation:** The `RollingMean` class performs "online" computation, meaning it updates the mean as new data arrives without requiring all data points to be available simultaneously. This is efficient for large data streams.
*   **Unit Tests:** The `TestOnlineStats` class provides a `test_rolling_mean` method to verify the correctness of the `RollingMean` implementation with a sequence of values and `assertEqual`/`assertAlmostEqual` assertions.
*   **Main Entry Point:** The `if __name__ == "__main__":` block allows the script to be run directly, executing the unit tests using `unittest.main()`.

In summary, `online.py` currently provides a fundamental building block for online statistical analysis by implementing a robust rolling mean calculation, complete with testing to ensure its accuracy.