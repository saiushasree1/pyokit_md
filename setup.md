This `setup.py` file configures the `setuptools` library to package and distribute the `pyokit` Python project.

**Core Functionality:**

*   **Project Metadata:** Defines essential information about the `pyokit` project, including its name (`pyokit`), version (`0.2.0`), author (`Philip J. Uren`), email, URL, download URL, and a description.
*   **Package Discovery:** Uses `find_packages('src')` to automatically locate and include all Python packages residing within the `src` directory. This means the actual Python source code is expected to be under `src/pyokit` (or similar subdirectories).
*   **Package Directory Mapping:** The `package_dir={'': 'src'}` argument tells `setuptools` that the root of the Python packages (where modules like `pyokit` would be found) is the `src` directory.
*   **Console Script Entry Point:** Configures a console script named `pyokit`. When the package is installed, this script will execute the `main` function located in the `pyokit.scripts.pyokitMain` module.
*   **Dependencies:** Specifies runtime dependencies: `mock>=1.0.0` and `enum34`.
*   **Licensing:** Declares the project's license as 'LGPL2'. The header comments, however, indicate the project is distributed under the GNU General Public License (GPL) version 3 or later. This discrepancy (LGPL2 in `setup()` vs. GPLv3 in comments) is an important detail for users and maintainers to be aware of.
*   **Keywords and Classifiers:** The `keywords` and `classifiers` lists are empty, meaning no specific keywords or PyPI classifiers are defined for this package.

**Structure and Important Details:**

*   **Standard `setup.py`:** This file follows the standard structure for Python projects using `setuptools` for distribution.
*   **Source Code Location:** A crucial detail is that the actual Python source code (packages like `pyokit`) is expected to be located within a top-level `src` directory, rather than directly in the project root.
*   **License Discrepancy:** The most important detail to note is the conflict between the license specified in the `setup()` call (`LGPL2`) and the detailed license information provided in the initial comments (`GNU General Public License ... either version 3 ... or any later version`). This should be clarified to avoid confusion about the project's exact licensing terms.
*   **No Test/Dev Dependencies:** Only `install_requires` are specified; there are no separate definitions for development or testing dependencies.
*   **No Data Files:** The setup does not explicitly include any non-Python data files.