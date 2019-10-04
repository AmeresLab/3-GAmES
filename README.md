# 3'GAmES (3´-terminal Gene Annotation from mRNA 3´End Sequencing datasets)

3' GAmES is a pipeline to refine and extend mRNA 3' end annotations using 3' end sequencing datasets. The details of the pipeline are in the figure below. 
![Flowchart 3'GAmES](flowchart.pdf)


## Installation

#### Clone from github

git clone https://github.com/poojabhat1690/refine-pipeline.git
cd pipeline/pre-processing/

All dependencies required for 3' GAmES are provided as 3 separate singularity modules. 
 1. all dependencies 
 2. R and R packages required
 3. slamdunk
Please make sure the singularity version you have is > 3.0. 

## Quickstart

To start, 3' GAmES requrires the dependecies as singularity images and an annotation set from refSeq and ENSEMBL. 

the annotations can be prepared as described below. 

Please make sure to download annotations and format them before running this script (examples for annotations for mouse version mm10 and zebrafish version dr11 are here : ). Steps to obtaining an formatting the annotation for your favourite organism can be found below. 

The script required to run the whole pipeline is beforeMapping.new.sh

beforeMapping.new.sh -a [adapter] -i [input directory] -o [output directory] -g [genome file] -t [threshold for priming sites]
-u [ucscDir] -e [ensemblDir] -m [mode rnaseq p/s/S] -c [condition]
 
 
 -a 3' adapter sequences that has to be removed using cutadapt
 
 -i input directory containing two folders named - quantseq, rnaseq
                   quantseq: contains *.fastq, *.fq.gz, *fq  files. 
                   rnaseq: mapped, sorted and indexed bam files. 
 
 -o Output directory
 
 -t threshold of the number of reads to define a priming site.
 
 -u ucsc directory containing annotations downloaded from ucsc table browser. 
 
 -e ensembl directory containing ensembl annotations ontained from biomart. 
 
 -m mode of counting for RNAseq coverage, derived from bedtools multicov (s: counting on the same strand, 
            p: paired end reads, S: counting on the opposite strand
 
 -c condition of sample (example: timepoint or organism)
                
 ## Prerequisites: 

3Eseq requires intron, exon, 3' UTR annotations from refSeq and ENSEMBL. The following should help guide you through the procedure to get these annotations. 


#### Annotations from refSeq, downloaded from the UCSC table browser manually. 

for 3' UTR annotations from UCSC genome browser (refSeq_mm10_3primeUTR.bed) 
             1. Form UCSC table browser, select:
                a. clade = mammal 
                b. genome = mouse
                c. assembly = Dec. 2011 (GRCm38/mm10)
                d. group = genes and gene prediction
                e. track = refSeq genes
                f. table = refGene
              2. select bed format and 3' UTR. 
      
   Also download the intron, exon and refFlat annotations from the ucsc genome browser. Further processing has been done using the script : pipeline/pre-processing/getAnnotations.Rmd
     


 ### Annotations from ENSEMBL:
 Can be downloaded as a gff file from ensembl and processed. 

 #### Annotations from ENSEMBL retreived from biomaRt
 
Ensembl annotations were retrieved from biomart, using RStudio using the script pipeline/pre-processing/getAnnotations.Rmd
processing ensembl annotations 
1. get only protein coding genes based on transcript biotype : allTranscripts_proteinCoding.txt .
2. get protein coding genes with annotated 3' UTRs : proteinCoding_annotatedUTRs.txt
3. protein coding genes with un annotated 3' UTRs : proteinCoding_UnannotatedUTRs.txt
        
An example of the annotations required are provided in testdata/mm10. As of now 3' GAmES works with ensembl and refSeq annotations.
 


 
## Output description
The ouput and all intermediate files are organized in the follwing folders:

#### final90percent :
 1. ends_greater90percent_intergenic_n100 :  contains the high condience mRNA 3' ends 
 2. allAnnotations.bed :  250nt counting windows (overlapping counting windows merged), used to count quantSeq reads.
 3. countingWindows_transcriptionalOutput.bed : genomic loci to filter multimappers using SLAMdunk. These include all counting windows + all 3' UTRs + all extended counting windows. 
 4. onlyIntergenic_90percent_n100: list of the intergenic counting windows created using presence of continuous RNAseq signal.

#### polyAmapping_allTimepoints : contains raw files of the pre-processing steps
 1. including adapter trimmed, poly(A) tail filtered fastq files, mapped files
 2. priming sites
 3. priming sites overalapping with different annotations (ENSEMBL 3' UTRs, refSeq 3' UTRs, ENSEMBL introns, ENSEMBL exons, nonOverlapping). 

#### PASplots :
 contains nucleotide profiles for priming sites overlapping with annotations, sparated by presence or absence of the poly A signal (PAS) and separated by downstream genomic A content. 

#### coverage : 
 contains list of priming sites (filterd for low genomic A content), that overlap with un-annotated regions supported by RNAseq. 
        


