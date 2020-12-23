# Geo

## Illumina to PCL Tests

### `test_illumina_to_pcl`

Tests entire illumina pipeline.

test steps:
- creates test Organism
- prepares illumina job
    - creates test ProcessorJob
    - creates test OriginalFile and associates it to the test job
    - creates test Samples and Sample annotations
    - associates the test Samples to the test OriginalFile
    - gets one of the Samples and sets the title and accession code to be ignored
- runs the illumina pipeline
- asserts that the job ran successfully
- for every test Sample
    - asserts that the Sample's smashable result file exists
    - cleans up by deleting that file
- does final cleanup by deleting the work directory

### `test_bad_illumina_detection`

Tests that the illumina pipeline will abort when the wrong Organism is set.

test steps:
- creates test Organism but sets the Organism name to `RATTUS_NORVEGICUS` instead of `HOMO_SAPIENS`
- prepares illumina job
    - creates test ProcessorJob
    - creates test OriginalFile and associates it to the test job
    - creates test Samples and Sample annotations
    - associates the test Samples to the test OriginalFile
    - gets one of the Samples and sets the title and accession code to be ignored
- runs the illumina pipeline
- asserts that the job was aborted
- does final cleanup by deleting the work directory

### `test_good_detection`

Tests that the detector properly handles a mislabled Sample.

test steps:
- creates test ProcessorJob
- creates test OriginalFile and associates it to the test job
    - the OriginalFile is `GSE54661_non_normalized.txt` which is mislabled as `illuminaHumanv4` on GEO
- creates test Organism
- creates test Samples
- associates the test Samples to the test OriginalFile
- runs the illumina pipeline
- asserts that the job ran successfully
- asserts that the platform was set to `illuminaHumanv3`
- for every test Sample
    - gets the keys from the Sample's associated Sample annotation set
    - asserts that the keys are one of the following accaptable keys: "detected_platform", "detection_percentage", or "mapped_percentage"
- does final cleanup by deleting the work directory

## Agilent Two Color Tests

###  `test_agilent_twocolor`

Tests the entire agilent twocolor pipeline

test steps:
- prepares agilent twocolor job
    - creates test ProcessorJob
    - creates test OriginalFile 
    - points the OriginalFile to an agilent twocolor file
    - associates it to the test job
- runs the agilent twocolor pipeline
- asserts that the output PCL file exists

## Future Tests

It might make sense to split `test_geo.py` into two files `test_illumina.py` and `test_agilent_twocolor.py`.

For illumina, there's a lot of setup logic before illumina is actually run.
The setup happens in the functions `_prepare_files`, `_detect_columns`, and `_detect_platform`.
We could write tests for each function to test that the logic works properly.

for `_prepare_files` we could test the logic that removes comments from R files.

for `_detect_columns` we could test that `columnIds` are properly added to the job context.

There are already tests that are meant to target `_detect_platform`, but it might be worth it to add some more targeted tests.

When it comes to testing agilent twocolor, the thing that stood out the most to me was the section in `_run_scan_twocolor` that refers to the expected
bug that occurs in Biocunductor.
It might be a good idea to look into this and see if that logic still stands. 
