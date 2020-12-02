# Salmon

## Overall Tests

### `test_salmon`

Tests the entire salmon pipeline

test steps:
- calls `prepare_job`
    - creates all prerequisites for the salmon pipeline to be run including:
        - ProcessorJob
        - Sample
        - OriginalFiles
        - OrganismIndex
- passes the created base job to `salmon.salmon()` which runs the entire pipeline
- asserts that the `job.success` is true
- asserts that the `sample.is_processed` is false
- asserts that the index type for the organism is 'TRANSCRIPTOME_SHORT'

### `test_no_salmon_on_geo`

Confirms that salmon won't be run on GEO data

test steps:
- calls `prepere_job`
    - creates all prerequisites for the salmon pipeline to be run including:
        - ProcessorJob
        - Sample
        - OriginalFiles
        - OrganismIndex
- creates copies for all original files since they will be cleaned up when the job fails
- edits the sample created in `prepare_job` to have a `source_database` of GEO
- calls `salmon.salmon()` on the base job to run the entire pipeline
- asserts that job context is created
- asserts `job.success` is false
- asserts the `job.failure_reason` is correct
- asserts that the job is not set to retry
- asserts that the original files got cleaned up properly

### `test_salmon_dotsra`

Tests the entire pipeline with a sra file

test steps:
- calls `prepare_dotsra_job`
    - creates all prerequisites for the salmon pipeline to be run including:
        - ProcessorJob
        - Sample
        - OriginalFiles
        - OrganismIndex
    - uses an RSA file when creating the OriginalFile
- passes the created base job to `salmon.salmon()` which runs the entire pipeline
- asserts that the `job.success` is true

### `test_salmon_dotsra_bad`

Confirms that the job fails with an SRA file that doesn't exist

test steps:
- calls `prepare_dotsra_job` with a file that doesn't exist
    - creates all prerequisites for the salmon pipeline to be run including:
        - ProcessorJob
        - Sample
        - OriginalFiles
        - OrganismIndex
    - uses an RSA file when creating the OriginalFile
- passes the created base job to `salmon.salmon()` which runs the entire pipeline
- asserts that `job_context` was created
- asserts that the `job.success` is false

### `test_salmon_quant_one_sample_double_reads`

Tests salmon quant on a single sample with two original files (double reads)

test steps:
- calls `prepare_organism_indices`
    - creates a dummy OrganismIndex that can be used in the salmon pipeline
- creates the test sample and experiment
- adds an additional fake sample to the experiment to prevent tximport from running on the experiment
    - tximport will only run after salmon quant is run on every sample associated with the experiment
- creates two original files and adds them to the test sample
- calls `salmon._prepare_files` to prepare the original files before running salmon quant
- calls `check_salmon_quant` to run salmon quant and tximport
    - asserts quant output file is made
    - asserts quant output file strongly correlates to reference file
    - the files are strongly correlated if both columns #3 and #4 (zero-indexed) of the two files have a spearman correlation >= 0.99.
- asserts that the test experiment is not ready for tximport yet

### `test_salmon_quant_two_samples_single_read`

Tests salmon quant on two samples each with one original file (single read) that belong to the same experiment

test steps:
- calls `prepare_organism_indices`
    - creates a dummy OrganismIndex that can be used in the salmon pipeline
- creates the test experiment
- creates sample1 and og_file_1, associates the two, and adds the sample to the experiemnt
- creates sample2 and og_file_2, associates the two, and adds the sample to the experiemnt
- calls `salmon._prepare_files()`, passing in  og_file_1
- calls `check_salmon_quant` to run salmon quant and tximport
    - asserts quant output file is made
    - asserts quant output file strongly correlates to reference file
    - the files are strongly correlated if both columns #3 and #4 (zero-indexed) of the two files have a spearman correlation >= 0.99.
- asserts that the experiment is not ready for tximport yet - salmon qunat still needs to be run on sample2
- asserts that there are no tximport outputs yet
- calls `salmon._prepare_files()`, passing in  og_file_2
- cleans up tximport data from previous runs if it exists
- calls `check_salmon_quant` to run salmon quant and tximport
    - asserts quant output file is made
    - asserts quant output file strongly correlates to reference file
    - the files are strongly correlated if both columns #3 and #4 (zero-indexed) of the two files have a spearman correlation >= 0.99.
    - at this point tximport should be run
- asserts that the rds file (tximport output file) has been made
- runs test_tximport.R to verify the output file
- assert that two output files were created
- assert that the two outut files exist

### `test_get_tximport_inputs`

Tests that tximport only considers RNA-Seq samples from GEO

test steps:
- creates a test experiment
- creates two test samples and adds them the the experiment
    - both samples have GEO as `sample.source_database` and RNA-SEQ as `sample.technology`
- creates a computational result and computed file for each sample and associates them
- calls `salmon.get_tximport_inputs()`, passing in sample1
    - the result of this is a dictionary of experiments mapped to their quant.sf files
- asserts that the returned dictionary is empty

## SalmonTools Tests

### `test_double_reads`

Tests SalmonTools on a sample with two OriginalFiles (double reads)

test steps:
- creates dummy `job_context` with both `input_file_path` and `input_file_path_2`
- creates dummy sample and adds it to the `job_context`
- runs SalmonTools
- asserts `job.success` is true
- unpacks the output file
- asserts that both output files' checksums match with the expected output files'

### `test_single_read`

Tests SalmonTools on a sample with only one OriginalFile (single read)

test steps:
- creates dummy `job_context` with just `input_file_path`
- creates dummy sample and adds it to the `job_context`
- runs SalmonTools
- asserts `job.success` is true
- unpacks the output file
- asserts that the output file's checksum matches with the expected output files'

## DetermineIndexLength Tests

### `test_salmon_determine_index_length_single_read`

Tests that the correct index length is calculated when a sample has one OriginalFile (single read)

test steps:
- calls `prepare_job`
    - creates all prerequisites for the salmon pipeline to be run including:
        - ProcessorJob
        - Sample
        - OriginalFiles
        - OrganismIndex
- creates a dummy `job_context` with one OriginalFile
- calls `salmon._prepare_files` to prepare the OriginalFile before determining the index length
- calls `salmon._determine_index_length`
- asserts that the raw index length is correctly set
- asserts that the index length is correctly set

### `test_salmon_determine_index_length_double_read`

Tests that the correct index length is calculated when a sample has two OriginalFiles (double read)

test steps:
- calls `prepare_job`
    - creates all prerequisites for the salmon pipeline to be run including:
        - ProcessorJob
        - Sample
        - OriginalFiles
        - OrganismIndex
- creates a dummy `job_context` with both OriginalFiles
- calls `salmon._prepare_files` to prepare the OriginalFiles before determining the index length
- calls `salmon._determine_index_length`
- asserts that the raw index length is correctly set
- asserts that the index length is correctly set

## RuntimeProcessor Tests

### `test_tximport`

Tests the processor for tximport

test steps:
- asserts that thre are no processors hosted yet
- creates a new processor
- validates that the processor was set up correctly
    - asserts name is correct
    - asserts `SYSTEM_VERSION` is correct
    - asserts that the docker image is correct
    - asserts the os distribution is correct
    - asserts the os package is correct
    - asserts the pip package is correct
    - asserts the Bioconductor and tximport packages are correct
- asserts that there is only one processor in the runtime environment

### `test_salmon_quant`

Tests the processor for salmon quant

test steps:
- asserts that thre are no processors hosted yet
- creates a new processor
- validates that the processor was set up correctly
    - asserts the name is correct
    - asserts that salmon is the correct version

### `test_salmontools`

Tests the processor for SalmonTools

test steps:
- asserts that thre are no processors hosted yet
- creates a new processor
- validates that the processor was set up correctly
    - asserts the name is correct
    - asserts that salmon is the correct version

### `test_exception_handler`

Tests that the exception handler properly handles a failed job

test steps:
- sets an invalid `proc_key`
- creates a dummy `job_context`
- tries to find a processor for the invalid `proc_key` which raises an exception
- calls `utils.handle_processor_exception` with the `proc_key` and `job_context`
- asserts that the job's success is false
- asserts that the job's failure reason is set correctly

### `test_salmontools_with_bad_processor`

Tests that trying to run SalmonTools with a bad processor is handled correctly

test steps:
- creates test directory
- creates dummy `job_context`
- creates dummy Sample
- sets the yml file to one that doesn't exist
- tries to run SalmonTools
- asserts that the job's success is false
- asserts that the job's failure reason is set correctly
- resets the yml file to the correct file

## EarlyTximport Tests

### `test_early_tximport`

Tests that running tximport early works correctly

test steps:
- creates lists of accession codes for incomplete and complete samples
    - incomplete samples has 5 accession codes
    - complete samples has 20 accessioncodes
    - By having 20/25 samples complete, we're just past both the numerical and percent cutoffs for running tximport early
- calls `run_tximport_at_progress_point`
    - creates a dummy `job_context` that could be passed into the salmon pipeline and have all steps related to tximport run successfully.
    - creates a test experiment
    - creates test samples and their associations for incomplete and complete samples and adds them to the test experiment
    - runs tximport
- asserts that 'tximported' is in the final `job_context`
- for each sample in the completed samples list:
    - check that the database associations were made properly 
    - check that the tpm and rds files were created for the sample
    - check that the sample was processed
- for each sample in the incomplete sample list:
    - assert that it was ignored in the pipeline by verifying that the sample has no computed files

### `test_version_filter`

Tests that we don't run tximport on old salmon versions

test steps:
- creates lists of accession codes for incomplete and complete samples
- calls `run_tximport_at_progress_point` and passes in salmon version 0.9.1
    - creates a dummy `job_context` that could be passed into the salmon pipeline and have all steps related to tximport run successfully.
    - creates a test experiment
    - creates test samples and their associations for incomplete and complete samples and adds them to the test experiment
    - runs tximport
- asserts that 'tximported' is not in the final `job_context`
    - 'tximported' should not get added because the salmon version is out of date

### `test_tximport_percent_cutoff`

Tests logic for determining if tximport should be run early.

test steps:
- creates lists of accession codes for incomplete and complete samples
    - incomplete samples has 6 accession codes
    - complete samples has 20 accessioncodes
    - By having 20/26 samples complete, we're past the numerical, but not the percent cutoff for running tximport early
- calls `run_tximport_at_progress_point`
    - creates a dummy `job_context` that could be passed into the salmon pipeline and have all steps related to tximport run successfully.
    - creates a test experiment
    - creates test samples and their associations for incomplete and complete samples and adds them to the test experiment
    - runs tximport
- asserts that 'tximported' is not in the final `job_context`
    - since we're not passed the percent cutoff, we shouldn't run tximport
- for each sample in the incomplete sample list:
    - assert that it was ignored in the pipeline by verifying that the sample has no computed files

### `test_tximport_numerical_cutoff`

test steps:
- creates lists of accession codes for incomplete and complete samples
    - incomplete samples has 5 accession codes
    - complete samples has 19 accessioncodes
    - By having 19/24 samples complete, we're past the percent, but not the numerical cutoff for running tximport early
- calls `run_tximport_at_progress_point`
    - creates a dummy `job_context` that could be passed into the salmon pipeline and have all steps related to tximport run successfully.
    - creates a test experiment
    - creates test samples and their associations for incomplete and complete samples and adds them to the test experiment
    - runs tximport
- asserts that 'tximported' is not in the final `job_context`
    - since we're not passed the percent cutoff, we shouldn't run tximport
- for each sample in the incomplete sample list:
    - assert that it was ignored in the pipeline by verifying that the sample has no computed files

### `test_missing_computed_file`

Tests that tximport will ignore computed files that were not uploaded to S3

test steps:
- creates lists of accession codes for incomplete and complete samples
    - incomplete samples has 5 accession codes
    - complete samples has 20 accessioncodes
- calls `create_tximport_job_context`
    - creates a dummy `job_context` that could be passed into the salmon pipeline and have all steps related to tximport run successfully.
    - creates a test experiment
    - creates test samples and their associations for incomplete and complete samples and adds them to the test experiment
- gets one of the complete samples and sets its computed file's s3 related fields to None 
- calls `run_tximport_for_job_context` to run tximport
- asserts that 'tximported' is not in the final `job_context`
    - since one of the complete samples "has computed files that were not uploaded to s3" it got filtered out
    - we now have 19/24 samples complete so we miss the numerical cutoff for running tximport early
- for each sample in the incomplete sample list:
    - assert that it was ignored in the pipeline by verifying that the sample has no computed files
