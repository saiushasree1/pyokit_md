The `wigIterators.py` file provides Python iterators for processing data in the Wiggle (WIG) format, a common format for displaying continuous data along a genome.

**Core Functionality:**

*   **Wiggle File Parsing:** The primary purpose is to read and parse WIG files, converting lines into `GenomicInterval` objects (presumably defined in `pyokit.datastruct.genomicInterval`).
*   **Support for Different WIG Formats:** It distinguishes between "regular" WIG files (where each data point explicitly defines its chromosome, start, and end) and "fixed-step" WIG files (where `fixedStep` lines define a region and subsequent lines provide scores at fixed intervals).
*   **Sorted Stream Enforcement:** Iterators can enforce sorting requirements (by start position or chromosome) and raise `WigIteratorError` if the input stream violates these requirements. This is crucial for efficient processing and for functions like `pairedWigIterator`.
*   **Progress Indication:** For verbose mode, it integrates with a `ProgressIndicator` to show the processing progress, which is helpful for large files.
*   **Paired Stream Iteration:** The `pairedWigIterator` function allows iterating over multiple WIG streams simultaneously, yielding lists of matching `GenomicInterval` objects. It includes a "mirror" feature to fill in missing intervals with a default score, ensuring that all streams yield an element for every considered genomic location.

**Code Structure and Key Components:**

*   **Constants:** `ITERATOR_SORTED_START` and `ITERATOR_SORTED_END` define sorting modes.
*   **`WigIteratorError`:** A custom exception class for errors specific to WIG iteration, particularly for unsorted input streams.
*   **`wigIterator(fd, ...)`:** This is the main entry point. It "peeks" at the first non-empty line of the input file descriptor (`fd`) to determine if it's a `fixedStep` or regular WIG file, then dispatches to the appropriate specialized iterator (`fixedWigIterator` or `regularWigIterator`).
*   **`regularWigIterator(fd, ...)`:** Iterates through a regular WIG file. It parses each line using `parseWigString` and yields `GenomicInterval` objects. It includes logic to check for sorting order if `sortedby` is specified.
*   **`fixedWigIterator(fd, ...)`:** Handles fixed-step WIG files. It parses `fixedStep` lines to determine the current chromosome, start, and step, then generates `GenomicInterval` objects for subsequent score lines. It also includes sorting checks.
*   **`pairedWigIterator(inputStreams, ...)`:** A more complex iterator that takes a list of file descriptors (or iterators). It maintains a list of the "next" element from each stream and advances the streams in a coordinated manner. It uses `operator.attrgetter` to create a key function for sorting and comparison of `GenomicInterval` objects.
*   **Unit Tests (`WigIteratorUnitTests`):** The `unittest.TestCase` demonstrates how to use the iterators and verifies their correctness for both regular, fixed-step, and paired WIG scenarios. It uses `DummyInputStream` for in-memory testing.

**Important Details and Considerations:**

*   **Dependencies:** The code relies heavily on classes from the `pyokit` library, specifically `GenomicInterval`, `parseWigString`, `openFD`, `getFDName`, `DummyInputStream`, `linesInFile`, and `ProgressIndicator`. Without `pyokit`, this code will not run.
*   **Wiggle Format Specifics:** The parsers are designed to handle the specific structure of Wiggle files, including `track` lines, `fixedStep` lines, and score data.
*   **Memory Efficiency:** The iterators are generators, meaning they yield one `GenomicInterval` at a time rather than loading the entire file into memory. This is crucial for processing very large WIG files.
*   **Sorting Enforcement:** The sorting checks are rigorous. If `sortedby` is set, any deviation from the expected order (by start or chromosome) will raise an error, making it suitable for pipelines that require pre-sorted input.
*   **`mirror` and `mirrorScore` in `pairedWigIterator`:** These parameters are powerful for comparing or combining WIG datasets where some regions might be present in one file but not another. `mirror=True` creates "placeholder" `GenomicInterval` objects for missing regions, using `mirrorScore`.
*   **GNU General Public License (GPLv3+):** The file is distributed under the GPL, indicating it's free software with specific usage and distribution rights.