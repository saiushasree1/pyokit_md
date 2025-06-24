This reStructuredText document outlines the installation process for the `Pyokit` Python library.

**Key aspects of the document:**

*   **Dependencies:** It explicitly states that `Pyokit` *optionally* depends on `rpy2` for some features. While `Pyokit` can be installed without `rpy2`, certain functionalities will be unavailable.
*   **Installation Methods:**
    *   **From PyPI (Recommended):** This is the easiest method using `pip`.
        *   It provides instructions to first acquire `pip` itself, suggesting `wget https://bootstrap.pypa.io/get-pip.py` followed by `python get-pip.py`. It also advises users to ensure `python` points to the desired Python version, especially if lacking administrator access.
        *   Once `pip` is available, `Pyokit` can be installed with `pip install pyokit`.
        *   A reminder about potentially needing `sudo` for global installations is included.
    *   **From Source:** This method is for users who want the latest "bleeding-edge" version or prefer to build from a specific release.
        *   It directs users to clone the GitHub repository (`www.github.com/pjuren/pyokit`) or download tagged `.tar.gz` files.
        *   `pip` is still required for this method.
        *   The steps involve:
            1.  Building the source distribution using `python setup.py sdist`.
            2.  Installing the distribution package with `pip install --no-index dist/pyokit-a.b.c.tar.gz`, where `a.b.c` represents the version number.

In summary, the document provides comprehensive instructions for installing Pyokit, catering to both standard PyPI installations and installations directly from source, with a clear note on an optional dependency.