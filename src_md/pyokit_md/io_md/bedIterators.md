The `bedIterators.py` script provides Python iterators and functions specifically designed for processing genomic data in BED (Browser Extensible Data) format. It aims to simplify tasks like parsing BED files, building interval trees from BED data, and comparing multiple BED streams.

**Core Functionality:**

*   **BED File Parsing:** The `BEDIterator` function is a central component, providing an efficient way to read BED files line by line and yield `GenomicInterval` objects. It supports various options for handling malformed lines, progress reporting, and enforcing sorting order.
*   **Interval Tree Construction:** The `intervalTrees` function builds a dictionary of `IntervalTree` objects, one for each chromosome, from a BED stream or file. Interval trees are efficient data structures for querying overlapping intervals, making this function useful for genomic range operations.
*   **Multi-Stream Iteration:**
    *   `pairedBEDIterator`: Iterates over multiple sorted BED streams simultaneously, yielding lists of `GenomicInterval` objects that match across all streams based on user-defined comparison criteria (e.g., ignoring strand, score, or name). It includes a "mirror" option to insert missing intervals into streams to maintain consistent output across all inputs.
    *   `BEDDuplicateIterator`: Compares two BED streams sorted by name, yielding pairs of lists. One list contains reads unique to the first file, and the other contains reads unique to the second. Duplicate reads (by name) are grouped together. It also provides options to remove common tags (junc and PE tags) from read names before comparison.
    *   `BEDUniqueIterator`: Similar to `BEDDuplicateIterator`, but focuses on finding unique reads. It yields pairs of `GenomicInterval` objects where `r1` is unique to the first file and `r2` is unique to the second. It can handle cases where one file has fewer unique entries and provides a "best" option to select the read with a smaller score in case of duplicate names.

**Structure and Key Details:**

*   **Constants:** Defines integer constants (`ITERATOR_SORTED_START`, `ITERATOR_SORTED_END`, etc.) to represent different sorting orders for BED files.
*   **Exception Handling:** Introduces a custom `BEDError` exception for specific errors encountered during BED file processing.
*   **Dependencies:** Relies on several modules from `pyokit`:
    *   `pyokit.datastruct.genomicInterval`: For handling `GenomicInterval` objects and parsing BED strings.
    *   `pyokit.testing.dummyfiles`: Used in unit tests for creating in-memory file streams.
    *   `pyokit.util.progressIndicator`: For displaying progress messages during file processing.
    *   `pyokit.util.fileUtils`: Specifically `linesInFile` to get the total line count for progress indicators.
    *   `pyokit.datastruct.intervalTree`: The core data structure for interval tree operations.
*   **Sorting Requirements:** Several iterator functions (e.g., `pairedBEDIterator`, `BEDDuplicateIterator`, `BEDUniqueIterator`) require their input BED streams to be pre-sorted by specific fields (e.g., chromosome and start/end, or name) for correct and efficient operation. This is a crucial detail for users.
*   **Progress Indicators:** The `BEDIterator` and `intervalTrees` functions can display progress to `sys.stderr` if `verbose` is set to `True`, which is helpful for large files.
*   **Unit Tests:** The script includes a `unittest.TestCase` class (`BEDIteratorUnitTests`) with comprehensive tests for each of the main iterator functions, covering various scenarios including sorted/unsorted inputs, mirroring, and duplicate/unique filtering.

**Important Considerations for Users:**

*   **Input File Sorting:** Users *must* ensure that input BED files are correctly sorted according to the `sortedby` parameter when using `BEDIterator`, `pairedBEDIterator`, `BEDDuplicateIterator`, or `BEDUniqueIterator`. Failure to do so will result in a `BEDError`.
*   **Memory Usage:** The `intervalTrees` function loads all genomic regions into memory before building interval trees. For extremely large BED files, this could lead to high memory consumption.
*   **BED Format Strictness:** The `parseBEDString` function (likely from `pyokit.datastruct.genomicInterval`) handles the parsing, and `BEDIterator` can raise `GenomicIntervalError` if lines are malformed. The `dropAfter` parameter provides flexibility for handling BED files with extra columns.
*   **Python 2 Compatibility:** The `Queue` module import and the `reduce` function suggest this code is written for Python 2. For Python 3, `queue.Queue` and `functools.reduce` would be used.