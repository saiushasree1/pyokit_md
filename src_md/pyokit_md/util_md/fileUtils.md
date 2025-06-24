The `fileUtils.py` module provides a collection of Python functions designed for common file manipulation tasks. It includes utilities for iterating through file contents, generating unique filenames, counting lines in a file, and robustly opening file descriptors.

**Key Functionality:**

*   **`genericFileIterator(fn, verbose=False)`**: This generator function iterates over a given file (specified by name or file object), yielding non-blank lines. If `verbose` is True, it attempts to display a progress indicator using `pyokit.util.progressIndicator`.
*   **`getUniqueFilename(dir=None, base=None)`**: Generates a unique temporary filename (e.g., "12345.tmp") in the current working directory or a specified directory, ensuring the generated name does not already exist.
*   **`linesInFile(fd)`**: Counts the total number of lines in a file, given either a file path string or an open file descriptor. It opens and closes the file if provided as a path.
*   **`openFD(fd)`**: Takes a file descriptor (string path, file object, or mmap object) and attempts to return a new file stream ready for reading from the beginning. It handles different input types by either opening the file, reopening an existing file object, or returning an mmap object directly. It tries to reset the stream if it's a file-like object that supports `reset()`.
*   **`getFDName(fd)`**: Extracts the filename from a given file descriptor, which can be a string path or a file-like object.

**Structure and Design:**

*   The module is self-contained, providing utility functions without defining any classes.
*   It leverages standard Python modules like `sys`, `os`, and `random`.
*   It uses `copy.copy` in `openFD` to attempt to create a shallow copy of a file-like object, which might be an unusual approach for ensuring a new stream from the beginning.
*   Type checking is primarily done using `type(var).__name__ == "str"` or similar string comparisons of the type name, which is generally less robust than `isinstance()` for checking types.

**Important Details and Potential Considerations:**

*   **Error Handling in `genericFileIterator`**: The `ProgressIndicator` attempts to get the file size and current tell position. If these operations fail (e.g., for non-seekable streams), it catches the exception broadly and disables verbose mode, printing a message to `stderr`.
*   **`openFD` Behavior**: The `copy(fd)` and `nfd.reset()` logic might not work as expected for all custom file-like objects. For standard `file` objects, `open(fd.name)` is a more reliable way to get a new stream from the beginning. The `mmap` handling is specific.
*   **`getUniqueFilename` Simplicity**: The `getUniqueFilename` function simply generates a random number and appends `.tmp`. For more robust unique filename generation in production systems, consider using `tempfile` module functions (e.g., `tempfile.NamedTemporaryFile`). It also currently only works in the current directory and doesn't use the `dir` parameter effectively to create files in a specified directory. The `base` parameter is also not used.
*   **Resource Management**: `linesInFile` explicitly closes the file handle if it opens it. The generator in `genericFileIterator` implicitly handles the file handle, but it's important to note that the calling code is responsible for closing the file object if one was passed in directly.
*   **GNU GPL v3 License**: The code is distributed under the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version. This implies that any redistribution or modification of this code must also adhere to the terms of the GPL.