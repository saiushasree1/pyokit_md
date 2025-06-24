The Python file `testing.py` provides utility functions to assist with testing, specifically focusing on mocking file operations.

The primary component is the `build_mock_open_side_effect` function. This function creates and returns a callable (a "side effect" function) that can be used with mocking libraries (like `unittest.mock.mock_open`) to simulate opening files.

**How it works:**

*   It takes two dictionaries as input:
    *   `string_d`: Maps filenames to their string content. When a simulated file is "opened" that matches a key in this dictionary, a `StringIO.StringIO` object is returned containing the specified string.
    *   `stream_d`: Maps filenames to pre-existing stream objects. If a filename matches a key here, the corresponding stream object is returned directly.
*   The `add_names` parameter, if `True` (default), adds a `.name` attribute to the returned `StringIO` or stream object, setting its value to the mocked filename. This can be useful for tests that inspect the `name` attribute of file-like objects.
*   The returned `mock_open_side_effect` function acts as a mock `open()` call. When invoked with a filename:
    *   It first checks `string_d`. If found, it returns a `StringIO` object.
    *   If not in `string_d`, it checks `stream_d`. If found, it returns the pre-supplied stream object.
    *   If the filename is not found in either dictionary, it raises an `IOError`, mimicking a "No such file" error and listing the known mock files for debugging.

**Important Details:**

*   The function ensures that there are no overlapping filenames between `string_d` and `stream_d` using an assertion.
*   It's designed to be used in conjunction with a mocking framework (e.g., `unittest.mock`) where `mock_open_side_effect` would be assigned as the `side_effect` for a mocked `open` function.
*   The file is licensed under the GNU General Public License v3.