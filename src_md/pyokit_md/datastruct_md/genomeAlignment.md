The `genomeAlignment.py` file provides classes and functionalities for representing and manipulating whole-genome alignments. It defines data structures for individual alignment blocks and aggregations of these blocks, including "just-in-time" loading mechanisms for efficiency with large datasets.

**Core Functionality:**

*   **Representing Genome Alignment Blocks:** The `GenomeAlignmentBlock` class extends `MultipleSequenceAlignment` to represent a contiguous segment of a reference genome aligned to one or more query genomes. It handles extracting specific columns (nucleotides) at given genomic positions.
*   **Aggregating Blocks into a Genome Alignment:** The `GenomeAlignment` class aggregates multiple `GenomeAlignmentBlock` objects. It uses `IntervalTree` data structures internally to efficiently retrieve blocks that overlap a specified genomic region. It can also extract a single alignment column at a given chromosomal position.
*   **Just-In-Time Loading:** `JustInTimeGenomeAlignmentBlock` and `JustInTimeGenomeAlignment` implement a lazy loading strategy. This means that the full alignment data is only loaded from a "factory" (e.g., an indexed file) when it's explicitly needed, improving performance and memory usage for very large alignments.
*   **Error Handling:** Custom exception classes (`GenomeAlignmentError`, `NoSuchAlignmentColumnError`, `NoUniqueColumnError`) are defined to provide specific error messages related to genome alignment operations.

**Structure and Key Details:**

*   **Exception Classes:**
    *   `GenomeAlignmentError`: Base class for all errors in this module.
    *   `NoSuchAlignmentColumnError`: Raised when a requested column is not defined in the alignment.
    *   `NoUniqueColumnError`: Raised when a requested column is ambiguously defined by multiple overlapping blocks.
*   **Helper Functions:**
    *   `_build_trees_by_chrom`: A private helper function used to organize `GenomeAlignmentBlock` objects into `IntervalTree`s, keyed by chromosome, for efficient spatial querying.
*   **`GenomeAlignmentBlock` Class:**
    *   Inherits from `pyokit.datastruct.multipleAlignment.MultipleSequenceAlignment`.
    *   Requires a `reference_species` name (e.g., "hg19") and expects reference sequences to be named `assembly.chrom` (e.g., "hg19.chr1").
    *   Properties `chrom`, `start`, and `end` provide genomic coordinates relative to the reference sequence.
    *   `get_column_absolute(position)`: Retrieves an alignment column at a specific absolute genomic `position` on the reference.
*   **`JustInTimeGenomeAlignmentBlock` Class:**
    *   Decorated with `@decorate_all_methods(just_in_time_method)`, indicating that most method calls will trigger loading the actual block data via a provided `factory`.
    *   Stores `_chrom`, `_start`, and `_end` from the `key` initially, allowing property access without immediate loading.
    *   Includes static methods `build_hash`, `build_hash_raw`, and `parse_hash` for creating and parsing unique string keys that identify a block's location.
    *   Raises `IndexError` if the loaded block's coordinates do not match the `key`'s coordinates.
*   **`GenomeAlignment` Class:**
    *   Constructor takes an iterable of `GenomeAlignmentBlock` objects.
    *   `block_trees`: A dictionary of `IntervalTree`s, one per chromosome, storing the alignment blocks for efficient lookup.
    *   `get_blocks(chrom, start, end)`: Returns all blocks that overlap the given genomic interval.
    *   `get_column(chrom, position)`: Retrieves a single alignment column. It ensures uniqueness and raises errors if the column is undefined or ambiguous.
*   **`JustInTimeGenomeAlignment` Class:**
    *   Manages a collection of alignment blocks that are potentially stored across multiple files/sources and loaded on demand.
    *   Takes `whole_chrom_files` (for full chromosome alignments) and `partial_chrom_files` (for specific regions) as dictionaries, where values are keys for a `factory`.
    *   `__switch_alig(key)`: Handles the actual loading of an alignment object using the provided `factory` if it's not already in memory.
    *   `get_blocks` is overridden to combine results from both whole and partial chromosome files, loading only necessary parts.
*   **Unit Tests (`TestGenomeAlignmentDS`):**
    *   Comprehensive test suite covering the functionality of `GenomeAlignmentBlock`, `GenomeAlignment`, `JustInTimeGenomeAlignmentBlock`, and `JustInTimeGenomeAlignment`.
    *   Tests include basic column retrieval, handling out-of-bounds requests, ambiguous columns, and the lazy loading mechanism of JIT classes.

**Dependencies:**

The code relies on external libraries from the `pyokit` package:
*   `pyokit.datastruct.multipleAlignment`: For `MultipleSequenceAlignment` and `MissingSequenceHandler`.
*   `pyokit.datastruct.intervalTree`: For efficient spatial querying of genomic intervals.
*   `pyokit.util.meta`: For decorators like `decorate_all_methods` and `just_in_time_method` to enable lazy loading.
*   `pyokit.datastruct.sequence`: For the `Sequence` class.
*   `pyokit.datastruct.genomicInterval`: For the `GenomicInterval` class.

**Usage:**

This module would typically be used in bioinformatics applications to work with whole-genome alignment data, allowing for efficient access to specific regions or columns within the alignment without necessarily loading the entire dataset into memory. The "just-in-time" features are crucial for handling large reference genomes and numerous query genomes.