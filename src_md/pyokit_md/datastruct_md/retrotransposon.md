The `retrotransposon.py` script defines Python classes and functions for handling and manipulating data related to retrotransposon occurrences in a genome, particularly those parsed from RepeatMasker output.

**Core Functionality:**

*   **Data Representation:**
    *   `Retrotransposon`: A simple class to store the name and family name of a retrotransposon, with an optional consensus sequence.
    *   `RetrotransposonOccurrence`: Extends `pyokit.datastruct.genomicInterval.GenomicInterval` to represent a specific instance of a retrotransposon in a genome. It includes detailed information about the genomic location, the strand of the consensus sequence it matches, the start and end coordinates within the consensus sequence, the total consensus sequence length, and a reference to the `Retrotransposon` object. It also has a placeholder for a `pairwise_alignment` and a `uniq_id`.

*   **RepeatMasker Parsing:**
    *   `from_repeat_masker_string(s)`: This crucial function parses a string in RepeatMasker output format (a common tool for identifying repetitive elements in DNA sequences) into a `RetrotransposonOccurrence` object. It handles the complex, 15-column, white-space separated format, including variations for positive and negative strand matches and optional fields. It converts all coordinates to positive strand and ensures `start < end` for the genomic interval.

*   **Coordinate Liftover:**
    *   `liftover(intersecting_region)`: This method is designed to translate genomic coordinates of a region that overlaps a retrotransposon occurrence to the corresponding coordinates within the retrotransposon's consensus sequence.
    *   It prioritizes using a `pairwise_alignment` if available for exact coordinate translation.
    *   If no explicit alignment is provided, it falls back to `liftover_coordinates`, which uses a heuristic approach:
        *   If the genomic match length equals the consensus match length, a direct linear mapping is used.
        *   If the lengths differ (implying insertions or deletions), it assumes uniform distribution of indels and attempts to adjust coordinates accordingly. This can lead to fragmented intervals if genomic deletions are inferred.
    *   `__liftover_coordinates_size_match`, `__liftover_coordinates_genomic_indels`, `__liftover_coordinates_genomic_insertions`, and `__liftover_coordinates_genomic_deletions` are private helper methods that implement the specific liftover logic based on indel assumptions and match strand.

**Structure and Important Details:**

*   **Dependencies:** Relies on `unittest` and `math` from standard Python, and `GenomicInterval` from `pyokit.datastruct.genomicInterval`.
*   **Exception Handling:** Defines a custom `RetrotransposonError` for specific errors related to retrotransposon data manipulation.
*   **Module-Wide Constants:** `COMPLEMENT_CHARS` is a small set used for determining the strand from RepeatMasker output.
*   **Coordinate System:** The code emphasizes converting all genomic intervals to positive strand coordinates (`start < end`). RepeatMasker's `query seq. remaining` field and consensus match coordinates are handled with care to achieve this consistency, particularly for negative strand matches.
*   **Indel Handling in Liftover (Heuristic):** When no explicit pairwise alignment is present, the `liftover_coordinates` methods make assumptions about the uniform distribution of insertions or deletions. This is a crucial detail, as these assumptions might not perfectly reflect biological reality, and users should be aware that coordinate translation without an explicit alignment is an approximation. Genomic deletions, in particular, can result in a list of multiple `GenomicInterval` objects.
*   **Unit Tests:** The `TestRetrotransposon` class provides comprehensive unit tests for the parsing and liftover functionalities, covering various scenarios including different strands, presence/absence of indels, and boundary conditions. This is vital for ensuring the correctness of the complex coordinate transformations.
*   **Docstrings:** The code is well-documented with detailed docstrings explaining the purpose of classes, functions, and parameters, especially for the `from_repeat_masker_string` function, which explains the RepeatMasker format.

In summary, `retrotransposon.py` is a specialized Python module for handling retrotransposon data, primarily focusing on parsing RepeatMasker output and providing functionalities to "lift over" genomic coordinates into the context of the retrotransposon's consensus sequence, with robust handling for different match strands and inferred indels.