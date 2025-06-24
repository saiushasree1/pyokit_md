This reStructuredText document, "Manipulating Genomic Intervals," details how to work with genomic intervals using the `pyokit` library, specifically focusing on its `genomicInterval` module.

The document covers three main functionalities:

1.  **Collapsing Interval Sets**: It references the `pyokit.datastruct.genomicInterval.collapseRegions` function, which is used to merge overlapping or adjacent genomic intervals into a smaller set of non-overlapping intervals.

2.  **Intersecting Interval Sets**: It references the `pyokit.datastruct.genomicInterval.regionsIntersection` function, which likely finds the common regions between two sets of genomic intervals.

3.  **Searching Interval Sets**: This section provides more detailed explanations and examples for finding specific intervals.
    *   **Naive Search**: Demonstrates a simple O(n) approach using `BEDIterator` and the `intersects` method for a single pass search.
    *   **Bucket Iterator**: Introduces `pyokit.datastruct.genomicInterval.bucketIterator` as a more efficient solution (compared to O(n^2) nested loops) for making multiple passes, particularly when you want to find elements that intersect with "bucket" intervals. An extensive example is provided, defining two lists of `GenomicInterval` objects (`buckets` and `elements`) and showing how `bucketIterator` groups `elements` by their intersection with `buckets`. The expected output is also shown.
    *   **Interval Tree**: Presents an alternative, often more efficient, method for complex search scenarios using interval trees. It demonstrates how to achieve the same result as the `bucketIterator` example by first building interval trees from the `elements` list (using `intervalTreesFromList`) and then querying these trees with the `buckets`.

The document uses `autofunction` directives to link to the documentation of the mentioned Pyokit functions and includes Python `doctest`-style examples to illustrate usage. It highlights the `GenomicInterval` class and its methods, as well as the `BEDIterator` for reading genomic data.