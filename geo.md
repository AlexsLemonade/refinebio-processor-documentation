# Geo

## Illumina to PCL Tests

### `test_illumina_to_pcl`

Tests entire illumina pipeline.

test steps:
- creates test organism
- prepares illumina job
    - creates test processor job
    - creates test original file and associates it to the test job
    - creates test samples and sample annotations
    - associates the test samples to the test original file
    - gets one of the samples and sets the title and accession code to be ignored
- runs the inllumina pipeline
- asserts that the job ran successfully
- for every test sample
    - asserts that the sample's smashable result file exists
    - cleans up by deleting that file
- does final cleanup by deleting the work directory

### `test_bad_illumina_detection`

Tests that the illumina pipeline will abort when the wrong organism is set.

test steps:
- creates test organism
- prepares illumina job
    - creates test processor job
    - creates test original file and associates it to the test job
    - creates test samples and sample annotations
    - associates the test samples to the test original file
    - gets one of the samples and sets the title and accession code to be ignored
- runs the inllumina pipeline
- asserts that the job ran successfully
- for every test sample
    - asserts that the sample's smashable result file exists
    - cleans up by deleting that file
- does final cleanup by deleting the work directory

### `test_good_detection`

Tests that the detector properly handles a mislabled sample.

test steps:
- creates test organism
- prepares illumina job
    - creates test processor job
    - creates test original file and associates it to the test job
    - creates test samples and sample annotations
    - associates the test samples to the test original file
    - gets one of the samples and sets the title and accession code to be ignored
- runs the inllumina pipeline
- asserts that the job ran successfully
- for every test sample
    - asserts that the sample's smashable result file exists
    - cleans up by deleting that file
- does final cleanup by deleting the work directory

## Agilent Two Color Tests

###  `test_agilent_twocolor`


## Future Tests

