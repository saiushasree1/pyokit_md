The `genomicInterval.py` file provides tools for manipulating and analyzing genomic regions. It defines a `GenomicInterval` class to represent contiguous segments of a genome and includes functions for operations like computing intersections, unions (collapsing regions), and Jaccard indices between collections of these intervals. It also offers utilities for parsing common genomic file formats (WIG and BED) into `GenomicInterval` objects and an efficient `bucketIterator` for binning genomic elements.

**Core Components:**

1.  **`GenomicInterval` Class:**
    *   Represents a genomic region with `chrom` (chromosome), `start` (inclusive), `end` (exclusive), `name`, `score`, and `strand` attributes.
    *   Implements methods for:
        *   Comparison (`__eq__`, `__lt__` for sorting by chromosome then end).
        *   Calculating length (`__len__`).
        *   Converting to a string representation (BED format).
        *   Calculating `distance` and `signedDistance` between intervals on the same chromosome.
        *   `subtract`ing other intervals.
        *   Determining `sizeOfOverlap` and `intersects` with other intervals.
        *   Checking `isPositiveStrand` or `isNegativeStrand`.
        *   `transform_center`: resizing the interval around its center.

2.  **Exception Classes:**
    *   `GenomicIntervalError`: Base exception for errors related to genomic interval operations.
    *   `InvalidStartIndexError`: Specifically for issues with parsing start indices.

3.  **Functions for Manipulating Collections of Genomic Intervals:**
    *   `jaccardIndex(s1, s2, stranded=False)`: Computes the Jaccard index between two collections of genomic intervals. Currently, `stranded` mode is not implemented.
    *   `intervalTreesFromList(inElements, verbose=False, openEnded=False)`: Builds a dictionary of `IntervalTree` objects (from `pyokit.datastruct.intervalTree`) for each chromosome, allowing for efficient querying of overlapping intervals.
    *   `collapseRegions(s, stranded=False, names=False, verbose=False)`: Computes the union of a set of genomic intervals, collapsing overlapping regions into non-overlapping ones. It can operate with or without considering strand information and can optionally accumulate names. Requires input intervals to be sorted by chromosome and start coordinate.
    *   `__collapse_stranded`: An internal helper function for `collapseRegions` to handle strand-specific collapsing.
    *   `regionsIntersection(s1, s2, collapse=True)`: Calculates the intersection of two lists of genomic regions. It first collapses the input regions before finding overlaps.
    *   `bucketIterator(elements, buckets)`: An iterator that yields each "bucket" interval along with a list of "elements" that overlap that bucket. Both `elements` and `buckets` must be sorted by chromosome and start index.

4.  **Functions for Parsing Genomic Intervals:**
    *   `parseWigString(line, scoreType=int)`: Parses a simple WIG formatted string into a `GenomicInterval` object.
    *   `parseBEDString(line, scoreType=int, dropAfter=None)`: Parses a BED formatted string into a `GenomicInterval` object.

5.  **Unit Tests (`TestGenomicInterval`):**
    *   Comprehensive test suite using `unittest` to verify the functionality of `collapseRegions`, `regionsIntersection`, `sizeOfOverlap`, `subtract`, `bucketIterator`, `distance`, `signedDistance`, `intersects`, and `transform_center`.

**Dependencies:**

*   Standard Python libraries: `sys`, `unittest`, `copy`, `heapq`.
*   `pyokit` library:
    *   `pyokit.common.pyokitError.PyokitError`
    *   `pyokit.util.progressIndicator.ProgressIndicator`
    *   `pyokit.datastruct.intervalTree.IntervalTree`
    *   `pyokit.util.meta.AutoApplyIterator`

**Key Design Choices & Important Details:**

*   **Coordinate System:** Genomic intervals are defined as **inclusive of start and exclusive of end** (0-based start, 1-based end), which is a common convention in bioinformatics (e.g., BED format).
*   **Sorting:** Many functions, particularly `collapseRegions`, `regionsIntersection`, and `bucketIterator`, explicitly **require input lists of genomic intervals to be sorted** by chromosome and then by start coordinate for efficient processing (often O(N) time complexity). Failure to provide sorted input will result in `GenomicIntervalError` exceptions.
*   **Strand Handling:** Some functions offer `stranded` options to perform operations considering DNA strand, but the `jaccardIndex`'s stranded mode is noted as "not implemented yet". `collapseRegions` differentiates between positive and negative strands when `stranded=True`.
*   **Immutability of Inputs (for some functions):** `collapseRegions` explicitly states that it returns "new objects, no existing object from s is returned or altered." This is a good practice to prevent side effects.
*   **Error Handling:** The module defines custom exception classes for specific error conditions (e.g., `InvalidStartIndexError`, `GenomicIntervalError` for sorting issues or invalid input).
*   **Performance:** The documentation notes O(N) time and space complexity for `collapseRegions` and `regionsIntersection`, highlighting efficiency. The `bucketIterator` leverages a min-heap for efficient management of overlapping elements.