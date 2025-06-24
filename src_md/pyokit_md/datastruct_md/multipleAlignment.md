The provided Python code, `multipleAlignment.py`, defines classes for representing and manipulating sequence alignments, specifically `MultipleSequenceAlignment` and `PairwiseAlignment`. It also includes an exception hierarchy for handling alignment-related errors and a `JustInTimePairwiseAlignment` class for lazy loading. A comprehensive set of unit tests using `unittest` is provided to ensure the correctness of the alignment operations.

**Core Functionality:**

*   **`MultipleSequenceAlignment`**: This class handles alignments of two or more sequences.
    *   It stores sequences internally in a dictionary, indexed by sequence name.
    *   It enforces that all sequences in an alignment must have the same length (after considering gaps).
    *   Provides methods to:
        *   Access individual sequences by name (`__getitem__`).
        *   Retrieve a specific column of the alignment (`get_column`), with options for handling missing sequence data.
        *   Convert coordinates from alignment-specific positions to a specific sequence's original coordinates (`alignment_to_sequence_coords`).
        *   Convert coordinates from a specific sequence's original coordinates to alignment-specific positions (`sequence_to_alignment_coords`).
        *   "Liftover" an interval from one sequence to another within the alignment (`liftover`).
        *   Provide basic information like the total alignment length (`size`) and number of sequences (`num_seqs`).
*   **`PairwiseAlignment`**: This class inherits from `MultipleSequenceAlignment` and specifically handles alignments of exactly two sequences. It provides convenient properties to access the two sequences as `s1` and `s2`.
*   **`JustInTimePairwiseAlignment`**: This class extends `PairwiseAlignment` and implements a "just-in-time" loading mechanism. It's designed to load alignment data only when it's accessed, typically from a factory object (like an `IndexedFile`). This is useful for handling very large sets of alignments efficiently without loading all of them into memory at once.

**Structure and Key Details:**

*   **Exception Handling**:
    *   `MultipleAlignmentError` (base class for alignment-related errors).
    *   `InvalidSequenceError`: Raised when a requested sequence is not found in the alignment.
    *   `InvalidAlignmentCoordinatesError`: Raised when provided coordinates are out of bounds or invalid for the alignment.
    *   These inherit from `pyokit.common.pyokitError.PyokitError`.
*   **Enumerations**: `MissingSequenceHandler` (an `Enum`) defines how `get_column` should treat sequences that might not have data for a given column (skip them or treat them as all gaps).
*   **Coordinate Systems**:
    *   Both alignment and sequence coordinates are **one-based**.
    *   Intervals are inclusive of the start but exclusive of the end (e.g., `[start, end)`).
    *   This is explicitly mentioned in the docstrings for `alignment_to_sequence_coords` and `sequence_to_alignment_coords`.
*   **Dependencies**:
    *   `unittest` for testing.
    *   `enum` (backported for older Python versions).
    *   Classes from `pyokit.datastruct.sequence` (like `Sequence`, `UnknownSequence`, `GAP_CHAR`, `InvalidSequenceCoordinatesError`).
    *   Utility decorators from `pyokit.util.meta` (`decorate_all_methods_and_properties`, `just_in_time_method`, `just_in_time_property`) for the lazy loading functionality.
    *   `pyokit.common.pyokitError.PyokitError` for custom exceptions.
*   **Unit Tests**: The `TestAlignments` class thoroughly tests various functionalities, including:
    *   Calculating ungapped lengths.
    *   Extracting columns.
    *   Converting coordinates between sequence and alignment spaces (both ways), including edge cases like trimming, out-of-bounds coordinates, and handling gap-only regions.
    *   Liftover operations between sequences in a pairwise alignment.
    *   Error handling for invalid input (e.g., sequences of different lengths, non-existent sequences, invalid coordinates).
    *   Behavior with `UnknownSequence` objects.

**Important Details for Readers:**

*   **One-Based Indexing**: Be mindful that both alignment and sequence coordinates use one-based indexing, which might differ from typical Python zero-based indexing.
*   **Interval Conventions**: Intervals are `[start, end)`, meaning `start` is inclusive and `end` is exclusive.
*   **Gap Handling**: The `GAP_CHAR` constant (imported from `pyokit.datastruct.sequence`) is used to denote gaps within sequences.
*   **`pyokit` Dependency**: The code relies heavily on the `pyokit` library for basic sequence objects and utility functions. Without `pyokit` installed, this code will not run.
*   **Just-In-Time Loading**: The `JustInTimePairwiseAlignment` class is a design pattern for memory efficiency, deferring the actual loading of alignment data until it's specifically needed. This requires a `factory` object that can provide the alignment based on a `key`.