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

Tests that the detector properly handles a mislabeled Sample.

test steps:
- creates test ProcessorJob
- creates test OriginalFile and associates it to the test job
    - the OriginalFile is `GSE54661_non_normalized.txt` which is mislabeled as `illuminaHumanv4` on GEO
- creates test Organism
- creates test Samples
- associates the test Samples to the test OriginalFile
- runs the illumina pipeline
- asserts that the job ran successfully
- asserts that the platform was set to `illuminaHumanv3`
- for every test Sample
    - gets the keys from the Sample's associated Sample annotation set
    - asserts that the keys are one of the following acceptable keys: "detected_platform", "detection_percentage", or "mapped_percentage"
- does final cleanup by deleting the work directory

## Agilent Two Color Tests

###  `test_agilent_twocolor`

Tests the entire Agilent twocolor pipeline

test steps:
- prepares Agilent twocolor job
    - creates test ProcessorJob
    - creates test OriginalFile
    - points the OriginalFile to an Agilent twocolor file
    - associates it to the test job
- runs the Agilent twocolor pipeline
- asserts that the output PCL file exists

## Future Tests

It might make sense to split `test_geo.py` into two files `test_illumina.py` and `test_agilent_twocolor.py`.

For illumina, there's a lot of setup logic before illumina is actually run.
The setup happens in the functions `_prepare_files`, `_detect_columns`, and `_detect_platform`.
We could write tests for each function to test that the logic works properly.

for `_prepare_files` we could test the logic that removes comments from R files.

for `_detect_columns` we could test that `columnIds` are properly added to the job context.

There are already tests that are meant to target `_detect_platform`, but it might be worth it to add some more targeted tests.

We should improve the tests by checking that the generated computed files of both geo and illumina are generated properly by checking thier correlation to pre-computed files.
This would involve:
- Creating a reference file and including it in the repo.
- Checking that the gene expression column of the file of both the generated and reference files had a Spearman correlation >= 0.99.

When it comes to Agilent twocolor, it should be noted that as of right now we don't actually process any Agilent arrays in production
because they are not in `config/supported_microarray_platforms.csv`.

It might make sense to hold off on beefing up the Agilent twocolor tests until we support processing Agilent arrays in production.

If we ever do decide to add tests for Agilent twocolor,
the thing that stood out the most to me was the section in `_run_scan_twocolor`
that refers to the expected bug that occurs in Biocunductor.
It might be a good idea to look into this and see if that logic still stands.
