The `WigDir` class in `wigDir.py` is designed to manage a collection of "wig" files, typically split by chromosome, within a directory. It provides functionalities to efficiently query data points (chromosomal positions) across these files without loading all of them into memory simultaneously.

**Core Functionality:**

*   **Initialization (`__init__`)**:
    *   Takes a directory path or a list of file paths/file-like objects.
    *   Loads initial file handles, mapping chromosome names (derived from filenames) to their respective file objects.
    *   Maintains a `self.files` dictionary where keys are chromosome names (e.g., "chr1") and values are open file handles.
    *   Keeps track of the `currentlyLoaded` `WigFile` object to optimize repeated queries on the same chromosome.
*   **Data Querying (`contains`, `getElement`, `getScore`)**:
    *   `contains(chrom, point)`: Checks if a given chromosomal position is covered by an element in the relevant Wig file.
    *   `getElement(chrom, point)`: Retrieves the `WigElement` (a data structure representing a segment and its score) that overlaps with the specified position. Returns `None` if no element is found.
    *   `getScore(chrom, point)`: Returns the numerical score associated with the `WigElement` at the given position.
*   **Lazy Loading (`__loadFile`)**:
    *   When a query for a specific chromosome is made, the `WigDir` checks if the corresponding `WigFile` is already loaded (`self.currentlyLoaded`).
    *   If not, it opens the relevant file handle, seeks to its beginning, and creates a `WigFile` object from it, making it the `currentlyLoaded` file. This minimizes memory usage by only loading one `WigFile` at a time.

**Structure and Important Details:**

*   **File Handling Abstraction**: The `WigDir` can handle a directory of files, a list of filenames, or even a list of pre-opened file-like objects (e.g., `DummyInputStream` used in tests), making it flexible.
*   **Chromosome to Filename Mapping (`__chromToFileHandle`)**: It internally maps a chromosome string (e.g., "chr1") to its corresponding file handle.
*   **Internal `__load` Method**: This private method handles the initial population of `self.files` based on the input `dir` argument, distinguishing between a directory path and a list of files/objects.
*   **Dependency on `WigFile`**: The `WigDir` class relies heavily on the `WigFile` class (from `pyokit.io.wigFile`) for the actual parsing and querying of individual `.wig` files. It essentially acts as an orchestrator for multiple `WigFile` instances.
*   **Unit Tests (`WigDirUnitTests`)**: The provided `unittest.TestCase` demonstrates how to use `WigDir` and verifies its `getScore` functionality using `DummyInputStream` objects, simulating in-memory wig files for testing purposes.
*   **GNU General Public License (GPLv3)**: The code is open-source and distributed under GPLv3, allowing free use, modification, and redistribution.

In summary, `WigDir` provides a convenient and memory-efficient way to access data from a collection of chromosome-specific `.wig` files by dynamically loading only the necessary file when a query for a particular chromosome is made.