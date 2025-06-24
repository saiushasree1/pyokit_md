The `david.py` script, created on December 19, 2014, provides functions for parsing and loading gene ontology enrichment results from DAVID bioinformatics resources. It's distributed under the GNU General Public License v3 or later.

**Core Functionality:**

The script's primary purpose is to read tab-separated DAVID result files and transform the relevant information into `GeneOntologyEnrichmentResult` objects, which are expected to be defined within the `pyokit.datastruct.geneOntology` module.

**Structure and Key Components:**

1.  **Module-Level Constants:**
    *   `NUM_FIELDS_IN_DAVID_RECORD`: Set to `13`, indicating the expected number of tab-separated fields in a DAVID result line.
    *   `PVAL_FIELD_NUM`: Set to `11`, specifying that the Benjamini-corrected p-value is located at this index (0-based) in the record.

2.  **`david_results_iterator(fn, verbose=False)` function:**
    *   This is the central parsing function. It acts as a generator, yielding `GeneOntologyEnrichmentResult` objects one by one.
    *   **Input:** Takes a file path `fn` and an optional `verbose` flag.
    *   **File Format Expectation:** It expects a tab-separated file with a specific 13-column format, as detailed in the docstring. The first line is treated as a header and skipped.
    *   **Parsing Logic:**
        *   It iterates line by line, skipping empty lines and the header.
        *   Each line is split by a tab delimiter.
        *   It performs a sanity check to ensure the line has the expected `NUM_FIELDS_IN_DAVID_RECORD` fields; otherwise, it raises an `IOError`.
        *   It extracts the `name` (term description) and `identifier` (GO ID) from the second field (index 1) by splitting on `~`. If `~` is not present, the `identifier` is set to `None`.
        *   The `catagory` is taken from the first field (index 0).
        *   The `p_val` (Benjamini-corrected p-value) is parsed from the field at `PVAL_FIELD_NUM` (index 11). A `ValueError` during float conversion will raise an `IOError`.
        *   Finally, it yields a `GeneOntologyEnrichmentResult` object constructed with the extracted `name`, `p_val`, `identifier`, and `catagory`.
    *   **Ignored Fields:** The docstring explicitly states that most fields (Count, Percent, Genes, List Total, Pop Hits, Pop Total, Fold Enrichment, Bonferroni, FDR) are currently ignored, and only Category, Term, and Benjamini p-value are used.

3.  **`david_results_load_file(fn, verbose=False)` function:**
    *   This is a convenience function that utilizes `david_results_iterator`.
    *   **Input:** Takes a file path `fn` and an optional `verbose` flag.
    *   **Functionality:** It calls `david_results_iterator` and converts all yielded `GeneOntologyEnrichmentResult` objects into a list, effectively loading all results into memory.

**Important Details and Assumptions:**

*   **Dependency:** Relies on the `pyokit.datastruct.geneOntology.GeneOntologyEnrichmentResult` class, which is not defined within this snippet but is imported. This class is crucial for the script's functionality.
*   **File Format Strictness:** The script is very strict about the input file format, specifically the number of tab-separated fields and the data type of the p-value field. Any deviation will result in an `IOError`.
*   **P-value Field:** The script specifically uses the "Benjamini" field (index 11) as the primary significance measure.
*   **Error Handling:** Basic error handling is present for incorrect field counts and non-numeric p-values, raising `IOError` with descriptive messages.
*   **Header Handling:** The first line of the input file is always skipped, assuming it's a header.