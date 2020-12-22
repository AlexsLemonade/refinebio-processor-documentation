# Array Express

## Tests

### `test_affy_to_pcl`

Tests entire array express pipeline for a brainarray file.

test steps:
- prepares job with a brainarray file
    - creates test processor job
    - creates test original file and sets the source to a brainarray file
    - associates the original file to the test processor job
- makes sure that the worker dir isn't still around from previous runs
- runs the array express pipeline
- asserts that the job ran successfully
- asserts that a single computational result was created
- asserts that a single computed file was created
- asserts that the computed file's name is correct
- cleans up by deleting the computed file

### `test_affy_to_pcl_no_brainarray`

Tests entire array express pipeline for a non brainarray file.

test steps:
- prepares job with a non brainarray file
    - creates test processor job
    - creates test original file and sets the source to a non brainarray file
    - associates the original file to the test processor job
- makes sure that the worker dir isn't still around from previous runs
- runs the array express pipeline
- asserts that the job ran successfully
- asserts that a single computational result was created
- asserts that a single computed file was created
- asserts that the computed file's name is correct
- cleans up by deleting the computed file

### `test_affy_to_pcl_huex_v1`

Tests entire array express pipeline for a huex v1 file.

Since there's no CDL for huex v1 files, this is a special case that should be tested.

test steps:
- prepares job with a huex v1 file
    - creates test processor job
    - creates test original file and sets the source to a huex v1 file
    - associates the original file to the test processor job
- makes sure that the worker dir isn't still around from previous runs
- runs the array express pipeline
- asserts that the job ran successfully
- asserts that a single computational result was created
- asserts that a single computed file was created
- asserts that the computed file's name is correct
- cleans up by deleting the computed file

## Future Tests


