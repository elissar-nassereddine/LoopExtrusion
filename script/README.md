
# Alignment workflow on our NGS data

## ChIP-SEQ workflow with ES input

### Files

  - `ChIP-Seq/input/Snakefile` : main script for snakemake, call all
    RULES and config file.
  - `ChIP-Seq/input/config.json` : config file which contain the list
    and location of all ChIP-SEQ and references files.
  - `ChIP-Seq/input/RULES/full_mapping_using_ctrl_genome.rules` : All
    rules needed to process ChIP-SEQ files with two inputs.

### Minimal configuration

  - Open config file (`config.json`) and type a directory where your
    data will be written (default `PROCESSED`).
  - Indicate the directory were the raw files (`fastq` format) are
    stored (default `RAW`).
  - Indicate the location, the name and the extension of the reference
    genome:
      - in `"FILES"` the first and second reference genome, usually
        `["hg19","mm10"]`.
      - in `"FILES"` the extension of the two files `.fa`
  - In `"INPUT"` and `"EXPERIMENT"`, the list of the name of the files
    (fastq file without the extension), and the corresponding
        genome:
      - `"INPUT":{"input_mouse":{"GENOME":1},"input_human":{"GENOME":0}}`
        where `"input_mouse"` is the name file and `"GENOME":1` the
        index of the genome in the `"GENOME":{"FILES"}` list (begin at
        `0`).
      - `"EXPERIMENT":{"experiment_human":{"GENOME":1,"CTRL":"input_mouse","EXPERIMENT":"input_human"}}`
        where :
          - `"experiment_human"` is the name file.
          - `"GENOME":1` the index of the genome in the
            `"GENOME":{"FILES"}` list (begin at `0`).
          - `"CTRL":"input_mouse"` the name of the file who correspond
            to the input control (mouse).
          - `"EXPERIMENT":"input_human"` the name of the file who
            correspond to the input experiment (human).

**Notes:** `bamCoverageScaleFactor` will use `CTRL` and `EXPERIMENT`
input files to normalize the reads count of each samples, given the
formula
:

<img src="https://render.githubusercontent.com/render/math?math=\frac{(input_{ctrl}\times reads_{exp})}{(input_{exp}\times reads_{ctrl})}" height="50" width="250">

### Run the workflow

Cmd : `snakemake -s Snakefile`.

### Rulegraph

![rulegraph](ChIP-Seq/input/rulegraph.svg)

## ChIP-SEQ workflow

### Files

  - `ChIP-Seq/classic/Snakefile` : main script for snakemake, call all
    RULES and config file.
  - `ChIP-Seq/classic/config.json` : config file which contain the list
    and location of all ChIP-SEQ and references files.
  - `ChIP-Seq/classic/RULES/fastqc.rules` : All cmd from FastQC program
    (quality control)
  - `ChIP-Seq/classic/RULES/bwa.rules` : All cmd from bwa (alignment to
    reference genome)
  - `ChIP-Seq/classic/RULES/samtools.rules` : All cmd from samtools
    (sam/bam manipulation)
  - `ChIP-Seq/classic/RULES/bigwig.rules` : R script call by snakemake
    to compute coverage in BigWig format.

### Minimal configuration

  - Open config file (`config.json`) and type a directory where your
    data will be written (default `PROCESSED`).
  - Indicate the directory were the raw files (`fastq` format) are
    stored (default `RAW`).
  - Indicate the location, the name and the extension of the reference
    genome (default `""`,`female.hg19`,`.fa`).

### Run the workflow

Cmd : `snakemake -s Snakefile`.

### Rulegraph

![rulegraph](ChIP-Seq/classic/rulegraph_chipseq.png)

## 4C-SEQ workflow

### Files

  - `4Cseq/4C-seq_process_trans.snakefile` : main script for snakemake,
    call all RULES and config file.
  - `4Cseq/create_matrix_count.R` : script to get the 4C-seq reads count
    matrix for each bins.
  - `4Cseq/demultiplex.py` : demultiplex each viewpoint for each input
    file.
  - `4Cseq/favorite_gaelle_smooth.R` : smooth bigwig.
  - `4Cseq/make_frags.R` : create the bed file for MboI and NlaIII motif
    position.
  - `4Cseq/normFrags.R` : normalize 4C-SEQ read counts.
  - `4Cseq/smoothData_adapted.R` : smooth bedGraph.
  - `4Cseq/EXCLUDES` : folder with position to exclude for each
    viewpoint.
  - `4Cseq/PRIMERS` : viewpoints and sequence primers.

### Minimal configuration

Open `4Cseq/4C-seq_process_trans.snakefile` and change :

  - `RAW_PATH` : path where the fastq files are located.
  - `DEMULTIPLEX_PATH` : path were the fastq fiels will be
    demultiplexed.
  - `OUT_PATH` : where the output files will be written.
  - `EXTENSION` : fastq extension.
  - `GENOME` : genome location.
  - `PRIMERS_PATH` : primers folder location.
  - `EXCLUDES_PATH` : folder location of position to exclude for each
    viewpoint.
  - `PRIMERS_FILES` : list of primer files located in `PRIMERS_PATH`.
  - `INFO_FASTQ` : for each input file, specify :
      - `"primers"` : the corresponding primer file.
      - `"viewpoint"` : the corresponding viewpoint from `PRIMERS`
        dictionary.
      - `"to.exclude"` : the corresponding region to exclude from
        `EXCLUDES` dictionary.

### Run the workflow

Cmd : `snakemake -s 4C-seq_process_trans.snakefile`.

### Rulegraph

![rulegraph](4Cseq/rulegraph.svg)
