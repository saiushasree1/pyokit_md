The `beta.py` file implements functions related to the Beta distribution, including the Beta function, the regularized incomplete Beta function, and the probability density function (PDF) and cumulative distribution function (CDF) for the Beta distribution.

**Core Functionality:**

*   **`__contfractbeta(a, b, x, ITMAX=200)`**: This is a private helper function that evaluates the continued fraction form of the incomplete Beta function. It's a numerical recipe adapted from "Numerical Recipes in C" and is crucial for calculating the `reg_incomplete_beta` function. It includes a `PyokitError` for convergence issues.
*   **`beta(a, b)`**: Calculates the Beta function using the `math.lgamma` (log-gamma) function, which is a standard way to compute it to avoid numerical overflow.
*   **`reg_incomplete_beta(a, b, x)`**: Computes the regularized incomplete Beta function. It handles edge cases where `x` is 0 or 1 and uses `__contfractbeta` for the main calculation, choosing between two forms based on the value of `x` for numerical stability.
*   **`beta_pdf(x, a, b)`**: Calculates the probability density function (PDF) of the Beta distribution given `x`, `a` (alpha), and `b` (beta) parameters.
*   **`beta_cdf(x, a, b)`**: Calculates the cumulative distribution function (CDF) of the Beta distribution, which internally calls `reg_incomplete_beta`.

**Structure and Design:**

*   The code is organized into logical sections using comments like "HELPER FUNCTIONS", "BETA FUNCTIONS", "BETA DISTRIBUTION", and "UNIT TESTS".
*   It utilizes standard Python libraries such as `math` and `unittest`.
*   It imports `PyokitError` from `pyokit.common.pyokitError`, indicating it's part of a larger `pyokit` library.
*   Docstrings are provided for each function, explaining their purpose, parameters, and return values.

**Important Details:**

*   **Numerical Stability**: The `__contfractbeta` and `reg_incomplete_beta` functions are adapted from "Numerical Recipes in C," suggesting attention to numerical stability in their implementation. The choice of which form of the incomplete beta function to use in `reg_incomplete_beta` (based on `x < (a + 1) / (a + b + 2)`) is also for numerical accuracy.
*   **Error Handling**: `__contfractbeta` raises a `PyokitError` if the continued fraction does not converge within the specified `ITMAX` iterations.
*   **Dependencies**: It depends on `pyokit.common.pyokitError`.
*   **Unit Tests**: A `BetaDistTests` class, inheriting from `unittest.TestCase`, provides comprehensive unit tests for `beta`, `reg_incomplete_beta`, `beta_pdf`, and `beta_cdf` functions, ensuring their correctness and accuracy with various inputs, including edge cases and floating-point comparisons using `assertAlmostEqual`.
*   **License**: The file is licensed under the GNU General Public License (GPL) version 3 or later.
*   **Execution**: When run as a standalone script, it executes the unit tests using `unittest.main()`.