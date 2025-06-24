This document, `bed.rst`, is a reStructuredText file providing comprehensive documentation for processing BED (Browser Extensible Data) files within the `Pyokit` library. It outlines two primary approaches for handling genomic intervals stored in BED files: iteration and bulk-loading.

**Key Functionality and Structure:**

1.  **Overview of BED File Processing:**
    *   Introduces BED files as plain-text files containing genomic intervals.
    *   Highlights `Pyokit`'s two main processing strategies: iteration (memory-efficient) and bulk-loading (for random access).
    *   Mentions `Pyokit`'s capability for simultaneous processing of multiple BED files to find matches or missing entries.
    *   Provides a link to the official BED format definition on UCSC Genome Browser.

2.  **Iterating Over BED Files:**
    *   Emphasizes the memory efficiency (O(1) memory usage) of iteration for sequential access.
    *   **Basic BED Iterator (`BEDIterator`):**
        *   Demonstrates its usage with a Python example, showing how to iterate over a file and obtain `GenomicInterval` objects.
        *   Uses `.. autofunction:: pyokit.io.bedIterators.BEDIterator` to pull in the function's signature and documentation.
    *   **Paired BED Iterator (`pairedBEDIterator`):**
        *   Designed for simultaneous iteration over multiple BED files.
        *   Explains options for handling non-matching intervals (ignoring or populating with defaults).
        *   Details the default matching criteria (chromosome, start, end, strand) and customizability.
        *   Provides a detailed Python example demonstrating how to use `pairedBEDIterator` to find matching intervals across three BED files (`one.bed`, `two.bed`, `three.bed`).
        *   Includes `bash` code blocks to show the content of the example BED files and the expected output from the Python script.
        *   Uses `.. autofunction:: pyokit.io.bedIterators.pairedBEDIterator` to pull in the function's signature and documentation.

3.  **Bulk-loading BED files:**
    *   Discusses scenarios where random access to intervals is needed, making bulk-loading more suitable.
    *   **Loading into a list:**
        *   Shows a simple way to load all intervals into a Python list using list comprehension with an existing iterator.
    *   **Loading into an interval tree:**
        *   Introduces the `intervalTrees` function, which loads a BED file into a dictionary of interval trees.
        *   Explains that keys are chromosome names and values are interval trees, enabling O(log(n)) lookup times for intervals within a genomic region.
        *   Adds a `.. note::` highlighting that while convenient, it might not always be the absolute fastest method, and pre-sorting/simultaneous iteration can sometimes be faster.
        *   References another section (`:ref:`intervalTreesSection``) for details on interval tree objects.
        *   Uses `.. autofunction:: pyokit.io.bedIterators.intervalTrees` to pull in the function's signature and documentation.

**Important Details:**

*   The document serves as a user guide, providing practical examples and explanations for common BED file processing tasks in `Pyokit`.
*   It leverages `autofunction` directives, indicating that the detailed API documentation for the mentioned functions (`BEDIterator`, `pairedBEDIterator`, `intervalTrees`) is generated dynamically from their docstrings within the `pyokit.io.bedIterators` module.
*   The examples are clear and runnable, illustrating the expected input and output for different functions.
*   It provides practical advice on when to choose between iteration (memory-efficient sequential access) and bulk-loading (random access with interval trees).