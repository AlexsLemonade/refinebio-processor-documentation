# Transcriptome Index

## Tests

### `test_assembly_information`

Tests `transcriptome_index._extract_assembly_information` correctly gets assembly information from original file source urls

test steps:
- creates two dummy OriginalFiles with valid source urls
- calls `transcriptome_index._extract_assembly_information` and passes in the two OriginalFiles
- asserts that the assembly version is correctly extracted
- asserts that the assembly name is correctly extracted
- asserts that the database name is correctly extracted

### `test_tx`

Tests that the index files are built

test steps:
- removes the work directories
- prepares the first job with a length of `short`
    - creates ProcessorJob
    - creates Sample
    - creates OriginalFiles and sets the filepaths to point to the file with the correct length
- runs first job
- asserts job is successful
- asserts length is properly set in the job
- prepares the second job with a length of `long`
    - creates ProcessorJob
    - creates Sample
    - creates OriginalFiles and sets the filepaths to point to the file with the correct length
- runs second job
- asserts job is successful
- asserts length is properly set in the job
- asserts that the output directories for both jobs are different
- asserts that the two output files actually exist
- asserts that the output files for both jobs are different
- asserts that the output files can be found using the logic in `salmon._find_index`

## Future Tests

When testing the entire pipeline in `test_tx`, we only check that the the output files are created.
We could validate the contents of the files using the methods in the Salmon Tests like comparing checksums and testing correlation between files.

The remaining parts of the pipeline - `_compute_paths`, `_prepare_files`, `_process_gtf`, `_create_index`, `_zip_index`, and `_populate_index_object` could all be tested individually.

Particularly `_process_gtf` seems like it would be important to test.
It generates two files.
The first removes pseudogenes - we should make sure that they are removed correctly.
The second builds a tsv mapping between `gene_ids` and `transcript_ids` - we should verify that these mappings are set up correctly.

Additional functions like `_handle_shell_error` and `get_organism_name_from_path` could also be tested pretty easily.

We could also add a RuntimeProcessor Test similar to the ones in `test_salmon.py` to validate that the `TX_INDEX` processor is set up correctly.


