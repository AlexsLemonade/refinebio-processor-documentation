# No Op

## Tests

### `test_qn_reference`

Tests the entire QN Reference pipeline

Also creates a smasher job that uses the output from the QN Reference run and tests that

test steps:
- creates prerequisites
    - creates ProcessorJob
    - creates test Organism
    - creates test experiment
    - creates 200 test Samples and ComputedFiles using the test test Organism and adds them to the test experiment
    - creates test Dataset with the test experiment and samples 1-6
    - adds the test Dataset to the ProcessorJob
- runs QN Reference
- asserts the job was successfull
- asserts the created `target_file` exists
- asserts the `target_file` is the correct size
- gets the `computed_file` from the test Organism
- asserts the `computed_file`'s sha1 is correct
- creates prerequisites for the smasher job
    - creates ProcessorJob
    - creates test Dataset with the test experiment and samples 1-5
    - adds the test Dataset to the ProcessorJob
- runs the smasher
- asserts that the job was successful
- asserts that the `merged_qn` value is correct
- asserts that the `original_merged` value is correct

### `test_qn_management_command`

Test that the management command fires off and then does not create
a job for an organism that does not have enough samples on the same
platform

test steps:
- creates prerequisites
    - creates test Organism
    - creates test experiment
    - creates 6 test Samples and ComputedFiles using the test test Organism and adds them to the test experiment
    - creates test Dataset with the test experiment and samples 1-6
    - adds the test Dataset to the ProcessorJob
- calls `create_qn_target` expecting it to fail
- asserts that the error printed to stdout includes "Target file"
- asserts that a ProcessorJob wasn't created since there aren't enough samples

## Future Tests

