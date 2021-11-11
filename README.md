# Single end read QuantSeq Fwd pipeline
Based on analysis of Cryptococcus neoformans RNA-seq Gat201 strains 2021.

Data created by Liz Hughes, December 2020. 
Analysis started by Edward Wallace, February 2021
Updated by Sam Haynes, November 2021

# Contents

* src - scripts for data processing
* input_annotation - Cryptococcus genome annotation used for read alignment and counting.
* input_experiment - other input files including sample sheet
* results - output data and results
* results_counts - Results consisting of count data and analyses of those.

## How we made the subsampled data

It is strongly recommended to do a test run on small yet real data - subsampled data - before running on a large dataset.
An example of subsampling fastq files is:

```
gzip -dkc EH_050221_Data/1_S1_R1_001.fastq.gz \
  | head -n 400000 | gzip > EH_050221_Data_subsample/1_S1_R1_001_init100000.fastq.gz

gzip -dkc EH_050221_Data/2_S44_R1_001.fastq.gz \
  | head -n 400000 | gzip > EH_050221_Data_subsample/2_S44_R1_001_init100000.fastq.gz
```


## How to run the pipeline

Download NextFlow, https://www.nextflow.io/.

Download hisat2 aligner, http://daehwankimlab.github.io/hisat2/.

Download the reference genome fasta file https://www.ncbi.nlm.nih.gov/assembly/GCF_000146045.2/. 

Index the reference genome using the hisat2-build tool, move the outputted *.ht2 files into the input_annotation/index folder of this repo.

```
hisat2-build <reference_in>
```
Add the correct gff file with 3'UTRS add (example gff is used in paired pipeline https://github.com/DimmestP/nextflow_paired_reads_pipeline/tree/main/data/shared_data/raw_annotation/reference_genome_annotation)

Edit the parameters in `src/quantseqfwd.nf` so that

* `params.input_fq_dir` points to an input directory containing all of your fastq files 
* `params.output_dir` points to the output directory where you would like all the outputs stored.
* `params.adapters` has the appropriate sequence adapters to remove
* `params.index_prefix` has the start of the *.1.ht2 files created above
* `params.mRNAgff` points to the relevent gff file location
* `params.featuretype` and params.featurename correspond to the right features in the gff file (i.e. primary transcript) 

Then run the command:

```
nextflow run src/quantseqfwd.nf -with-dag flowchart.png -with-report nextflow_report.html
```

The options `-with-dag` and `-with-report` are just to tell you a little more about the output, they are not strictly needed in order to run.

