The `join.py` script is a command-line utility designed to join two data tables based on a common key field. It supports various options for handling headers, missing values, and duplicate keys.

**Core Functionality:**

*   **File Joining:** The primary purpose is to perform a join operation similar to a database join or the Unix `join` command, merging lines from two input files where a specified key column matches.
*   **Key Field Specification:** Users can specify the key field by its column number (1-indexed) or by its header name. This dictates whether the input files are treated as having headers or not.
*   **Missing Value Handling:** The script allows for the definition of a "missing value" string, which is used to populate empty fields or fields from unpaired lines.
*   **Duplicate Key Handling:** A crucial feature is its robust handling of duplicate key values, offering three strategies:
    *   **`error_on_dups`**: The program exits with an error if any duplicate keys are found in either input file. This is the default.
    *   **`all_pairwise_combinations`**: For a duplicated key, it outputs a separate line for every possible combination of matching lines from the two input files.
    *   **`column_wise_join`**: For a duplicated key, it outputs a single line where values from other fields are concatenated (separated by semicolons) if they differ across duplicated entries.
*   **Output of Unpaired Lines:** The script can optionally output lines from either file that do not find a match in the other file, filling the non-matching columns with the specified missing value.
*   **Ragged Input Detection:** It checks for consistent column counts per line within input files and raises an error if "ragged" input is detected.
*   **Command-Line Interface (CLI):** It utilizes a custom `CLI` class from `pyokit.interface.cli` to parse command-line arguments and options.
*   **Unit Testing:** The script includes a comprehensive `unittest.TestCase` class (`TestJoin`) to verify its functionality across various scenarios, including simple joins, header handling, empty fields, missing keys, duplicate headers, and different duplicate key handling strategies.

**Code Structure and Key Components:**

1.  **Imports:** Standard Python libraries (os, sys, abc, mock, copy, unittest, StringIO, collections.OrderedDict) and `enum` for Python 2 compatibility. It also imports custom modules from `pyokit`: `PyokitError`, `CLI`, `Option`, and `ProgressIndicator`.
2.  **Constants and Enums:**
    *   `DEFAULT_VERBOSITY`: Boolean for default verbosity setting.
    *   `OutputType` (Enum): Defines the three strategies for handling duplicate keys, each linked to a specific `OutputHandlerBase` subclass.
3.  **Exception Classes:** A hierarchy of custom exceptions, inheriting from `PyokitError`, to provide specific error messages for various failure conditions (e.g., `MissingKeyError`, `DuplicateKeyError`, `InvalidHeaderError`, `RaggedInputError`).
4.  **Output Handlers:**
    *   `OutputHandlerBase` (Abstract Base Class): Defines the interface for output handlers, requiring `write_output`, `get_description`, and `write_header` methods. The `write_header` method provides common logic for generating output headers based on input file headers and missing values.
    *   `NoDupsOutputHandler`: Implements the `error_on_dups` strategy. It calls `PairwiseCombinationOutputHandler` but raises a `DuplicateKeyError` if more than one entry is found for a key.
    *   `ColumnCombinationOutputHandler`: Implements `column_wise_join`, collapsing multiple values for the same key into a single line by concatenating field values with a semicolon.
    *   `PairwiseCombinationOutputHandler`: Implements `all_pairwise_combinations`, outputting a separate line for each pairwise match.
5.  **Helper Functions:**
    *   `file_iterator`: Iterates over file lines, optionally showing progress.
    *   `__populated_missing_vals`: Replaces empty string parts with a specified `missing_val`.
    *   `__parse__header`: Parses a header line, validates it for empty or duplicate fields, and identifies the key column index.
    *   `_build_entry`: Adds an entry to a dictionary, handling whether the key is a field number or name, and managing duplicate key scenarios based on the `output_type`.
    *   `load_file`: Reads an entire input file into an `OrderedDict`, parsing headers and building entries. It's used for the file that is loaded into memory first.
    *   `__output_unpaired_vals`: Helper to write lines that didn't find a match, populating missing columns.
6.  **User Interface (`getUI`):** Configures the `CLI` object with all command-line options (input files, output file, key fields, missing value, duplicate handling method, ignore missing keys, output unpaired lines, verbosity, and help/test flags).
7.  **Command Line Processing and Dispatch (`_main`, `get_key_field`):**
    *   `get_key_field`: Parses a command-line option to determine the key field (number or name).
    *   `_main`: The main entry point for script execution. It parses CLI arguments, opens input/output files, retrieves all user-specified options, and dispatches to the `process` function. It also handles unit test execution and help display.
8.  **Main Program Logic (`process`, `populate_unpaired_line`, `process_without_storing_header`, `get_key_value`, `process_without_storing`, `process_by_storing`):**
    *   `process`: The core logic that orchestrates the join. It first loads one file into memory (`f2_dictionary`) and then either processes the second file by storing it (`process_by_storing`) or by iterating through it line by line (`process_without_storing`). The choice depends on the duplicate handling method.
    *   `process_without_storing`: Handles joins where the second file is processed line by line, without loading its entire content into memory, useful for large files when the `column_wise_join` method isn't used.
    *   `process_by_storing`: Used when the `column_wise_join` duplicate handling method is selected, as this method requires all entries for a key to be available simultaneously. It loads the second file entirely.
9.  **Unit Tests (`TestJoin`):**
    *   A `setUp` method initializes various test data strings representing different file contents (with/without headers, duplicates, gaps, ragged lines).
    *   `build_mock_open_side_effect`: A helper to mock file opening, allowing tests to run without actual file I/O.
    *   Numerous test methods (`test_simple_headerless_join`, `test_simple_header_join`, etc.) cover a wide range of scenarios and error conditions, asserting expected output or raised exceptions.

**Important Details and Considerations:**

*   **Memory Usage:** The `process_by_storing` function, used for the `column_wise_join` output type, loads an entire input file into an `OrderedDict`. This could lead to high memory consumption for very large files. The `process_without_storing` method, used for other output types, is more memory-efficient as it processes one file line by line.
*   **Performance:** For very large files, the choice of duplicate handling method directly impacts performance and memory.
*   **Error Handling:** The script uses specific custom exceptions for different error conditions, making it easier to diagnose issues.
*   **Key Column Indexing:** When specifying key columns by number, they are 1-indexed on the command line but converted to 0-indexed internally.
*   **License:** The code is distributed under the GNU General Public License, Version 3 or later, making it free software.