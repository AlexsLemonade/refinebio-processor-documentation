# No Op

## Tests


### `test_convert_simple_pcl`

Tests the entire No Op pipeline for an AFFY gene sample

test steps:
- creates prerequisites for the first pipeline run
    - creates ProcessorJob
    - creates an OriginalFile and points to a file with a header
    - creates a test Sample
    - associates the ProcessorJob and OriginalFile to the test Sample
- runs the No Op pipeline using the above prerequisites
- creates prerequisites for the second pipeline run
    - creates ProcessorJob
    - creates an OriginalFile and points to a file with no header
    - creates a test Sample
    - associates the ProcessorJob and OriginalFile to the test Sample
- runs the No Op pipeline using the above prerequisites
- asserts the job successfully ran
- asserts that the output file exists
- asserts that the output file is the size we expect
    - within 5% since the output file sizes can vary each run

### `test_convert_processed_illumina`

Tests the entire No Op pipeline for an Illumina gene sample

test steps:
- creates prerequisites
    - creates ProcessorJob
    - creates an OriginalFile and points to a file with a header
    - creates a test Sample
    - associates the ProcessorJob and OriginalFile to the test Sample
- runs the No Op pipeline using the above prerequisites
- asserts the job successfully ran
- asserts that the output file exists
- asserts that the output file is the size we expect
    - within 5% since the output file sizes can vary each run
- asserts that the output file passes quality control
    - makes sure each row is no more than 2 columns
    - if there's more than 2 column in a row that's considered bad data likely caused by an old machine or bad processing

### `test_convert_illumina_no_header`

Tests the entire No Op pipeline for an Illumina gene sample without a header

test steps:
- creates prerequisites
    - creates ProcessorJob
    - creates an OriginalFile and points to a file without a header
    - creates a test Sample
    - associates the ProcessorJob and OriginalFile to the test Sample
- runs the No Op pipeline using the above prerequisites
- asserts the job successfully ran
- asserts that the output file exists
- asserts that the output file is the size we expect
    - within 5% since the output file sizes can vary each run

### `test_convert_illumina_bad_cols`

Tests the entire No Op pipeline for an Illumina gene sample with a bad column in the input file

test steps:
- creates prerequisites
    - creates ProcessorJob
    - creates an OriginalFile and points to a file without a header
    - creates a test Sample
    - associates the ProcessorJob and OriginalFile to the test Sample
- runs the No Op pipeline using the above prerequisites
- asserts the job did not successfully ran
- asserts the job failure reason is set correctly

## Future Tests

The No Op pipeline is composed of `_prepare_files`, `_convert_genes`, and `_create_result`.

`_prepare_files` has a lot of logic in it and should probably be individually tested.
We could test that the `work_dir` is properly created, that `input_file_path` and `output_file_path` are created properly,
and that all the logic related to the header works correctly.

`_convert_genes` also has a lot of logic both for `affy` and `illumina` genes.
Both routes should probably be individually tested.

`_create_result` could also be pretty easily tested.
We could test that the ComputationalResult and the ComputedFile are properly created and that they get associated properly to the Samples.
