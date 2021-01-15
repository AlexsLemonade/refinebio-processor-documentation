# Smasher

## Tests

### `test_smasher`

Tests the entire smasher pipeline.
Additionally verifies results for different aggregation and scale types.

test steps:
- creates prerequisites
    - creates test ProcessorJob
    - creates test Experiment
    - creates test ComputationalResult
    - creates test Organism
    - creates test Sample
    - adds a SampleAnnotation to the Sample with `{"hi": "friend"}` as the data
    - associates the test sample to the test Experiment and ComputationalResult
    - creates test ComputedFile and associates it to the Sample
    - creates second test Sample
    - associates the second Sample to the test Experiment, ComputationalResult, and ComputedFile
    - creates PCL ComputedFile that is smashable
    - creates DAT ComputedFile that is not smashable
    - associates the DAT Computed File with the second test Sample
    - creates test Dataset with the test Experiment and Samples
    - associates the test Dataset to the test ProcessorJob
- verifies that the prerequisites were setup properly
    - gets the first test Sample
    - asserts that `hi` is in the Sample's annotation
    - gets the test Dataset
    - asserts that the Dataset has 2 Samples and 1 Experiment
- smoke tests `dataset.get_samples_by_experiment()`, `dataset.get_samples_by_species()`, `dataset.get_aggregated_samples()`
- for the aggregation types "ALL" and "EXPERIMENT"
    - sets the test Dataset to use the aggregation type
    - runs the smasher pipeline
    - asserts that the output file has a size that isn't 0
    - asserts that `dataset.is_processed` was set to True
    - cleans up by setting `dataset.is_processed` back to False, removing the output file, and setting the job start and end time to None
- for the scale types "NONE", "MINMAX", "STANDARD", and "ROBUST"
    - sets the test Dataset to use the aggregation type "EXPERIMENT" and the correct scale type
    - runs the smasher pipeline
    - asserts that the output file has a size that isn't 0
    - asserts that `dataset.is_processed` was set to True
    - cleans up by setting `dataset.is_processed` back to False, removing the output file, and setting the job start and end time to None
- for the scale types "MINMAX" and "STANDARD"
    - sets the test Dataset to use the aggregation type "SPECIES" and the correct scale type
    - runs the smasher pipeline
    - gets the final frame
    - sanity tests that the final frame can be computed upon by calling `.mean()`, `.min()`, `.max()`, `.std()`, and `.median()` on it
    - gets the name of all files in the output zip archive
    - asserts that the homo sapiens metadata tsv file is in the zip archive
    - asserts that the aggregated metadata json file is in the zip archive
    - asserts that the readme is in the zip archive
    - asserts that the license is in the zip archive
    - asserts that the homo sapiens tsv file is in the zip archive
    - cleans up by removing the output file and setting the job start and end time to None
- for the scale types "MINMAX" and "STANDARD"
    - sets the test Dataset to use the aggregation type "SPECIES" and the correct scale type
    - runs the smasher pipeline
    - gets the final frame
    - sanity tests that the final frame can be computed upon by calling `.mean()`, `.min()`, `.max()`, `.std()`, and `.median()` on it
    - gets the name of all files in the output zip archive
    - asserts that the homo sapiens metadata tsv file is in the zip archive
    - asserts that the aggregated metadata json file is in the zip archive
    - asserts that the readme is in the zip archive
    - asserts that the license is in the zip archive
    - asserts that the homo sapiens tsv file is in the zip archive
    - cleans up by removing the output file and setting the job start and end time to None

### `test_get_results`

Tests `sample.get_result_files()`

test steps:
- creates test Sample
- creates test ComputationalResult
- creates two test ComputedFiles
- associates the ComputedFiles and ComputationalResult to the test Sample
- calls `sample.get_result_files()`
- asserts that the number of ComputedFiles returned is 2

### `test_fail`

Verifies that the smasher pipeline can correctly handle a failure

test steps:
- creates test ComputationalResult
- creates test Sample
- associates the ComputationalResult to the test Sample
- creates test ComputedFile
- associates the ComputedFiles to the test Sample
- creates test Dataset with the test Sample
- creates test ProcessorJob and associates the Dataset to it
- runs the smasher pipeline
- asserts that the list of unsmashable files in the final job context is not empty

### `test_no_smash_all_diff_species`

Tests a weird use case where `aggregate_by` is set to "ALL" with different species

test steps:
- creates prerequisites
    - creates test ProcessorJob
    - creates test Experiment
    - creates test ComputationalResult
    - creates test Organism
    - creates test Sample
    - associates the test Sample to the ComputationalResult and Experiment
    - creates test ComputedFile and associates it to the test Sample
    - creates second test Experiment
    - creates second test Sample
    - associates the test Sample to the ComputationalResult and Experiment
    - creates second test ComputedFile and associates it to the second test Sample
    - creates test Dataset with the two Experiments and Samples
    - associates the Dataset to the test ProcessorJob
- runs the smasher pipeline
- asserts that the first Sample's ComputedFile was added to the job context's list of unsmashable files

### `test_qn_targets_only`

Tests the pipeline when `quant_sf_only` is set to True

test steps
- creates prerequisites
    - creates test ProcessorJob
    - creates test Experiment
    - creates test ComputationalResult
    - creates test Organism
    - creates test Sample
    - associates the test Sample to the ComputationalResult and Experiment
    - creates quant sf ComputedFile and sets its sha1 to match the downloaded file
    - creates tsv ComputedFile and associates it to the test Sample
    - creates test Dataset with the test Experiment and Sample
    - associates the Dataset to the test ProcessorJob
- runs the smasher pipeline
- asserts that the quant sf file exists in the output directory
- validates the metadata
    - asserts that `quant_sf_only` is True
    - asserts that the number of samples is set to 1
    - asserts that the number of experiments is set to 1
    - asserts that `aggregate_by` is not set in the metadata

### `test_no_smash_dupe`

Tests the case where two Samples are associated to the same ComputedFile.

** In this test there are additional places where test ComputationalResults are created.
I think they don't actually need to be created -
hey were kind of confusing to me and removing them or adding comments for their would make the test less confusing

test steps:
- creates prerequisites
    - creates test ProcessorJob
    - creates test Experiment
    - creates test ComputationalResult
    - creates test Organism
    - creates test Sample
    - associates the test Sample to the ComputationalResult and Experiment
    - creates test ComputedFile and associates it to the test Sample
    - creates second test Sample
    - associates the test Sample to the same ComputationalResult and Experiment
    - creates test Dataset using the two Samples
    - associates the Dataset to the test ProcessorJob
- runs the smasher pipeline
- asserts that the job completed successfully
- asserts that `_x` is not in the name of any `original_merged` columns 

### `test_no_smash_dupe_two`

Tests the case where two ComputedFiles have colliding titles.
Additionally this test uses a real QN Target file.

test steps:
- creates prerequisites
    - creates test ProcessorJob
    - creates test Experiment
    - creates test ComputationalResult
    - creates test Organism
    - creates test Sample
    - associates the test Sample to the ComputationalResult and Experiment
    - creates test ComputedFile and associates it to the test Sample
    - creates second test Sample
    - associates the second test Sample to the same ComputationalResult and Experiment
    - creates second test ComputedFile and associates it to the second test Sample
    - creates test Dataset using the two Samples and sets `quantile_normalize` to True
    - associates the Dataset to the test ProcessorJob
    - creates QN Target ComputedFile
    - creates QN Target ComputationalResultAnnotation and adds `"is_qn": True` to its data
- runs the smasher pipeline
- asserts the job completed successfully
- creates another test ProcessorJob 
- creates a new test Dataset with just one of the test Samples
- associates the new job to the new Dataset
- runs the smasher pipeline
- asserts the job completed successfully
- opens the `aggregated_metadata.json` file and asserts that the `quantile_normalized` field is set to True

### `test_log2`

Tests that an Experiment with non-log2 data is handled correctly

test steps:
- creates prerequisites
    - creates test ProcessorJob
    - creates test Experiment
        - uses "GSE44421" which has non-log2 data
    - creates test ComputationalResult
    - creates test Organism
    - creates test Sample
    - associates the test Sample to the ComputationalResult and Experiment
    - creates test ComputedFile and associates it to the test Sample
    - creates second test Sample
    - associates the second test Sample to the same ComputationalResult and Experiment
    - creates second test ComputedFile and associates it to the second test Sample
    - creates test Dataset using the two Samples
    - associates the Dataset to the test ProcessorJob
- runs the smasher pipeline
- asserts that the job completed successfully

### `test_dualtech_smash`

Tests the smasher pipeline  on a Dataset with Samples that use both MICROARRAY and RNASEQ technologies.

test steps:
- creates prerequisites
    - creates test ProcessorJob
    - creates MICROARRAY prerequisites
        - creates test Experiment
        - creates test ComputationalResult
        - creates test Organism
        - creates test MICROARRAY Sample
        - associates the test Sample to the ComputationalResult and Experiment
        - creates test ComputedFile and associates it to the test Sample
    - creates RNASEQ prerequisites
        - creates test Experiment
        - creates test ComputationalResult
        - creates test Organism
        - creates test RNASEQ Sample
        - associates the test Sample to the ComputationalResult and Experiment
        - creates test ComputedFile and associates it to the test Sample
- Aggregate by "SPECIES"
    - creates test Dataset using the two Experiments and Samples
    - associates the test dataset to the ProcessorJob
    - asserts that `dataset.is_cross_technology()` returns True
    - runs the smasher pipeline
    - asserts that the output file exists
    - removes the output file
    - asserts that the final output frame has 2 columns
- aggregate by "EXPERIMENT"
    - gets the test Dataset and sets `aggregate_by` to be "EXPERIMENT"
    - resets the test ProcessorJob start and end times
    - runs the smasher pipeline
    - asserts that the output file exists
    - removes the output file
    - asserts that the final output frame has 1 columns
- aggregate by "ALL"
    - gets the test Dataset and sets `aggregate_by` to be "ALL"
    - resets the test ProcessorJob start and end times
    - runs the smasher pipeline
    - asserts that the output file exists
    - asserts that the final output frame has 1 columns

### `test_quant_sf_only_smash`

Tests that quant sf files that are set as non-smashable are handled correctly.
Also uses a real truncated file from prod to test that truncated files are filtered out correctly.

test steps:
- creates prerequisites
    - creates test ProcessorJob
    - creates test Experiment
    - creates test ComputationalResult
    - creates test Organism
    - creates test Sample
    - associates the test Sample to the ComputationalResult and Experiment
    - creates test ComputedFile
        - sets `is_smashable` to False
        - sets `sha1` to match the downloaded file
    - creates second test ComputationalResult
    - creates second test Sample
    - associates the second test Sample to the second ComputationalResult and first Experiment
    - creates second test ComputedFile
        - the file is set to the truncated quant sf file
        - sets `is_smashable` to True
        - sets `sha1` to match the downloaded file
    - creates test Dataset using the two Samples
    - associates the Dataset to the test ProcessorJob
- runs the smasher pipeline
- asserts that the quant sf file associated with the first test Sample exists in the output directory
- validates the metadata
    - asserts that `quant_sf_only` is True
    - asserts that the number of samples is set to 1
    - asserts that the number of experiments is set to 1
    - asserts that the `reason` for why the second test Sample is in `filtered_samples` is correct

### `test_sanity_imports`

Verifies that the dependencies for the smasher pipeline can be imported without any errors.

test steps:
- imports the following packages:
    - `numpy`
    - `scipy`
    - `matplotlib`
    - `pandas`
    - `sklearn`
    - `sympy`

### `test_get_synced_files`

Tests getting a ComputedFile from s3 and from local disk.

test steps:
- creates test ComputationalResult
- creates test ComputedFile using the result
- makes sure that the ComputedFile isn't already saved to the disk
- calls `get_synced_file_path` to get from s3
- asserts that the file that was gotten exists
- calls `get_synced_file_path` to get from local disk
- asserts that the file that was gotten exists

### `test_notify`

Tests that the notify part of the smasher pipeline works.

test steps:
- creates test Dataset
- creates test ProcessorJob and associates it to the Dataset
- creates dummy job context
- calls `smasher._notify()`
- asserts that the job ran without any failures

## Compendia Tests

### `test_call_create`

Tests the `create_compendia` call command.

test steps:
- rerouts stderr and stdout
- asserts that the `create_compendia` call command raises an exception
- resets stderr and stdout

### `test_fetch_create`

Tests the `fetch_compendia` call command.

test steps:
- rerouts stderr and stdout
- asserts that the `fetch_compendia` call command raises an exception
- resets stderr and stdout

## Aggregation Tests

### `test_columns`

Tests that `smashing_utils.get_tsv_columns()` correctly gets the columns from metadata.

test steps:
- creates test metadata
    - contains 2 Samples
    - ArrayExpress Sample
        - second annotation contains special fields
        - `characteristic` which is taken out as separate columns
        - `variable` which is also taken out as separate columns
        - `source` which is skipped in the tsv file
        - `extract` which is also skipped in the tsv file
    - GEO Sample
        - the annotation contains special fields
        - `characteristics_ch1` which is taken out as separate columns
        - all values that are single-element arrays are saved directly in the tsv file
- creates test ProcessorJob
- creates dummy job context -- this might not be needed
- calls `smashing_utils.get_tsv_columns()` to get the columns
- asserts that the there are 22 columns
- asserts that the first column is `refinebio_accession_code`
- asserts that the the following columns exist:
    - refinebio_accession_code
    - cell population
    - dose
    - stimulation
    - serum

### `test_all_samples`

Verifies that the metadata tsv file is created correctly when aggregated by "ALL"

test steps:
- creates test metadata
    - contains 2 Samples
    - ArrayExpress Sample
        - second annotation contains special fields
        - `characteristic` which is taken out as separate columns
        - `variable` which is also taken out as separate columns
        - `source` which is skipped in the tsv file
        - `extract` which is also skipped in the tsv file
    - GEO Sample
        - the annotation contains special fields
        - `characteristics_ch1` which is taken out as separate columns
        - all values that are single-element arrays are saved directly in the tsv file
- creates test ProcessorJob
- creates dummy job context
    - passes in test metadata
    - passes in test Dataset with `aggregate_by` set to "ALL"
- calls `smashing_utils.write_tsv_json()`
- asserts that the tsv file exists
- opens and parses the tsv file
- asserts that the following rows are set properly for the ArrayExpress Sample:
    - cell population
    - dose
    - source
    - detection_percentage
    - extract
    - experiment_accession
- asserts that the following rows are set properly for the GEO Sample:
    - tissue
    - refinebio_organism
    - contact_address
    - experiment_accession
- asserts that there are only two data rows in the tsv file
- removes the tsv file

### `test_experiment`

Verifies that the metadata tsv file is created correctly when aggregated by "EXPERIMENT"

test steps:
- creates test metadata
    - contains 2 Samples
    - ArrayExpress Sample
        - second annotation contains special fields
        - `characteristic` which is taken out as separate columns
        - `variable` which is also taken out as separate columns
        - `source` which is skipped in the tsv file
        - `extract` which is also skipped in the tsv file
    - GEO Sample
        - the annotation contains special fields
        - `characteristics_ch1` which is taken out as separate columns
        - all values that are single-element arrays are saved directly in the tsv file
- creates test ProcessorJob
- creates dummy job context
    - passes in test metadata
    - passes in test Dataset with `aggregate_by` set to "EXPERIMENT"
- calls `smashing_utils.write_tsv_json()`
- asserts that the ArrayExpress tsv file exists
- opens and parses the tsv file
- asserts that the following rows are set properly:
    - refinebio_accession_code
    - experiment_accession
    - cell population
    - dose
    - detection_percentage
- asserts that there is only one data row in the tsv file
- removes the tsv file

### `test_unicode_writer`

Tests that unicode in the metadata is handled correctly

test steps:
- creates test metadata
    - scatters the emoji "😎" randomly around the metadata in keys and values
    - the Sample that's used is an ArrayExpress Sample
- creates test ProcessorJob
- creates dummy job context
    - passes in test metadata
    - passes in test Dataset with `aggregate_by` set to "ALL"
- calls `smashing_utils.write_tsv_json()`
- opens the tsv file and prints out each line with utf-8 encoding
- creates dummy job context
    - passes in test metadata
    - passes in test Dataset with `aggregate_by` set to "EXPERIMENT"
- calls `smashing_utils.write_tsv_json()`
- opens the tsv file and prints out each line with utf-8 encoding
- creates dummy job context
    - passes in test metadata
    - passes in test Dataset with `aggregate_by` set to "SPECIES"
- calls `smashing_utils.write_tsv_json()`
- opens the tsv file and prints out each line with utf-8 encoding

### `test_species`

Verifies that the metadata tsv file is created correctly when aggregated by "SPECIES"

- creates test metadata
    - contains 2 Samples
    - ArrayExpress Sample
        - the organism is set to be `fake_species`
        - second annotation contains special fields
        - `characteristic` which is taken out as separate columns
        - `variable` which is also taken out as separate columns
        - `source` which is skipped in the tsv file
        - `extract` which is also skipped in the tsv file
    - GEO Sample
        - the organism is set to be `homo_sapiens`
        - the annotation contains special fields
        - `characteristics_ch1` which is taken out as separate columns
        - all values that are single-element arrays are saved directly in the tsv file
- creates test ProcessorJob
- creates dummy job context
    - passes in test metadata
    - passes in test Dataset with `aggregate_by` set to "SPECIES"
    - adds `group_by_keys` and sets it to include `homo_sapiens` and `fake_species`
    - adds `input_files` with values for both species set in the `group_by_keys` section
- calls `smashing_utils.write_tsv_json()`
    - this generates 4 files
    - 2 tsv files, 1 for each species
    - 2 json files, 1 for each species
- validates `homo_sapiens` tsv file
    - asserts that the file exists
    - opens the file and checks the rows:
    - asserts that `refinebio_accession_code` is set to `GSM1361050`
    - asserts that `experiment_accession` is set to `GSE56409`
    - asserts that `tissue` is set to `Bone Marrow`
        - this is a GEO specific row
    - asserts that `refinebio_organism` is set to `homo_sapiens`
    - asserts that there's only 1 data row
    - removes the file
- validates the `homo_sapiens` json file
    - asserts that the file exists
    - opens the file and validates the contents:
    - asserts that `species` is set to `homo_sapiens`
    - asserts that the number of Samples is 1
    - asserts that the first Sample's `refinebio_accession_code` is set to `GSM1361050`
    - removes the file
- validates `fake_species` tsv file
    - asserts that the file exists
    - opens the file and checks the rows:
    - asserts that `refinebio_accession_code` is set to `E-GEOD-44719-GSM1089311`
    - asserts that `experiment_accession` is set to `E-GEOD-44719`
    - asserts that `cell population` is set to `IFNa DC`
        - this is an ArrayExpress specific row
    - asserts that `dose` is set to `1 mL`
        - this is an ArrayExpress specific row
    - asserts that `detection_percentage` is set to `98.44078`
    - asserts that there's only 1 data row
    - removes the file
- validates the `fake_species` json file
    - asserts that the file exists
    - opens the file and validates the contents:
    - asserts that `species` is set to `fake_species`
    - asserts that the number of Samples is 1
    - asserts that the first Sample's `refinebio_accession_code` is set to `E-GEOD-44719-GSM1089311`
    - removes the file

### `test_bad_smash`

Verifies that big and small tsv files are handled correctly.

- creates prerequisites
    - creates test ProcessorJob
    - creates test Experiment
    - creates test ComputationalResult
    - creates test Organism
    - creates test Sample
    - adds a SampleAnnotation to the Sample with `{"hi": "friend"}` as the data
    - associates the test Sample to the ComputationalResult and Experiment
    - creates a test ComputedFile with a big tsv file and associates it to the test Sample
    - creates second test Sample
    - associates the test Sample to the first ComputedFile, ComputationalResult, and Experiment
    - creates a second test ComputedFile with a small tsv file and associates it to the second test Sample
    - creates test Dataset with the test Experiment and two Samples
    - associates the Dataset to the test ProcessorJob
- runs the smasher pipeline
- gets the test Dataset
- asserts that `dataset.is_processed` is True

### `test_bad_overlap`

Verifies that a bad tsv file with overlap is handled correctly.

test steps:
- creates prerequisites
    - creates test ProcessorJob
    - creates test Experiment
    - creates test ComputationalResult
    - creates test Organism
    - creates test Sample
    - associates the test Sample to the ComputationalResult and Experiment
    - creates a test ComputedFile with the bad tsv file and associates it to the test Sample
    - creates test Dataset with the test Experiment, two random Samples, and the Sample created above
    - associates the Dataset to the test ProcessorJob
- runs the smasher pipeline
- asserts that the `final_frame` has 4 frames

## Future Tests

The smasher has a lot of tests, so I don't think we need to add any more, but I think that the tests that do exist could be cleaned up.

Some code is unnecessary I think - we should go through and make sure, then clean up anything that doesn't need to be there.

Additionally, most of the tests just verify that the outputs exist or verify that the job ran successfully.
It might be a good idea to go through and figure out if we can do a better job of testing the outputs.
