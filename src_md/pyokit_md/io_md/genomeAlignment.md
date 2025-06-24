The `genomeAlignment.py` file is designed for reading and writing genome alignments, specifically focusing on MAF (Multiple Alignment Format) files. It provides functionalities to load alignment data from single files or entire directories, with support for "just-in-time" loading using index files to optimize memory usage.

**Core Functionality:**

*   **MAF Parsing:** It can parse MAF files and represent each alignment block as a `GenomeAlignmentBlock` object.
*   **Reference Species:** All operations are performed with respect to a designated "reference species" within the alignment.
*   **Bulk Loading:**
    *   `build_genome_alignment_from_directory`: Loads all MAF files from a specified directory into a `GenomeAlignment` object.
    *   `build_genome_alignment_from_file`: Loads a single MAF file into a `GenomeAlignment` object.
*   **Just-In-Time (JIT) Loading:**
    *   `load_just_in_time_genome_alignment`: Loads alignment data from a directory in a JIT manner. This means that individual alignment blocks are only read from disk when they are explicitly requested, making it suitable for very large datasets.
    *   This JIT loading leverages index files (`.idx` extension) to quickly locate specific alignment blocks without reading the entire MAF file.
*   **Iterator-based Processing:** `genome_alignment_iterator` provides an efficient way to iterate over alignment blocks in a MAF file, supporting both immediate loading and "index-friendly" mode for building indexes.

**Structure and Key Components:**

*   **Helper Functions (`__trim_extension_dot`, `__trim_extensions_dot`, `__split_genomic_interval_filename`, `__find_index`):** These private utility functions handle string manipulation for file extensions and parsing genomic interval filenames (e.g., "chrom:start-end.ext").
*   **`WrappedStringIO` Class:** A custom wrapper around `StringIO` used primarily for unit testing to track the number of read operations, ensuring that JIT loading indeed defers disk reads.
*   **`GATestHelper` Class:** Contains predefined MAF block strings and their expected `Sequence` object representations, serving as test data for the unit tests.
*   **Unit Tests (`TestGenomeAlignment`):** A comprehensive suite of `unittest` cases covering:
    *   Basic MAF iteration and parsing (`test_genome_alignment_iterator`).
    *   Loading from directories (`test_build_genome_alignment_from_directory`).
    *   Loading from single files (`test_build_genome_alignment_from_file`).
    *   Loading with index files and verifying JIT behavior (`test_build_genome_alignment_from_indexed_file`, `test_just_in_time_genome_alig`).
    *   The tests extensively use `mock` to simulate file system operations and `__builtin__.open` to control file content during testing.

**Important Details and Considerations:**

*   **Dependencies:** The code relies heavily on custom data structures and I/O utilities from the `pyokit` library, specifically `pyokit.datastruct.genomeAlignment` (for `GenomeAlignmentBlock`, `JustInTimeGenomeAlignmentBlock`, `JustInTimeGenomeAlignment`, `GenomeAlignment`), `pyokit.datastruct.sequence` (for `Sequence`, `UnknownSequence`), `pyokit.io.maf`, `pyokit.io.indexedFile`, `pyokit.util.progressIndicator`, and `pyokit.io.ioError`. Without these, the code cannot function.
*   **File Naming Convention:** For JIT loading, the code expects alignment files in a directory to follow a specific naming convention: `chrom:start-end.ext` for partial chromosome alignments or `chrom.ext` for whole chromosome alignments.
*   **Index Files:** JIT loading relies on separate index files (e.g., `.idx`) associated with each MAF file. The `__find_index` function is responsible for locating these.
*   **Error Handling:** It uses `PyokitIOError` and `ValueError` for specific error conditions, such as missing index files or invalid filenames.
*   **GNU General Public License (GPLv3):** The code is distributed under the GPLv3, meaning it is free software that can be redistributed and modified under the terms of the license.
*   **Progress Indicator:** The `build_genome_alignment_from_file` function includes a `ProgressIndicator` when loading from an indexed file in verbose mode, providing feedback to the user during potentially long operations.