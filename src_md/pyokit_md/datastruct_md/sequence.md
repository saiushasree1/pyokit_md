The `sequence.py` file defines a foundational `Sequence` class in Python for handling biological sequence data (DNA or RNA). It provides functionalities for creating, manipulating, and querying sequences, including operations like reverse complementing, subsequence extraction, masking, and calculating nucleotide percentages. The module also defines custom exception classes for sequence-related errors and includes a `UnknownSequence` subclass for cases where sequence data is absent but metadata exists. Unit tests are provided to ensure the correctness of the `Sequence` class methods.

**Core Functionality:**

*   **Sequence Representation:** The `Sequence` class stores a sequence name, the actual nucleotide sequence data (either as a standard Python string or a `MutableString` for performance in editing operations), start and end coordinates, strand information, remaining sequence length, and arbitrary metadata.
*   **Sequence Manipulation:**
    *   `copy()`: Creates a deep copy of the sequence object.
    *   `reverseComplement()`: Generates the reverse complement of the sequence, with options for DNA or RNA.
    *   `nsLeft()`, `nsRight()`: Replaces a specified number of bases from the left or right end with 'N' (unknown nucleotide).
    *   `maskRegion()`, `maskRegions()`: Replaces nucleotides within specified regions with 'N's.
    *   `toRNA()`, `toDNA()`: Converts the sequence in-place between DNA (T) and RNA (U).
    *   `split()`: Splits the sequence into two halves.
    *   `truncate()`: Creates a new sequence truncated to a specified length.
    *   `clip_end()`: Clips a matching sequence from the end of the current sequence.
*   **Sequence Querying:**
    *   `__len__()`: Returns the total length of the sequence data, including gaps.
    *   `ungapped_len` (property): Returns the length of the sequence excluding gap characters ('-').
    *   `effective_len` (property): Returns the length of the sequence excluding 'N' characters.
    *   `start` (property): Returns the 1-based start coordinate of the sequence.
    *   `end` (property): Returns the 1-based end coordinate of the sequence (exclusive).
    *   `subsequence()`, `relative_subsequence()`, `gapped_relative_subsequence()`: Extracts subsequences based on absolute, relative ungapped, or relative gapped coordinates.
    *   `isDNA()`, `isRNA()`: Guesses if the sequence is DNA or RNA based on nucleotide content.
    *   `percentNuc()`: Calculates the percentage of a specific nucleotide.
    *   `similarity()`: Computes the number of matching bases between two subsequences.
    *   `isPolyA()`, `isPolyT()`, `isLowQuality()`: Checks for specific sequence characteristics (e.g., poly-A tail, low quality based on 'N' content).
    *   `maskMatch()`: Determines if the sequence matches a given mask string, where 'N' in the mask matches any nucleotide.
*   **String Formatting:**
    *   `__str__()`: Returns the FASTA string representation of the sequence.
    *   `to_fasta_str()`: Explicitly generates a FASTA formatted string, including options for line width and coordinate inclusion.
    *   `meta_data_to_string()`: Formats the metadata dictionary into a string.

**Structure and Design:**

*   **Constants:** Defines module-level constants for DNA/RNA complements, gap characters, unknown sequence names, and valid DNA/RNA nucleotides.
*   **Exception Classes:**
    *   `SequenceError`: A general base class for errors during sequence manipulation.
    *   `InvalidSequenceCoordinatesError`: Specifically for errors related to invalid sequence indexing.
*   **`Sequence` Class:** The core class, inheriting from `object`. It uses properties for `start`, `end`, `ungapped_len`, and `effective_len` for computed or just-in-time values.
*   **`UnknownSequence` Class:** A subclass of `Sequence` that represents sequences where the actual nucleotide data is not available. Most methods that require accessing sequence data will raise a `SequenceError`.
*   **Unit Tests:** A `unittest.TestCase` class (`SequenceUnitTests`) provides comprehensive tests for various functionalities of the `Sequence` class.

**Important Details:**

*   **Coordinate System:** Pyokit's sequence indexing, especially for `start` and `end` coordinates, is 1-based, and `end` coordinates are generally exclusive (similar to Python slicing behavior where the end index is not included). This is crucial for correctly interpreting subsequence extractions.
*   **Mutable String Option:** The `useMutableString` parameter in the `Sequence` constructor allows storing sequence data as a `MutableString`. This can improve performance for in-place editing operations but prevents the `Sequence` object from being used as a dictionary key or in sets due to its mutability.
*   **Gap Character:** The `GAP_CHAR` is defined as `"-"`, which is used in `ungapped_len` calculations and in `gapped_relative_subsequence`.
*   **Guessed RNA/DNA:** The `reverseComplement()`, `isDNA()`, and `isRNA()` methods attempt to guess the sequence type (DNA or RNA) if not explicitly provided, based on the presence of 'U' or 'T'.
*   **Licensing:** The code is explicitly licensed under the GNU General Public License (GPL) version 3 or any later version, indicating it is free software.