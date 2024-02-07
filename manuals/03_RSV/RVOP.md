#  Illumina Respiratory Virus Oligos Panel
From Illumina:

https://www.illumina.com/products/by-type/sequencing-kits/library-prep-kits/respiratory-virus-oligo-panel.html
```
The Illumina Respiratory Virus Enrichment Kit allows researchers to obtain whole-genome next-generation sequencing (NGS) data for over 40 important respiratory viruses including SARS-CoV-2, RSV, MERS, and influenza A/B viruses. 

Target enrichment through hybrid–capture methods allows for highly sensitive detection without requiring the high read depth necessary with shotgun metagenomic sequencing. Additionally, this method allows for near-complete whole-genome sequence data acquisition of viral targets.
```

The RVOP protocol allows for the simultaneous detection of multiple viral pathogens. It is somewhat of like "targeted metagenomics".


### Get Kraken2 databases
First we need to grab a Kraken2 database to run `Taxprofiler`. You can download the databases here:

https://benlangmead.github.io/aws-indexes/k2

For this tutorial data, let's use a manageable database `Standard-16`. This should be fine most *most* use cases, however the `Standard` database is very large (57Gb), but more accurate in terms of identification.

Download this database into a new folder:
```bash
mkdir k2_standard_16gb_20231009
cd k2_standard_16gb_20231009
# I have provided the db already, but here is how you can download
# wget https://genome-idx.s3.amazonaws.com/kraken/k2_standard_16gb_20231009.tar.gz
tar xzvf k2_standard_16gb_20231009.tar.gz
cd ../
```
### Running Taxprofiler

#### 1. Samplesheet creation
Like nearly all nextflow pipelines we need to create a samplesheet for `taxprofiler`

The format is slightly different than most:
```bash
sample,run_accession,instrument_platform,fastq_1,fastq_2,fasta
2612,run1,ILLUMINA,2612_run1_R1.fq.gz,,
2612,run2,ILLUMINA,2612_run2_R1.fq.gz,,
2612,run3,ILLUMINA,2612_run3_R1.fq.gz,2612_run3_R2.fq.gz,
```
- `run_accession` can be different of same runs, used to compare. In our case we can just make this name up.
- `instrument_platform` either `ILLUMINA` or `OXFORD_NANOPORE`

I've made a small python script to help automate the creation of this samplesheet, called `make_taxprofiler_samplesheet.py`

Usage:  
python make_taxprofiler_samplesheet.py `/path/to/your/fastq/files` `run_accession` `instrument_platform`

It can be used like this to create our samplesheet:
```bash
python make_taxprofiler_samplesheet.py fastqs run1 ILLUMINA
```
The output is a new file called `taxprofiler_samplesheet.csv`.

Look at the contents to make sure it looks right:
```bash
less taxprofiler_samplesheet.csv
```

#### 2. Database sheet creation
`taxprofiler` also needs a `database.csv` file that tells the program which databases are downloaded and where to find the files. 

The `database.csv` file has the following format:
```bash
tool,db_name,db_params,db_path
kraken2,db2,--quick,/<path>/<to>/kraken2/testdb-kraken2.tar.gz
metaphlan,db1,,/<path>/<to>/metaphlan/metaphlan_database/
```

Please make the 

```
tool,db_name,db_params,db_path
kraken2,k2_standard_16gb,,kraken2_databases/k2_standard_16gb_20231009.tar.gz
```

```bash
nextflow run nf-core/taxprofiler \
   -profile docker \
   --input taxprofiler_samplesheet.csv \
   --databases databases.csv \
   --outdir taxprofiler_results  \
   --run_kraken2 \
   --max_cpus 4 \ 
   --max_memory '16.GB'
```


## Run viralrecon pipeline

```bash
python make_samplesheet.py fastqs/
```

```bash
nextflow run nf-core/viralrecon \
    --input samplesheet.csv \
    --platform illumina \
    --outdir RSV_output \
    --fasta reference_genomes/RSV_AB.fasta \
    --protocol metagenomic \
    -profile docker \
    --max_cpus 4 \
    --max_memory 30.GB \
    --skip_nextclade \
    --skip_abacas \
    --skip_pangolin \
    --skip_consensus_plots \
    -resume
```