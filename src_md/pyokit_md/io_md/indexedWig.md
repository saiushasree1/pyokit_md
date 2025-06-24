The provided Python code implements an `IndexedWig` class designed for efficient random access to large Wig (Wiggle) format files. Wig files typically store genomic interval data with associated scores. The core idea is to avoid loading the entire file into memory by building an index and using a cache for frequently accessed data blocks.

**Core Functionality:**

*   **Random Access:** Allows querying the Wig file for a specific genomic coordinate (chromosome and position) to retrieve the corresponding Wig element.
*   **Memory Efficiency:** Achieves memory efficiency by dividing the Wig file into "blocks" and only loading the necessary blocks into memory when requested.
*   **Caching:** Implements a Least Recently Used (LRU) like cache (using a min-heap based on `time.time`) to store a limited number of recently accessed Wig blocks in memory, improving performance for queries exhibiting locality of reference.
*   **Index Building:** During initialization, it reads the entire Wig file to build an index. This index is a collection of `WigBlock` objects, each representing a segment of the Wig file. `IntervalTree` structures are used to efficiently find the correct `WigBlock` for a given genomic coordinate.
*   **Strict Sorting Requirement:** The Wig file *must* be sorted by chromosome and then by start position. The code explicitly checks for this and raises an `IndexedWigError` if the file is not sorted, which is crucial for the indexing and lookup mechanisms to work correctly.

**Code Structure and Key Classes:**

1.  **`IndexedWigError(Exception)`:** A custom exception class used to signal errors specific to the `IndexedWig` operations, primarily related to file format issues (e.g., unsorted file, invalid lookups).

2.  **`WigBlock(object)`:**
    *   Represents a contiguous block of Wig entries within the file.
    *   Stores its `fileLocation` (byte offset in the file), `chrom`, `start`, `end` (genomic coordinates), and `capacity` (maximum number of Wig elements it can hold).
    *   `populate(filehandle)`: Loads the actual Wig elements for this block from the file into memory. It then constructs an `IntervalTree` (`self.iTree`) from these elements, enabling efficient internal lookups within the block.
    *   `depopulate()`: Clears the in-memory data for the block, freeing up memory.
    *   `lookup(chrom, point)`: Performs a lookup within this specific block to find the Wig element intersecting the given point.
    *   `contains(chrom, point)`: Checks if the block contains a Wig element intersecting the given point.
    *   `isfull()`: Indicates if the block has reached its element capacity.

3.  **`IndexedWig(object)`:**
    *   **Constructor (`__init__`)**:
        *   Takes `filename` (or file handle), `blocksize` (elements per block), `blockCapacity` (max blocks in cache), `debug`, and `verbose` flags.
        *   Initializes `populatedBlocks` (a min-heap used for the LRU cache), `blocksByChrom` (a dictionary mapping chromosomes to lists of `WigBlock` objects), and `itrees` (a dictionary mapping chromosomes to `IntervalTree` instances, where each `IntervalTree` stores `WigBlock` objects for that chromosome).
        *   Calls `build()` to create the initial index.
    *   **`build()`**:
        *   Iterates through the input Wig file line by line.
        *   Parses each line into a Wig element (`e`).
        *   Crucially, it checks if the file is sorted by chromosome and then by position; if not, it raises `IndexedWigError`.
        *   It groups Wig elements into `WigBlock` objects based on `blocksize` and chromosome.
        *   Records the file offset (`at`) for each block's start.
        *   After processing the entire file, it builds an `IntervalTree` for each chromosome, where the nodes of the tree are the `WigBlock` objects. This allows rapid identification of the correct block(s) for a given genomic coordinate.
        *   Includes a `ProgressIndicator` for verbose mode.
    *   **`contains(chrom, point)`**:
        *   Uses the `IntervalTree` for the given `chrom` to find the relevant `WigBlock` that might contain the `point`.
        *   If a block is found and not yet populated (i.e., its data is not in memory), it `populate`s the block.
        *   Manages the `populatedBlocks` cache: if the cache is full, it `depopulate`s the least recently used block (determined by `time.time` stored in a min-heap) before populating the new one.
        *   Finally, it calls the `contains` method on the retrieved `WigBlock`.
    *   **`lookup(chrom, point)`**: Similar to `contains`, but retrieves the actual `WigElement` intersecting the point, managing block population and cache eviction.

**Important Details & Considerations:**

*   **Assumptions:** The primary assumption for good performance is "locality of reference," meaning subsequent queries are likely to be near previous ones. Complete random access *will* be slow as it might force a file seek and block load for every lookup.
*   **File Sorting:** The requirement for a sorted input Wig file is fundamental. If the file is not sorted, `IndexedWigError` will be raised during the `build` phase.
*   **Dependencies:**
    *   `pyokit.testing.dummyfiles`: For `DummyInputStream`, used in unit tests.
    *   `pyokit.datastruct.genomicInterval`: For `parseWigString`, likely a utility to parse Wig lines into structured objects.
    *   `pyokit.datastruct.intervalTree`: A custom `IntervalTree` implementation, crucial for efficient block lookup.
    *   `pyokit.util.progressIndicator`: For displaying progress during index building.
*   **Caching Strategy:** A simple LRU-like cache is implemented using a min-heap where elements are `(timestamp, block_object)`. When the cache reaches `blockCapacity`, the block with the smallest timestamp (oldest) is removed.
*   **Error Handling:** Extensive use of `IndexedWigError` for various invalid scenarios, such as unsorted files, lookups for non-existent chromosomes, or multiple Wig entries intersecting the same point (which usually indicates a malformed Wig file or unexpected overlap).
*   **Unit Tests (`TestIndexedWig`)**: Basic unit tests are provided to verify correct data recall and the `OutOfOrder` exception handling.

In summary, `IndexedWig.py` provides a robust and memory-efficient solution for accessing large Wig files by leveraging indexing and caching, with a strict requirement for sorted input data.