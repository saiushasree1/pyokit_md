This reStructuredText document, `datastrct.rst`, serves as documentation for key data structures within the `pyokit` library. It outlines several fundamental bioinformatics-related classes, providing a high-level overview of their purpose and a reference to their detailed API documentation (via `autoclass` directives).

**Core Functionality and Structure:**

The document is organized into sections based on the type of biological data they represent:

1.  **Genomic Intervals:**
    *   Introduces the `GenomicInterval` class, used to represent genomic regions defined by chromosome, start (exclusive end), and an optional DNA strand, name, and score.
    *   References the `pyokit.datastruct.genomicInterval.GenomicInterval` class for detailed member information.

2.  **Interval Trees:**
    *   Describes `IntervalTree` as a binary tree structure enabling efficient lookup of intervals that intersect a given point or interval.
    *   References the `pyokit.datastruct.intervalTree.IntervalTree` class.

3.  **Sequences:**
    *   Explains the `Sequence` class as a general wrapper for sequence data, providing basic manipulation functionality.
    *   Highlights specializations for specific formats: `FastaSequence` and `FastqSequence`.
    *   References the `pyokit.datastruct.sequence.Sequence`, `pyokit.datastruct.sequence.FastaSequence`, and `pyokit.datastruct.sequence.FastqSequence` classes.

4.  **Sequence Alignments:**
    *   Briefly mentions `PairwiseAlignment` for representing sequence alignments.
    *   References the `pyokit.datastruct.multipleAlignment.PairwiseAlignment` class.

5.  **Gene Ontology:**
    *   Presents two classes for Gene Ontology (GO) data:
        *   `GeneOntologyTerm`: A basic data type for GO term information.
        *   `GeneOntologyEnrichmentResult`: A subclass of `GeneOntologyTerm` that includes additional data for term enrichment.
    *   References the `pyokit.datastruct.geneOntology.GeneOntologyTerm` and `pyokit.datastruct.geneOntology.GeneOntologyEnrichmentResult` classes.

**Important Details for Readers:**

*   **API Documentation:** The primary purpose of this document is to serve as a high-level table of contents for the `pyokit` data structures. Detailed information about class members (methods, attributes) is expected to be generated automatically by Sphinx using the `.. autoclass::` directives, pulling directly from the Python docstrings.
*   **Exclusive End Intervals:** For `GenomicInterval`, it's explicitly stated that the end index is exclusive, which is a common convention in bioinformatics but important to note.
*   **Default Strand:** The `GenomicInterval` defaults to the positive DNA strand if not explicitly set.
*   **Further Information:** Several sections include `::ref:` links (e.g., `:ref:genomicIntervalsSection`, `:ref:sequencesSection`), indicating that more detailed usage examples or conceptual explanations are available in other parts of the `pyokit` documentation.
*   **Supported Formats:** Currently, `fasta` and `fastq` are the supported sequence formats.

In essence, this document acts as an organized index and brief description of the core data structure classes provided by the `pyokit` library for handling common bioinformatics entities.