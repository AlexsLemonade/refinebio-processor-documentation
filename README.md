# refinebio-processor-documentation
For tracking documentation of the methodology behind our processor tests: https://github.com/AlexsLemonade/refinebio/issues/2470

## New Required Tests

In addition to improving the existing tests as listed in the links below, we also would like to run a few tests on a weekly basis to make sure things aren't drifting without us knowing.
These can be configured to run as cron jobs on the foreman server.
These should check:
- that brainarray has not drastically changed genes since the last time it was run
- that a newly generated QN reference file is similar enough to the latest version currently being used.

## Test Documentation Links

### [Array Express](./array_express.md)

### [Compendia](./compendia.md)

### [Create Quantpendia](./create_quantpendia.md)

### [GEO](./geo.md)

### [NO OP](./no_op.md)

### [QN Reference](./qn_reference.md)

### [Salmon](./salmon.md)

### [Smasher](./smasher.md)

### [Transcriptome Index](./transcriptome_index.md)
