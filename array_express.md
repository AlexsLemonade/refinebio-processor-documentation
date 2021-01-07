# Array Express

## Tests

### `test_affy_to_pcl`

Tests entire array express pipeline for a brainarray file.

test steps:
- prepares job with a brainarray file
    - creates test ProcessorJob
    - creates test OriginalFile and sets the source to a brainarray file
    - associates the OriginalFile to the test ProcessorJob
- makes sure that the worker directory isn't still around from previous runs
- runs the array express pipeline
- asserts that the job ran successfully
- asserts that a single ComputationalResult was created
- asserts that a single ComputedFile was created
- asserts that the ComputedFile's name is correct
- cleans up by deleting the computed file

### `test_affy_to_pcl_no_brainarray`

Tests entire array express pipeline for a non brainarray file.

test steps:
- prepares job with a non brainarray file
    - creates test ProcessorJob
    - creates test OriginalFile and sets the source to a brainarray file
    - associates the OriginalFile to the test ProcessorJob
- makes sure that the worker directory isn't still around from previous runs
- runs the array express pipeline
- asserts that the job ran successfully
- asserts that a single ComputationalResult was created
- asserts that a single ComputedFile was created
- asserts that the ComputedFile's name is correct
- cleans up by deleting the computed file

### `test_affy_to_pcl_huex_v1`

Tests entire array express pipeline for a huex v1 file.

Since there's no CDF for huex v1 files, this is a special case that should be tested.

test steps:
- prepares job with a huex v1 file
    - creates test ProcessorJob
    - creates test OriginalFile and sets the source to a huex v1 file
    - associates the OriginalFile to the test ProcessorJob
- makes sure that the worker directory isn't still around from previous runs
- runs the array express pipeline
- asserts that the job ran successfully
- asserts that a single ComputationalResult was created
- asserts that a single ComputedFile was created
- asserts that the ComputedFile's name is correct
- cleans up by deleting the computed file

## Future Tests

There is some setup logic that we could test in the function `_determine_brainarray_package` which also calls the function `_create_ensg_pkg_map`.

It seems like it would be pretty easy to test `_create_ensg_pkg_map`, we just have to assert that the resulting dict is what we expect.

For `_determine_brainarray_package` we could test that `platform_accession_code` and `brainarray_package` are properly set in the job context.

Also, right now we only check that the generated computed file exists and is named properly.
We could improve the tests by checking that the generated computed file is generated properly by checking its correlation to a pre-computed file.
