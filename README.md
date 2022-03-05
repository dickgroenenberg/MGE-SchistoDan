# MitoGeneExtractor

MitoGeneExtractor can be used to conveniently extract mitochondrial genes from sequencing libraries.
Mitochondrial reads are often found as byproduct in sequencing libraries, in particular in hybrid enrichment libraries.

## Prepared Snakemake workflows

[Manual for using existing Snakemake workflows to extract genes with MitoGeneExtractor.](Use-Snakemake-manual.md)

## Authors of the publication:
- Marie Brasseur, ZFMK, Bonn, Germany
- Jonas Astrin, ZFMK, Bonn, Germany
- Matthias Geiger, ZFMK, Bonn, Germany
- Christoph Mayer, ZFMK, Bonn, Germany

## Authors of the software project:
- Christoph Mayer, ZFMK, Bonn, Germany: MitoGeneExtractor program.
- Marie Brasseur, ZFMK, Bonn, Germany: Snakemake pipeline and anylses for publication.

## Reference: Please cite when using MitoGeneExtractor
Brasseur ... 2022 ...




## How MitoGeneExtractor works:
MitoGeneExtractor aligns all given nucleotide sequences against a
protein reference sequence to obtain a multiple sequence alignment. The
recommended use case is to extract mitochondrial genes from sequencing
libraries, e.g. from hybrid enrichment libraries sequenced on the
Illumina platform. The individual alignments are computed by calling the
exonerate program. 

Exonerate is a very efficient alignment program which allows to align protein and nucleotide sequences.
Nucleotide sequences which cannot be aligned to the protein reference will not be included in
the output. Exonerate should be able to align several 100k short reads in a few
minutes using a single CPU core. Therefore, this approach can be used for projects of any size.

## Supported Platforms:
MitoGeneExtractor: All platforms are supported for which a C++ compiler is available.
It can be compiled by users without root previliges.
This program has been tested on Linux, HPC Unix platforms and MacOS. It should be possible in principle to compile it on windows. However, there is not support from the authors for this platform.

Snakemake workflow: Linux, HPC Unix platforms and MacOS, (Windows - without support).

## Installation:
MitoGeneExtractor requires either an Exonerate output file as input, or the Exonerate program has to be installed, so that such an the exonerate output can be generated by MitoGeneExtractor. As of writing this, the most recent version of the Exonerate program is 2.4, which is avaiable e.g. here:
https://github.com/nathanweeks/exonerate

On MacOS you will need to install the command line developer tools. For a manual how to do this, see 
https://osxdaily.com/2014/02/12/install-command-line-tools-mac-os-x/.


To install MitoGeneExtractor, do one of the following:
- Clone the MitoGeneExtractor project to your computer. The link can be found by klicking on the "Code" pulldown menu at the top of this page.
- Download the zipped project folder and extract the folder. The link can be found by klicking on the "Code" pulldown menu at the top of this page.  

Now enter the MitoGeneExractor-vx.x folder on the command line and run the make program by typing "make" and hitting return. The make program should be preinstalled on all Linux distributions. On MacOS it is included in the command line developer tools (see above). 
This will generate an executable called MitoGeneExtractor. Either copy this to a directory in your path, to reference it by its full path on the command line.

PUT THIS SOMEWHERE ELSE:
In rare cases, the Exonerate program  quits/crashes unexpectedly.  Interestingly, when rerunning Exonerate, there is a good chance that it will not abort. Why rerunning Exonerate can be successful is a mystery. It has been checked that successful runs always create the same expected output. MitoGeneExtractor can identify, if Exonerate aborted the run and will try upto 10 times by rerunning Exonerate.  The log output of MitoGeneExtractor will report the run count for the succesfull run.

## Get help and a full list of command line options:
Type MitoGeneExtractor -h (if MitoGeneExtractor is in your path) or Path-to-MitoGeneExtractor/MitoGeneExtractor -h to list all command line options of MitoGeneExtractor. 

In order to run MitoGeneExtractor you need your input sequences in fasta format as well as an amino acid reference sequence of your mitochondrial gene of interest. Example references are included in the reference-sequence-examples folder of this project. For the COI gene, one can specify as a reference the amino acid sequence of the barcode region, or if intended, the full COI sequence. If the full COI sequence shall be extracted, we suggest to create a reference specific for your taxonomic group, since the COI gene can differ considerably in the first and last few amino acids for specific groups with respect to references designed for larger groups. For the barcode region of COI this is normally not a problem. In principle all mitochondrial genes can be extracted with this approach.

## Example analysis:
Let us assume that you want to extract sequences from a short read file called input-reads.fas and that your amino acid reference file is called COI-whole-xxx.fas. Files with these names are found in the "example-analysis" folder.

Run the example analysis with the command (see run-analysis.sh):
xxxx


## Command line options:
A full list of the command line options is avaiable when typing
MitoGeneExtractor -h

**-d, --dna_sequences_file:** Name (potentially including the path) of the nucleotide sequence file in the fasta format. Sequences are expected to be unaligned without gaps. Typcially, these are short or long reads. (Required) 

**-p, --prot_reference_file:** Protein sequence file in the fasta format. This is the sequence used to align the reads against. File is expected to have exactly one reference sequence. (Required) 

**-o** Name of alignment output file. (Required) 

**-V, --vulgar_file:** Name of exonerate vulgar file. If the specified file exists, it will be used for this analysis. If it does not exist, MitoGeneExtractor will run exonerate in order to create the file. The created file will then be used to proceed. If no file is specified with this option, a temporary file called tmp-vulgar.txt will be created and removed after the program run. In this case a warning will be printed to the console, since the vulgar file cannot be used again. (Not required, but recommended) 

**-e, --exonerate_program:** Name of the exonerate program in the system path OR the path to the exonerate program including the program name. Default: exonerate. (Not required)

**-n, --numberOfBpBeyond:** Specifies the number of base pairs that are shown beyond the exonerate alignment. A value of 0 means that the sequence is clipped at the point the exonerate alignment ends. Values of 1 and 2 make sense, since exonerate does not consider partial matches of the DNA to the amino acid sequence, so that partial codons would always be clipped, even if the additional base pairs would match with the expected amino acid. Values >0 lead to the inclusion of sequence segments that do not align well with the amino acid sequence and have to be treated with caution. They might belong to chimera, numts, or other problematic sequences. Larger values might be included e.g. if problematic sequences with a well matching seed alignment are of interest. CAUTION: Bases included with this option might not be aligned well or could even belong to stop codons! They should be considered as of lower quality compared to other bases. Bases that are added with this option are added as lower case characters to the output alignment file. A sequence coverage of bases not belonging to these extra bases can be requested with the --minSeqCoverageInAlignment_uppercase option. Default: 0. Type: integer. (Not required)

**-c, --consensus_file:** If this option is specified, a consensus sequence of all aligned reads is written to the file with the specified name. Normally, this is the intended output. Default: No consensus is written, since no good default output file is known. (Not required)

**-t, --consensus_threshold:** This option modifies the consensus threshold. Default: 0.7 which corresponds to 70%. Type: Decimal number. (Not required)

**-D, --includeDoubleHits:** Include reads with two alignment results found by exonerate. Default: No. (Not required)

**-G, --includeGap:** Include reads which aligned with a gap. Default: No. (Not required)

**-F, --includeFrameshift:** Include reads which aligned with a frameshift. Default: false. The number of reads excluded is listed at the end of the output. If this number is particularly large, the problem should be investigated. Default: No. (Not required)

**-f, --frameshift_penalty** The frameshift penalty passed to exonerate. The option value has to be a negative integer. Default: -9. More negative values lead to lower scores and by this can have the following effects: (i) hit regions are trimmed since trimming can lead to a better final alignment score, (ii) they can also lead to excluding a read as a whole if the final score is too low and trimming does lead to a higher score. The default of the exonerate program is -28. A value of -9 (or other values less negative than -28) lead to more reads in which the best alignment has a frameshift. In order to remove reads that do not align well, one can use a less negative value for the frameshift penalty and then exclude hits with a frameshift, see -F option). (Not required)

**-C, --genetic_code:** The number of the genetic code to use in exonerate, if this step is required. Default: 2, i.e. vertebrate mitochondrial code. (Type: integer). [See genetic code list at NCBI.](https://www.ncbi.nlm.nih.gov/Taxonomy/Utils/wprintgc.cgi). (Not required but recommended). ** A misspecification of the genetic code leads to unusable results. Make sure the default is correct, or specify the genetic code.**

**-r, --relative_score_threshold:** Specified the relative alignment score threshold for exonerate hits to be considered. The relative score is the score reported by exonerate divided by the alignment length. Default 0. Reasonable thresholds are between 1 and 2. Type: decimal number (Not required)

**--minSeqCoverageInAlignment_total:** Specifies the absolute value of the minimum alignment coverage for computing the consensus sequence. For the coverage, all nucleotides count, also those lower case nucleotides that have been added beyond the exonerate alingmnet region. Default: 1. Increasing this value increases the number of unknown nucleotides in the consensus sequence. Type:integer, (Not required)

**--minSeqCoverageInAlignment_uppercase:** Specifies the absolute value of the minimum alignment coverage for computing the consensus sequence. As coverage, only upper case nucleotides are taken into account, i.e. no nucleotides are counted that have been added beyond the exonerate alignment region. Bases beyond the exonerate alignment are added with the -n or --numberOfBpBeyond option. If no bases are added beyond the exonerate alignment (default), the effect of this option is identical to the minSeqCoverageInAlignment_total option. Default: 1. Increasing this value increases the number of unknown nucleotides in the consensus sequence. Type:integer, (Not required)

**-s, --minExonerateScoreThreshold:** The score threshold passed to exonerate to decide whether to include or not include the hit in the output. Typ: integer, optional parameter. Type:integer, (Not required)

**--verbosity:** Specifies how much run time information is printed to the console. Values: 0: minimal output, 1: important notices, 2: more notices, 3: basic progress, 4: detailed progress, 50-100: debug output, 1000: all output. Type:integer, (Not required)





