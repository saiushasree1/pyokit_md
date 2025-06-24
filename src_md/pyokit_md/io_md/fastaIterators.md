The file `fastaIterators.py` provides functions and iterators for parsing and processing FASTA-formatted sequence files.

**Core Functionality:**

*   **`fastaIterator(fn, useMutableString=False, verbose=False)`**: This is the primary generator function. It takes a filename (string) or a file-like object and yields `Sequence` objects (from `pyokit.datastruct.sequence`) representing each sequence in the FASTA file.
    *   It can optionally construct sequences using mutable strings for potentially more efficient editing (`useMutableString`).
    *   It supports verbose output to `sys.stderr` to show progress during parsing, calculating progress based on file size. If the size of the input stream cannot be determined (e.g., standard input), it gracefully degrades by suppressing progress.
*   **`num_sequences(fileh)`**: This helper function determines the total number of sequences present in a given FASTA file or stream by iterating through it.
*   **`_isSequenceHead(line)`**: A private helper function that checks if a given line starts with `>` (the FASTA header indicator), determining if it's a sequence header.

**Structure and Design:**

*   **Exception Handling**: It defines a custom exception `FastaFileFormatError` to specifically handle issues related to invalid FASTA file formatting, particularly when a non-header line is encountered where a header is expected.
*   **Internal Helpers**:
    *   `__read_seq_header(fh, prev_line)`: Reads the next FASTA header line from the stream, handling initial empty lines and validating the previous line if provided.
    *   `__read_seq_data(fh)`: Reads the sequence data associated with a header until the next header or end of file is encountered.
    *   `__build_progress_indicator(fh)`: Tries to create a `ProgressIndicator` object based on the file size for verbose output. It handles cases where file size cannot be determined for a given stream.
*   **Dependencies**:
    *   Relies on standard Python modules like `sys`, `os`, `math`, `unittest`, `StringIO`.
    *   Crucially depends on `pyokit` library components: `Sequence` for representing biological sequences, `ProgressIndicator` for progress reporting, and `PyokitError` as a base for its custom exception.
*   **Unit Tests**: Includes a comprehensive `TestFastaIterators` class using `unittest` to verify the functionality of `num_sequences` and `fastaIterator`. It also uses `mock` to test progress reporting in various scenarios, including file-based and stream-based inputs, and error handling for unknown stream lengths.

**Important Details:**

*   **FASTA Format Assumptions**: The code strictly expects FASTA header lines to begin with `>`. Leading whitespace before `>` will cause it to fail to recognize a header. Newline characters at the end of lines are tolerated.
*   **Stream Consumption**: The `num_sequences` function consumes the input stream. If you need to re-read the stream after calling `num_sequences` on it, you must reset its position (e.g., using `fileh.seek(0)`) or reopen the file.
*   **Mutable Strings**: The `useMutableString` option allows for more memory-efficient modification of sequences, but it's noted with a caution, implying potential differences in behavior or expectations compared to immutable Python strings.
*   **Error Handling for Progress**: The progress indicator gracefully handles streams for which `os.path.getsize` cannot determine a size, preventing crashes and issuing a warning.
*   **Copyright and License**: The code is licensed under the GNU General Public License v3 or later.