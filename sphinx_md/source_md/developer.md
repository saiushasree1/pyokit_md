This documentation file, `developer.rst`, provides essential information for developers contributing to the Pyokit project. It covers two main areas:

1.  **Testing Changes:**
    *   **Core Principle:** Emphasizes that all test scripts and anything using Pyokit utilize the *installed* version of the library, not the source code directly.
    *   **Workflow for Testing Code Changes:** Developers must rebuild and reinstall Pyokit after making changes to the source code to ensure tests run against the updated library.
    *   **Handling Versioning:** Since the Python package manager won't reinstall if the version number hasn't changed, a practical workaround is provided: `make uninstall; make install`. This ensures a clean reinstallation.

2.  **Maintaining Documentation:**
    *   **Documentation System:** Pyokit uses Sphinx for documentation, which is built from both Pyokit source code and dedicated `.rst` documentation source files (located in `sphinx/source`).
    *   **Deployment:** Documentation is hosted on GitHub Pages and automatically built and pushed by Travis CI on every push to the Pyokit GitHub repository.
    *   **Updating Documentation:** Developers should edit the Sphinx `.rst` source files.
    *   **Local Build:** A `make docs` command is available to rebuild the documentation locally. Sphinx will output the HTML files (currently in `Docs/html`), which can be previewed in a web browser.
    *   **Important Nuance (Warning):** When building documentation, the *installed* version of Pyokit source code is used alongside the *source* `.rst` files. This means changes to Pyokit's source code will *not* be reflected in the documentation build until the library itself is rebuilt and reinstalled locally. However, simply editing `.rst` files only requires a documentation rebuild.