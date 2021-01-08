# Create Quantpendia

## Tests

### `test_create_quantpendia`

Tests the entire Create Quantpendia pipeline

test steps:
- creates prerequisites
    - creates test ProcessorJob
    - creates test Experiment
    - creates test ComputationalResult
    - creates test Organism
    - creates test Sample and associates it with the test ComputationalResult and Experiment
    - creates second test Sample and only associates it with the test Experiment making it non-downloadable
    - creates ComputedFile and sets the sha1 to match the downloaded file
    - creates second ComputedFile and associates it with the downloadable test Sample
    - creates test Dataset with the test Experiment and Samples and associates it to the ProcessorJob
- runs Create Quantpendia pipeline
- asserts that the quant.sf file was created
- asserts that the readme was created
- asserts that the license file was create
- asserts that the `aggregated_metadata` file was created
- checks metadata values
    - asserts that `quant_sf_only` is True
    - asserts that the number of Samples is 1
    - asserts that the number of Experiments is 1
- checks that the archive exits
    - gets the latest ComputedFile with `is_compendia` and `quant_sf_only` both set to True
    - asserts that the ComputedFile exists

## Future Tests


