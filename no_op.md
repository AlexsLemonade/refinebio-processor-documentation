# No Op

## Tests


### `test_convert_simple_pcl`

Tests the entire no op pipeline for an AFFY gene sample

test steps:
- creates prerequisites for the first pipeline run
    - creates ProcessorJob
    - creates an OriginalFile and points to a file with a header
    - creates a test Sample
    - assaciates the ProcessorJob and OriginalFile to the test Sample
- runs the no op pipeline using the above prerequisites
- creates prerequisites for the second pipeline run
    - creates ProcessorJob
    - creates an OriginalFile and points to a file with no header
    - creates a test Sample
    - assaciates the ProcessorJob and OriginalFile to the test Sample
- runs the no op pipeline using the above prerequisites
- asserts the job successfully ran
- asserts that the output file exists
- asserts that the output file is the size we expect
    - within 5% since the output file sizes can vary each run

### `test_convert_processed_illumina`

Tests the entire no op pipeline for an Illumina gene sample

test steps:
- creates prerequisites
    - creates ProcessorJob
    - creates an OriginalFile and points to a file with a header
    - creates a test Sample
    - assaciates the ProcessorJob and OriginalFile to the test Sample
- runs the no op pipeline using the above prerequisites
- asserts the job successfully ran
- asserts that the output file exists
- asserts that the output file is the size we expect
    - within 5% since the output file sizes can vary each run
- asserts that the output file passes quality control
    - makes sure each row is no more than 2 columns
    - if there's more than 2 column in a row that's considered bad data likely caused by an old machine or bad processing

### `test_convert_illumina_no_header`

Tests the entire no op pipeline for an Illumina gene sample without a header

test steps:
- creates prerequisites
    - creates ProcessorJob
    - creates an OriginalFile and points to a file without a header
    - creates a test Sample
    - assaciates the ProcessorJob and OriginalFile to the test Sample
- runs the no op pipeline using the above prerequisites
- asserts the job successfully ran
- asserts that the output file exists
- asserts that the output file is the size we expect
    - within 5% since the output file sizes can vary each run

### `test_convert_illumina_bad_cols`

Tests the entire no op pipeline for an Illumina gene sample with a bad column in the input file

test steps:
- creates prerequisites
    - creates ProcessorJob
    - creates an OriginalFile and points to a file without a header
    - creates a test Sample
    - assaciates the ProcessorJob and OriginalFile to the test Sample
- runs the no op pipeline using the above prerequisites
- asserts the job did not successfully ran
- asserts the job failure reason is set correctly

## Future Tests

