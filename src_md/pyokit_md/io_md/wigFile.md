The `WigFile.py` script defines a `WigFile` class designed to load, store, and provide efficient random access to data from Wiggle (WIG) files. WIG files typically contain genomic data with associated scores, defined over specific chromosomal regions.

**Core Functionality and Structure:**

1.  **`WigFileError` Exception:** A custom exception class `WigFileError` is defined for handling specific errors within the `WigFile` class, particularly when multiple Wiggle elements unexpectedly overlap at a given point.

2.  **`WigFile` Class:**
    *   **Constructor (`__init__`)**:
        *   Takes a `filename` (either a string path or a file-like object) and an optional `verbose` flag.
        *   Initializes `self.itrees` as an empty dictionary. This dictionary will store `IntervalTree` objects, keyed by chromosome name.
        *   Calls the private `__load` method to parse the WIG file and populate the `itrees`.
    *   **`__load` Method (Private):**
        *   Reads the input WIG file using a `wigIterator` (imported from `pyokit.io.wigIterators`).
        *   Groups `WigElement` objects by chromosome.
        *   For each chromosome, it constructs an `IntervalTree` (from `pyokit.datastruct.intervalTree`) using the `WigElement` objects belonging to that chromosome. The `openEnded=True` argument suggests that the intervals are treated as open at their end boundary.
        *   This pre-loading and indexing using `IntervalTree` is crucial for achieving efficient random access (O(log(n))) after an initial O(n log(n)) build time.
    *   **`contains(self, chrom, point)`:**
        *   Checks if any Wiggle element covers a given `point` on a specified `chrom`.
        *   Returns `True` if an intersecting element is found, `False` otherwise.
    *   **`getElement(self, chrom, point)`:**
        *   Retrieves the `WigElement` that intersects a given `point` on a specified `chrom`.
        *   Returns `None` if no element intersects.
        *   **Important Detail:** Raises a `WigFileError` if *more than one* element intersects the specified point, indicating an unexpected overlap in the WIG data for this operation.
    *   **`getScore(self, chrom, point)`:**
        *   Retrieves the `score` (a float) of the `WigElement` that intersects a given `point` on a specified `chrom`.
        *   Returns `None` if no element intersects.
        *   Also raises a `WigFileError` if multiple elements intersect, leveraging the `getElement` method.

3.  **`WigFileUnitTests` Class:**
    *   A standard `unittest.TestCase` class for testing the `WigFile` functionality.
    *   **`setUp`:** An empty `setUp` method.
    *   **`testGetValue`:**
        *   Creates a dummy WIG input string.
        *   Uses `DummyInputStream` to simulate a file handle.
        *   Initializes a `WigFile` instance with the dummy input.
        *   Tests `getScore` with various `(chromosome, point, expected_score)` tuples, asserting that the returned score matches the expectation.
    *   **`testFailOverlap`:**
        *   Creates a dummy WIG input string with deliberately overlapping entries for the same chromosome (`chr1` at point `9`).
        *   Asserts that calling `wf.getScore("chr1", 9)` raises a `WigFileError`, confirming the overlap detection mechanism.

**Key Design Principles and Optimizations:**

*   **Efficient Random Access:** The core strength of this class lies in its use of `IntervalTree` data structures. By organizing Wiggle elements within `IntervalTree` objects per chromosome, it enables logarithmic time complexity (O(log(n))) for querying points, which is significantly faster than linear scans for large files.
*   **Memory Usage:** The entire WIG file is loaded into memory during initialization. This is a trade-off: it allows for fast queries but means that very large WIG files could consume a significant amount of RAM.
*   **Error Handling:** Explicit error handling for overlapping elements (which might not be desired for single-point queries) is implemented via `WigFileError`.
*   **Modular Dependencies:** It leverages external `pyokit` modules for WIG file iteration (`wigIterators`), interval tree implementation (`intervalTree`), and testing utilities (`dummyfiles`).

In summary, `WigFile.py` provides a robust Python class for managing and querying Wiggle file data, prioritizing efficient random access through the use of interval trees, while also providing clear error handling for common data integrity issues like overlapping entries.