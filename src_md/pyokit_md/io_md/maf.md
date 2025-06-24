The `maf.py` file provides classes and functions for representing and manipulating pairwise and multiple sequence alignments, specifically in the MAF (Multiple Alignment Format) file format.

**Core Functionality:**

*   **MAF Parsing (Reading):** The `maf_iterator` function is the primary mechanism for reading MAF files. It parses each "block" in a MAF file, which represents a multiple sequence alignment, and yields `MultipleSequenceAlignment` objects (or a user-specified class). It handles different MAF line types ('a', 's', 'i', 'e', 'q') to construct `Sequence` and `UnknownSequence` objects and populate their metadata.
*   **MAF Writing:**
    *   `sequence_to_maf_format`: Converts a `Sequence` or `UnknownSequence` object into its corresponding MAF 's', 'e', 'i', and 'q' line string representation.
    *   `alignment_to_maf`: Converts a `MultipleSequenceAlignment` object into a complete MAF block string, including the 'a' line and all associated sequence lines.
    *   `write_maf`: Takes an iterable of alignment objects and writes them sequentially to an output stream in MAF format.
*   **Data Structures:** It leverages custom `Sequence`, `UnknownSequence`, and `MultipleSequenceAlignment` classes (presumably from `pyokit.datastruct`).
*   **Metadata Handling:** It defines constants for specific metadata keys used within `Sequence` objects to store information from 'i' (context) and 'q' (quality) lines, as well as a special `SEQ_ORDER_KEY` to preserve the original sequence order within an alignment block.
*   **Error Handling:** It defines a `MAFError` exception class, inheriting from `PyokitIOError`, for issues encountered during MAF parsing or conversion.

**Structure and Key Details:**

*   **Constants:** A section dedicated to defining constants for MAF line types (`A_LINE`, `S_LINE`, etc.) and metadata keys (`QUALITY_META_KEY`, `LEFT_STATUS_KEY`, etc.).
*   **Exception Classes:** `MAFError` for MAF-specific exceptions.
*   **Helper Functions (Internal):**
    *   `merge_dictionaries`: A utility function to merge two dictionaries, with values from the second dictionary overriding duplicates from the first.
    *   `__build_sequence`: Parses an 's' line to create a `Sequence` object. It correctly handles sequence coordinates and strands.
    *   `__build_unknown_sequence`: Parses an 'e' line to create an `UnknownSequence` object, which represents non-aligning regions with status information.
    *   `__annotate_sequence_with_context`: Populates a `Sequence` object's metadata with context information from an 'i' line.
    *   `__annotate_sequence_with_quality`: Populates a `Sequence` object's metadata with quality scores from a 'q' line.
    *   These helper functions are prefixed with `__` indicating they are intended for internal use by the MAF iterator/writer.
*   **`maf_iterator` Function:**
    *   Handles opening files or accepting file-like objects (e.g., `StringIO`).
    *   Iterates line by line, identifies line types, and dispatches to appropriate helper functions for sequence and metadata construction.
    *   Collects sequences and block-level metadata (`a` line) until a new block starts or the file ends, then yields a `MultipleSequenceAlignment` object.
    *   Crucially, it stores the order of sequences in a block using `SEQ_ORDER_KEY` to allow faithful reconstruction during writing.
*   **Unit Tests (`TestMAF` class):**
    *   Comprehensive unit tests are included to verify the parsing and writing functionality.
    *   `setUp` method initializes example MAF blocks and corresponding expected `Sequence` and `UnknownSequence` objects.
    *   `test_maf_iterator`: Tests the parsing of a multi-block MAF string, verifying that the generated `MultipleSequenceAlignment` objects and their contained `Sequence` objects match expected values. It also tests round-trip conversion (read then write).
    *   `test_maf_comment_lines`: Ensures that comment lines at the beginning of a MAF file are correctly ignored.
*   **Dependencies:** Relies on `unittest` and `StringIO` from standard Python, and `PyokitIOError`, `Sequence`, `UnknownSequence`, and `MultipleSequenceAlignment` from the `pyokit` library.

**Important Considerations:**

*   **MAF Format Interpretation:** The code strictly adheres to a specific interpretation of the MAF format, particularly regarding coordinate systems (zero-based, handling of negative strands), and the meaning of 'i' and 'e' line status characters.
*   **Metadata Propagation:** Metadata from 'i' and 'q' lines is stored directly within the `meta_data` dictionary of individual `Sequence` objects, while 'a' line metadata is stored in the `meta_data` of the `MultipleSequenceAlignment` block.
*   **Modularity:** The code is well-structured with clear separation of concerns (parsing, building objects, writing, testing).
*   **Error Checking:** Includes checks for malformed MAF lines (e.g., mismatching sequence names in 'i'/'q' lines, incorrect number of fields).