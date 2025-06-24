The `dummyfiles.py` script provides two Python classes, `DummyInputStream` and `DummyOutputStream`, designed to simulate file-like objects using in-memory string data. This is particularly useful for testing or scenarios where actual file I/O is undesirable or impractical. The code is licensed under the GNU General Public License v3 or later.

**`DummyInputStream` Class:**
*   **Purpose:** Behaves like a read-only input stream, similar to `sys.stdin` or an opened file for reading.
*   **Initialization (`__init__`)**:
    *   Takes `lines` as input, which can be a single string (split by newlines) or a list of strings.
    *   Each line is automatically appended with a newline character (`\n`).
    *   Maintains `current` (the current line index), `length` (total number of lines), and an optional `filename`.
*   **Core Functionality:**
    *   `readline()`: Returns the next line from the internal buffer. Returns an empty string when the end of the "file" is reached.
    *   `reset()`: Resets the `current` position to the beginning (0).
    *   `tell()`: Returns the current line index (analogous to file pointer position).
    *   `seek(s)`: Sets the `current` line index to `s`.
    *   `__iter__` and `next`: Makes the `DummyInputStream` iterable, allowing it to be used directly in `for` loops.
*   **Comparison Operators (`__eq__`, `__ne__`)**:
    *   Implements `__eq__` to consider itself equal to `sys.stdin`, likely for compatibility in code that might conditionally use `sys.stdin`.
    *   Implements `__ne__` accordingly.
*   **`close()`**: A no-op method, as there are no actual file resources to close.

**`DummyOutputStream` Class:**
*   **Purpose:** Behaves like a write-only output stream, similar to `sys.stdout` or an opened file for writing.
*   **Initialization (`__init__`)**:
    *   Initializes `stored` (a list to hold written lines) and `prev` (a buffer for incomplete lines).
*   **Core Functionality:**
    *   `write(sth)`: Writes a string `sth` to the internal buffer. It handles incomplete lines (those without a newline) by buffering them in `self.prev` until a newline is encountered, then appends complete lines to `self.stored`.
    *   `close()`: Appends any remaining content in `self.prev` to `self.stored`.
    *   `itemsWritten()`: Returns the list of all complete lines that have been "written."
    *   `numItemsWritten()`: Returns the count of complete lines written.
    *   `__str__()`: Provides a string representation of all written items, joined by newlines.

**Important Details:**
*   The `DummyInputStream` automatically appends `\n` to each line upon initialization, mimicking standard file reading behavior.
*   The `DummyOutputStream` handles line buffering, ensuring that data is only considered a "line" once a newline character is encountered. This is crucial for accurately simulating how output streams process data.
*   Both classes are designed to be lightweight and primarily serve as mock objects for testing or simple data processing where actual file system interaction is not required.
*   The `DummyInputStream`'s `__eq__` implementation with `sys.stdin` suggests it might be used to interchangeably replace standard input in certain contexts.