The Python script `fastqNucDist.py` is designed to compute the nucleotide frequency at each position within a FASTQ file.

**Core Functionality:**

1.  **Command-Line Interface (CLI):** It uses the `pyokit.interface.cli` module to provide a user-friendly command-line interface. Users can specify an input FASTQ file, an optional output file (defaulting to stdout), and control verbosity. It also includes options to display help or run unit tests.
2.  **Nucleotide Counting (`count` function):**
    *   It iterates through reads in a FASTQ file using `pyokit.io.fastqIterators.fastqIterator`.
    *   For each read, it iterates through the nucleotides at each position.
    *   It maintains a list of dictionaries (`res`), where each dictionary corresponds to a position in the reads.
    *   Inside each position's dictionary, it stores the counts of each nucleotide ('A', 'C', 'G', 'T', or other characters).
    *   It handles variable read lengths by dynamically extending the `res` list as needed.
3.  **Output Generation (`write_output` function):**
    *   Takes the `counts` data structure (list of dictionaries) and an output file handle.
    *   Writes the nucleotide frequencies to the output in a tab-separated format: `position\tnucleotide\tcount`.
    *   It sorts the nucleotides for each position before writing to ensure consistent output order.
4.  **Unit Testing:**
    *   Includes a `unittest.TestCase` class `TestNucDist` to verify the core functionality.
    *   Uses `mock` to simulate file I/O, allowing tests to run without actual file system interactions.
    *   The `test_simple` method provides a basic test case with a hardcoded FASTQ input and verifies the expected nucleotide counts and output format.

**Structure and Flow:**

*   **Constants:** `DEFAULT_VERBOSITY` sets the default for verbose output.
*   **User Interface (`getUI`):** Defines the command-line options and parses them using `CLI`.
*   **Command Line Processing and Dispatch (`_main`):**
    *   The entry point for the main logic.
    *   Checks for special options like "test" (runs `unittest.main`) or "help" (displays usage).
    *   If no special options, it determines the verbosity, opens the input FASTQ file, and sets up the output file handle (stdout or a specified file).
    *   Calls `count` to perform the nucleotide counting and `write_output` to write the results.
*   **Main Program Logic (`count`, `write_output`):** Contains the core data processing functions.
*   **Unit Tests (`TestNucDist`):** Self-contained tests for verifying the program's correctness.
*   **Main Entry Point (`if __name__ == "__main__":`):**
    *   Calls `_main` with command-line arguments.
    *   Includes basic error handling for unexpected exceptions.

**Important Details:**

*   **Dependencies:** Relies on standard Python modules (`sys`, `unittest`, `StringIO`) and specific `pyokit` modules (`fastqIterator`, `CLI`, `Option`, `build_mock_open_side_effect`).
*   **File Handling:** Uses `open()` for input and output files. Output defaults to `sys.stdout`.
*   **Error Handling:** A generic `try-except` block is used in the main entry point to catch and report unexpected exceptions.
*   **License:** The code is distributed under the GNU General Public License v3 (or later).
*   **Input Format:** Expects a valid FASTQ file as input. `fastqIterator` is used to parse the reads. `allowNameMissmatch=True` is set in `fastqIterator`, which might be relevant for specific FASTQ formats where the name in the first line and third line (if present) can differ.