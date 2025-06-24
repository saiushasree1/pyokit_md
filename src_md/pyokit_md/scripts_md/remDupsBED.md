The Python script `remDupsBED.py` is an XPIPE component designed to filter mapped reads from two input BED files. Its primary function is to remove "ambiguous" reads, specifically those that appear in both input files (e.g., mapped in exonic and junction regions).

**Core Functionality:**

*   **Read Filtering:** The script takes two BED-formatted input files and two corresponding output files. It iterates through the reads in both input files, identifying and removing duplicate reads (based on read name, and potentially other criteria if "best" mode is enabled). Reads that are unique to one of the input files are written to their respective output file.
*   **Ambiguous Read Resolution (Optional):** When the `--best` option is used, the script attempts to resolve ambiguous reads (reads with the same name found in both inputs) by keeping the one with a "better" score. The definition of "better" score is implicitly handled by the `BEDUniqueIterator`.
*   **Command-Line Interface (CLI):** It provides a robust command-line interface for specifying input and output files, controlling verbosity, and enabling the "best match" filtering.
*   **Unit Testing:** The script includes a `unittest` suite to ensure its core logic (`ambigFilter`) functions correctly.

**Structure:**

1.  **Header and Copyright:** Standard Python shebang, creation date, XPIPE component description, and GNU General Public License information.
2.  **Imports:** Imports necessary modules from `sys`, `unittest`, `StringIO`, `mock` (for testing), and custom `pyokit` libraries for CLI, BED iteration, and testing utilities.
3.  **Constants:** Defines `MAX_NEEDED_BED_FIELDS` and `DEFAULT_VERBOSITY`.
4.  **`getUI(prog_name, args)`:**
    *   Initializes and configures a `CLI` object from `pyokit.interface.cli`.
    *   Defines long and short descriptions for the tool.
    *   Adds various command-line options:
        *   `-f`/`--first`: Required, path to the first input BED file.
        *   `-s`/`--second`: Required, path to the second input BED file.
        *   `-o`/`--output`: Required, whitespace-separated list of two output filenames.
        *   `-v`/`--verbose`: Optional, enables verbose output.
        *   `-b`/`--best`: Optional, enables filtering to keep the "best" ambiguously mapped read.
        *   `-h`/`--help`: Special, displays help message.
        *   `-u`/`--test`: Special, runs unit tests.
    *   Parses command-line arguments and returns the configured `CLI` object.
5.  **`_main(args, prog_name)`:**
    *   Serves as the main entry point for script execution.
    *   Calls `getUI` to parse arguments.
    *   Handles special options (`--help` and `--test`).
    *   Opens input and output file handles based on user-provided filenames.
    *   Extracts the `best` option.
    *   Calls `ambigFilter` to perform the core filtering logic.
6.  **`ambigFilter(in_fh1, in_fh2, out_fh1, out_fh2, verbose=False, best=False)`:**
    *   The core logic of the script.
    *   Takes two input file handles, two output file handles, and flags for verbosity and "best" filtering.
    *   Uses `BEDUniqueIterator` from `pyokit.io.bedIterators` to iterate through unique reads from both input streams. This iterator is responsible for identifying duplicates and, if `best` is true, selecting the "better" read. `dropAfter=6` likely indicates that only the first 6 fields of the BED record are considered for uniqueness by the iterator.
    *   Writes non-`None` reads returned by the iterator to their respective output files.
7.  **`TestRemDupsBed(unittest.TestCase)`:**
    *   A class containing unit tests for the `ambigFilter` function.
    *   `setUp()`: Initializes string versions of sample BED files (`sfile1` through `sfile4`) and a mapping for `mock_open`.
    *   `test_basic()`: Tests the default filtering behavior (removing exact duplicates). It uses `mock.patch` to simulate file operations and `StringIO` to capture output for assertion.
    *   `test_best_match()`: Tests the `--best` option, verifying that when reads with the same name are present, the one with the better score (as handled by `BEDUniqueIterator`) is retained.
8.  **Main Entry Point (`if __name__ == "__main__":`)**
    *   Calls `_main` with command-line arguments, wrapping it in a `try-except` block to catch and report unexpected exceptions.

**Important Details:**

*   **BED Format:** The script assumes input files are in BED format. It explicitly states a `MAX_NEEDED_BED_FIELDS` of 6, implying that fields beyond the 6th are not considered for uniqueness or are dropped by the `BEDUniqueIterator`.
*   **`pyokit` Dependency:** This script relies heavily on the `pyokit` library, particularly `CLI` for command-line parsing and `BEDUniqueIterator` for the core read comparison and filtering logic. The exact behavior of `BEDUniqueIterator` (how it defines uniqueness and "best" when scores are present) is crucial but abstracted.
*   **Error Handling:** Basic error handling is present for incorrect number of output filenames and a general exception catch in `_main`.
*   **License:** The script is released under the GNU General Public License, version 3 or later.
*   **Output Format:** The output files are also in BED format, with reads written as `str(r)` followed by a newline.