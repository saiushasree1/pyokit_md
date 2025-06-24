The `conservationProfile.py` script is designed to compute and visualize conservation profiles around a set of genomic intervals. It takes as input a BED file containing genomic regions and a whole-genome alignment (either a single MAF file or a directory of MAF files). The core functionality is to calculate the "Percent Identity" (PID) for each position within a defined window around the specified genomic intervals and then average these PIDs across all intervals to create a conservation profile.

**Key Functionality:**

*   **Genomic Region Transformation:** It can transform input BED regions by centering them on their start, end, 5' end, 3' end, or geometric center, and then expanding them to a specified `window_size`.
*   **Conservation Score Calculation (PID):** The `pid` function calculates the percentage identity of an alignment column, defined as the frequency of the most frequent nucleotide. Gaps can optionally be ignored.
*   **Profile Generation:** The `conservtion_profile_pid` function builds a conservation profile for a given region using a genome alignment, returning a list of PIDs for each base within the region.
*   **Profile Averaging:** The `processBED` function iterates through the input BED regions, transforms them, generates individual conservation profiles, and then merges these profiles into a single averaged profile using `RollingMean` objects for each position in the window.
*   **Command Line Interface (CLI):** It provides a comprehensive command-line interface using the `pyokit.interface.cli` module, allowing users to specify input files, window size, centering options, treatment of missing sequences, target species, and output file.
*   **Genome Alignment Handling:** It supports loading genome alignments from either a single MAF file or a directory containing multiple MAF files, with optional index files for efficient "just-in-time" loading.

**Code Structure and Important Details:**

1.  **Imports:** The script imports standard Python modules like `os`, `sys`, `copy`, `unittest`, `StringIO`, `functools`, and `mock`. Crucially, it relies heavily on the `pyokit` library for I/O (BED iterators, genome alignment parsing), UI (CLI), data structures (genome alignment, sequence), and statistics (RollingMean).
2.  **Constants:** Defines constants for `WINDOW_CENTRE_OPTIONS` (START, END, FIVE_PRIME, THREE_PRIME, CENTRE) and default parameter values (`DEFAULT_WINDOW_SIZE`, `DEFAULT_VERBOSITY`, `DEFAULT_WINDOW_CENTRE`).
3.  **Helper Functions for Region Transformation:**
    *   `center_start`, `center_end`, `center_middle`: These functions (though `transform_locus` currently only implements `CENTRE`) are intended to adjust a genomic region based on a chosen centering point and window size.
    *   `transform_locus`: A wrapper function to apply the chosen centering transformation to a region.
4.  **Helper Functions for Profile Building:**
    *   `pid`: Computes the Percent Identity for a single alignment column. It raises a `ValueError` if the column contains only gaps and `ignore_gaps` is False.
    *   `conservtion_profile_pid`: Generates a list of PID scores for each position within a given genomic region using the provided `GenomeAlignment` object. It handles `NoSuchAlignmentColumnError` and `NoUniqueColumnError` by assigning `None` to the corresponding position.
    *   `merge_profile`: Adds a new profile (list of PIDs) to a list of `RollingMean` objects, effectively accumulating the mean for each position in the profile.
5.  **Main Script Logic (`processBED`):** This is the core function that orchestrates the profile generation. It initializes a list of `RollingMean` objects, iterates through BED entries, transforms each locus, computes its conservation profile, and merges it into the `mean_profile`.
6.  **User Interface (`getUI`):** Defines the command-line arguments and options using `pyokit.interface.cli.CLI`.
7.  **Command Line Processing and Dispatch (`main`):**
    *   Parses command-line arguments using the `CLI` object.
    *   Handles special options like `--help` and `--test`.
    *   Opens input/output files.
    *   Retrieves parameters such as `window_size`, `windowCentre`, `mi_seqs`, and `species`.
    *   Loads the genome alignment using `load_just_in_time_genome_alignment` (for directories) or `build_genome_alignment_from_file` (for single files).
    *   Calls `processBED` to get the conservation profile.
    *   Writes the resulting profile to the output stream.
8.  **Unit Tests:** The script includes a comprehensive set of unit tests using Python's `unittest` framework and `mock` for simulating file system interactions and file reads.
    *   `ConsProfileTestHelper`: A helper class providing sample MAF alignment blocks and BED regions for testing.
    *   `_build_index`, `_build_open_side_effect`, `_build_isfile_side_effect`, `_build_isdir_side_effect`: Helper functions for mocking file system operations during tests.
    *   `TestConservationProfileIndvFiles`: Tests for processing individual MAF files, including direct testing of `conservtion_profile_pid` and `processBED`, and an end-to-end test with the UI.
    *   `TestConservationProfileDirectory`: Specifically tests the functionality of handling a directory of MAF files with "just-in-time" alignment loading.

**Dependencies:**

*   `pyokit`: This is a critical dependency, providing core functionalities for genome alignment handling, BED file parsing, and command-line interface.

**Usage:**

The script is executed from the command line, requiring a BED file, a genome alignment file/directory, and a reference species. For example:

```bash
python conservationProfile.py regions.bed genome_alignment_file.maf ReferenceSpecies
```

Or for a directory of MAF files with indices:

```bash
python conservationProfile.py -e .maf -i .idx regions.bed alignment_directory ReferenceSpecies
```