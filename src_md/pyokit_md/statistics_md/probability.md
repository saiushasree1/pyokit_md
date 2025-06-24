The `probability.py` file provides classes and functions for handling probabilistic selections and generations.

**Core Functionality:**

*   **`WeightedRandom` Class:** This class enables the random selection of an object from a given list, where the probability of selecting an object is proportional to its assigned weight. It achieves this by constructing an `IntervalTree` (imported from `pyokit.datastruct.intervalTree`) based on the cumulative weights of the candidates.
    *   **`Interval` Inner Class:** A simple helper class within `WeightedRandom` to represent an interval with a start, end, and the associated object.
    *   **`__init__(self, candidates, weights)`:** Initializes the `WeightedRandom` instance. It raises a `ProbabilityError` if the number of candidates doesn't match the number of weights. It then builds an internal `IntervalTree` from the provided weights and candidates.
    *   **`_buildTree(self, weights, candidates)`:** A private method responsible for creating the `IntervalTree`. It calculates cumulative weights and creates `Interval` objects, which are then used to populate the `IntervalTree`.
    *   **`choose(self)`:** Selects a random object based on the assigned weights. It generates a random number within the total weight range and uses the `IntervalTree` to find the corresponding object. It raises a `ProbabilityError` if the random choice fails to identify exactly one object.

*   **`generateProbabilities(num)` Function:** This standalone function generates a list of `num` probabilities. Each probability `x` in the list satisfies `0 <= x <= 1`, and the sum of all probabilities in the list equals 1. It achieves this by generating random numbers, sorting them, and calculating the differences between consecutive sorted numbers.

**Error Handling:**

*   **`ProbabilityError` Class:** A custom exception class derived from `Exception`, used to signal errors specific to probability operations (e.g., mismatched weights and candidates, or issues during random selection).

**Structure and Dependencies:**

*   The file uses standard Python imports like `random`, `unittest`, and `sys`.
*   It depends on the `IntervalTree` data structure from the `pyokit.datastruct` library.
*   The code is organized into sections for exception classes, probability functions, and unit tests.

**Unit Tests (`ProbabilityTests` Class):**

*   **`testWeightedChoice()`:** This test verifies the `WeightedRandom` class by performing a large number of random selections and asserting that the observed frequencies of selections closely match the expected probabilities based on the weights.
*   **`testGenerateProbabilities()`:** This test verifies the `generateProbabilities` function by generating various sets of probabilities and asserting that their sum is 1 and that each individual probability is within the valid range [0, 1].

**Usage:**

The `if __name__ == "__main__":` block allows the script to be run directly to execute the defined unit tests using `unittest.main()`.

**Important Details:**

*   The `WeightedRandom` class uses an `IntervalTree` for efficient lookup of objects based on random numbers, which is crucial for performance when dealing with a large number of candidates.
*   The license is GNU General Public License v3 or later, indicating that it is free software.
*   The `generateProbabilities` function is useful for creating a set of normalized probabilities that sum to one, often needed for simulations or distributions.