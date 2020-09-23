### Dependencies

This script needs the [biogl](https://github.com/glarue/biogl) module to function properly. If you use (or can get) `pip`, you can simply do

```python3 -m pip install biogl```

to add the package to a location reachable by your Python installation. 

Otherwise, you can clone the `biogl` repo and source it locally (to run from anywhere, you'll need to add it to your PYTHONPATH environmental variable, a process that varies by OS):

```git clone https://github.com/glarue/biogl.git```

### Usage info

```
usage: palign [-h] [-p PARALLEL_PROCESSES] [-s] [-f OUTPUT_FORMAT]
              [-o OUTPUT_NAME] [-t THREADS] [-e E_VALUE] [-naf] [--clobber_db]
              query subject
              {diamondp,diamondx,blastn,blastp,blastx,tblastn,tblastx}

Align one file against another. Any arguments not listed here will be passed
to the chosen aligner unmodified.

positional arguments:
  query                 query file to be aligned
  subject               subject file to be aligned against
  {diamondp,diamondx,blastn,blastp,blastx,tblastn,tblastx}
                        type of alignment to run

optional arguments:
  -h, --help            show this help message and exit
  -p PARALLEL_PROCESSES, --parallel_processes PARALLEL_PROCESSES
                        run the alignment step using multiple parallel
                        processes (default: 1)
  -s, --single          disable parallel processing (default: False)
  -f OUTPUT_FORMAT, --output_format OUTPUT_FORMAT
                        integer output format for alignment results (default:
                        6)
  -o OUTPUT_NAME, --output_name OUTPUT_NAME
                        filename for results (otherwise, automatic based on
                        input) (default: None)
  -t THREADS, --threads THREADS
                        number of threads per process. Be careful when
                        combining this with multiple processes! (default: 1)
  -e E_VALUE, --e_value E_VALUE
                        e-value threshold to use for search (default: 1e-10)
  -naf, --no_auto_format
                        disable helper operations that auto-format
                        subject/query as needed and build database if not
                        already present (default: False)
  --clobber_db          create new database even if one already exists
                        (default: False)
```

## __[tl;dr]__
`palign` allows for significantly faster sequence alignment (via parallelization in the case of `BLAST`, and via the inherent speed of `DIAMOND`), with some nice convenience functions to boot.

## __[details]__
BLASTing files can take a long time. `palign` speeds up `BLAST+` searches by first breaking the query file into chunks, and then BLASTing those chunks 
against the subject file in parallel.

For reasons that aren't entirely clear, this approach has significant speed gains over using the native `--num_threads` argument in the modern `BLAST+` suite (note that this is _not_ true for DIAMOND; using `--threads` is recommended instead of `-p` in that case).

Additionally, `palign` will auto-create the required database for a given BLAST run, and will format the input files to FASTA if necessary.

Alternatively, `palign` can use the very fast aligner `DIAMOND` instead for the equivalent of `blastp` and `blastx` runs.

## __[example usage]__
To BLAST fileA against fileB using tblastx and 6 separate processes, simply do:

```palign fileA fileB tblastx -p 6 > blast.output```

This will create the BLAST database, and convert filaA and fileB to FASTA files if they aren't already before running the search.

## __[background]__
