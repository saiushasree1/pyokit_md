The `sequences.rst` file is a reStructuredText document that serves as documentation for Pyokit's biological sequence handling capabilities.

**Overall Purpose:**

This document explains how Pyokit represents and processes biological sequences, specifically focusing on FASTA and FASTQ file formats. It highlights the core `Sequence` class and its specialized derivatives for different file types, along with iterators for efficient file parsing.

**Structure and Key Features:**

1.  **Biological Sequences Overview (`sequencesSection`):**
    *   Introduces the `Sequence` class as the fundamental representation for biological sequences in Pyokit.
    *   Mentions specializations for FASTA and FASTQ formats.
    *   Refers to other documentation sections (`sequenceClassSection`, `fastaSequenceClassSection`, `fastqSequenceClassSection`) for detailed class descriptions.

2.  **Processing FASTA Files:**
    *   Demonstrates the `fastaIterator` from `pyokit.io.fastaIterators`.
    *   Provides a practical example of iterating through a FASTA file (`sequences.fa`), performing a reverse complement on each sequence, and printing the result.
    *   Illustrates how the iterator handles multi-line sequence data and preserves line width in the output (with a minor note on ragged line widths).
    *   Includes the `autofunction` directive to link to the `fastaIterator`'s API documentation.

3.  **Processing FASTQ Files:**
    *   Explains that FASTQ processing is similar to FASTA but with more complexity due to potential multi-line sequence/quality data.
    *   Introduces three FASTQ iterators:
        *   `fastqIteratorSimple`: Assumes 4-line records (most common and recommended).
        *   `fastqIteratorComplex`: Handles multi-line sequence/quality data (slower, generally to be avoided).
        *   `fastqIterator`: A general wrapper, currently for `fastqIteratorSimple`.
    *   Presents an example similar to the FASTA one, demonstrating `fastqIterator` for reverse complementing sequences and showing that quality scores are also correctly reversed.
    *   Provides `autofunction` directives for `fastqIterator`, `fastqIteratorSimple`, and `fastqIteratorComplex`.

4.  **Manipulating Sequences:**
    *   Briefly directs the reader to the detailed class descriptions (`sequenceClassSection`, `fastaSequenceClassSection`, `fastqSequenceClassSection`) for information on sequence manipulation methods.

**Important Details for Readers:**

*   **`Sequence` Class:** This is the core object for sequence data.
*   **Iterators for Files:** Pyokit provides efficient iterators (`fastaIterator`, `fastqIterator`) for processing large sequence files line by line, which is memory-efficient.
*   **FASTA Line Handling:** The `fastaIterator` intelligently handles multi-line sequence data and tries to preserve the original line width in output.
*   **FASTQ Iterator Choices:** Be aware of the different FASTQ iterators. `fastqIterator` (or `fastqIteratorSimple`) is the recommended default, as multi-line FASTQ records are uncommon. Use `fastqIteratorComplex` only if strictly necessary for non-standard files.
*   **Quality Score Reversal:** When reverse complementing FASTQ sequences, the associated quality scores are also correctly reversed.
*   **Further Details:** For in-depth understanding of sequence methods, readers are pointed to dedicated class documentation sections.