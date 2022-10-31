# Compendia

## Tests

### `test_create_compendia`

Tests entire compendia pipeline.

Also verifies that samples with non-existent ComputedFiles are filtered.

test steps:
- creates compendia prerequisites
    - creates test ProcessorJob
    - creates Microarray test Sample
        - creates test Experiment
        - creates test ComputationalResult
        - creates test Sample and associates it to the test Experiment and ComputationalResult
        - creates test ComputedFile and associates it to the test Sample
    - creates Sample with non-existent ComputedFile
        - creates test Sample and associates it to the test Experiment and ComputationalResult from the last step
        - creates test ComputedFile, points it to a non-existent file, and associates it to the test Sample
    - creates RNA-Seq test Sample
        - creates test Experiment
        - creates test ComputationalResult
        - creates test Sample and associates it to the test Experiment and ComputationalResult
        - creates test ComputedFile and associates it to the test Sample
    - creates dataset using the three Samples and two Experiments created in previous steps
    - associates the dataset to the ProcessorJob
- runs the compendia pipeline
- asserts that the job ran successfully
- asserts that the test Sample with non-existent ComputedFile was filtered correctly

### `test_create_compendia_danio`

Tests entire compendia pipeline for DANIO_RERIO Organism.

Also verifies that samples with non-existent ComputedFiles are filtered.

test steps:
- creates Compendia prerequisites
    - creates test ProcessorJob
    - creates ComputationalResult and ComputedFile
    - creates danio rerio test organism and passes in the ComputedFile to be used as the `qn_target`
    - creates Microarray test Samples
        - creates test Experiment
        - creates test ComputationalResult
        - creates test Sample and associates it to the test Experiment and ComputationalResult
        - creates test ComputedFile and associates it to the test Sample
    - creates RNA-Seq test Sample
        - creates test Experiment
        - creates test ComputationalResult
        - creates test Sample and associates it to the test Experiment and ComputationalResult
        - creates test ComputedFile and associates it to the test Sample
    - creates Sample with non-existent ComputedFile
        - creates test Sample
        - associates test Sample to the RNA-Seq Experiment and ComputationalResult
        - does not create a ComputedFile for this Sample
    - creates dataset using the Microarray and RNA-Seq Experiments created in previous steps
    - associates the dataset to the ProcessorJob
- runs the compendia pipeline
- asserts that one `compendium_result` exists
- asserts that the `compendium_result`'s primary organism name was set correctly to `DANIO_RERIO`
- asserts that there is only one organism associates with the `compendium_result`
- asserts that the test Sample with non-existent ComputedFile was filtered correctly

## Future Tests

The are two main parts of the compendia pipeline that I think could be tested more thouroughly

Firstly, `_prepare_frames` could be tested pretty easily I think.
This function uses `smashing_utils.process_frames_for_key` to set up the job context before the next steps.
We could call this function and assert that the resulting job context is set up correctly,
or it might make more sense to just test `smashing_utils.process_frames_for_key` in the util test file.

Secondly, and most importantly we should definitely test the massive function `_perform_imputation`.
Right now our tests just verify that the pipeline ran successfully and that the `compendium_result` exists.
We should make sure that the intermediate tests work as well:
- We should test that imputation is done correctly:
    - Remove some values from the matrix.
    - Run imputation.
    - Compare the actual and imputed values.
- We filter both rows and columns during creation of compendia, we should test that the expected rows and columns are dropped from the matrices. These filters are explained in the comments of the `create_compendia.py` processor file, but repeated here for convenience:
    - Drop all rows in rnaseq_expression_matrix with a row sum < 10th percentile of rnaseq_row_sums; this is now filtered_rnaseq_matrix
    - Remove samples (columns) with >50% missing values in combined_matrix
- We should also test that quantile normalization is skipped for quantpendia.
