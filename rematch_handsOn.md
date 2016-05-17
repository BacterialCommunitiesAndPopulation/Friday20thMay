###Identifying targets with ReMatCh

In this hands-on module we will use [ReMatCh](https://github.com/bfrgoncalves/ReMatCh/tree/course_version) to identify the presence/absence of a set of target genes and also use it to identify sequence variants based on read mapping against a reference. Information about its use and parameters can be found at the application's [GitHub page](https://github.com/bfrgoncalves/ReMatCh/tree/course_version).

This data set will consist of 8 sequencing runs of *Campylobacter jejuni* that will be used has query, and 8 target genes that we want to match the reads to. The *C. jejuni* genomes were obtained from different spontaneous Ciprofloxacin resistance mutants of the reference stain 81-176. Details can be found in the following paper: ["Effect of ciprofloxacin exposure on DNA repair mechanisms in *Campylobacter jejuni*" Microbiology (2013), 159, 2513–2523] (http://www.microbiologyresearch.org/docserver/fulltext/micro/159/12/2513_mic069203.pdf?expires=1463468864&id=id&accname=guest&checksum=7D107820B32FBE0B15D4EF33E65D7A90). All isolates p1, p3, p4 and p6 are resistant to Ciprofloxacin. 

To start the analysis, make sure you have loaded the necessary modules and added the necessary software to the PATH.

    sinteractive
    screen -S <job title>
    module load biokit
  
The data that will be used will be available on the shared data folder that you sync in the beginning of the course, at `/wrk/<username>/course_data/shared_all/Friday20thMay/Campylobacter/81-176` 
**NOTE:** Remember with *Ctrl a d* you can always close the *screen* session. You can reattach the screen by typing *screen -r <job title>

###Defining a work directory

**ReMatCh** works by applying a set of programs to map query fastQ sequences against a reference. For that, first it requires the creation of a work directory, where all the necessary files to perform the analysis will be placed. Create the *rematch_analysis* folder and access to it with the command

    cd /wrk/<username>/
    #replace <username> by your user account
    
    mkdir rematch_analysis
    cd rematch_analysis

The next step is to define the **target** sequences to which we will map our reads. Targets are available in the course shared folder, at `/wrk/<username>/course_data/shared_all/Friday20thMay/Campylobacter/rematch_targets`. Copy the targets to your rematch_analysis directory with the command

    cp /wrk/<username>/course_data/shared_all/Friday20thMay/Campylobacter/rematch_targets/listTargetsRematch.fasta .

Now that we have the target genes in the *rematch_analysis* directory, we need to define the **queries**. ReMatCh gives two options to define queries. It can download fastQ files from ENA by using run assessing numbers, or it can perform the analysis on pre-defined data. In this module we will use data that is available in the course shared folder. As so, copy the *81-176_sample_100x* folder available at  `/wrk/<username>/course_data/shared_all/Friday20thMay/Campylobacter/81-176/81-176_sample_100x` to the *rematch_analysis* directory with the command

    cp -r /wrk/<username>/course_data/shared_all/Friday20thMay/Campylobacter/81-176/81-176_sample_100x/. ./
    
    #replace <username> by your user account


ReMatCh also requires a list with the name of the folders where the fastQs are. Since you have copied the contents of *81-176_sample_100x* to your *rematch_analysis* folder, this list already available there. However,  to create a new query list, you can use the command

	   
    ls -d */ | rev | cut -c 2- | rev > queryList.txt
    
    #This will create a list with the name of the folder you want to analyse

Now that we have the rematch_analysis work directory set up, we can run ReMatCh. Make sure you have loaded biokit module and added ReMatCh and BEDtools in the PATH. Then, use the command

    rematch.py -l queryList.txt -d . -picard picard -threads 16 -gatk /wrk/<username>/course_data/shared_all/Friday20thMay/dependencies/GenomeAnalysisTK.jar -r listTargetsRematch.fasta -bowtieBuild -clean -xtraSeq 100
    
    #replace <username> by your user account

**NOTE:** We have used the *-xtraSeq* parameter with a value of 100 since the reference targets we have used had an extra 100 bp on each side. This is to guaranty a good coverage analysis over the entire gene. Otherwise, coverage problems would appear in the beginning and in the end of the sequence. 

After finishing the analysis, a new folder called *rematch_results* will be available inside the folder with the fastQ files.

- **fastq_mappingCheck.tab** - File with mapping statistics for each target. 
- **fastq_sequences.fasta** - File with the consensus sequence produced after the mapping of the reads against the reference. 

####**fastq_mappingCheck.tab:**
- **Sequence** - Target sequence name.
- **Duplication** - Sequence nucleotide frequency with more than 1.25x mean coverage depth.
- **Indel** - Sequence nucleotide frequency with less than 0.1x mean coverage depth.
- **Coverage** - Sequence nucleotide frequency with less than minimum coverage required for base calling (0 - all nucleotide present; 1 - sequence absent).
- **LowSNPsQualityScore** - Presence of SNPs with low mapping quality score (< -qual).
- **SNPCoverage** - Presence of SNPs with low coverage depth (< -cov).
- **SNPMultipleAlleles** - Presence of SNPs with multiple alleles.
- **meanSequenceCoverage** - Mean coverage depth of the entire sequence.
- **meanTrimmedSequenceCoverage** - Mean coverage depth of the sequence without the trimmed ends.

Finally, to get the results of presence/absence of the target genes in the sequencing run, we need to use other ReMatCh command, the *--mergeResults*. It merges all the results available in the work directory.

    rematch.py --mergeResults .
    
    #replace <username> by your user account

It returns a file of presence/absence of the targets (*mergedResults.tab*) and a folder (*consensus_sequences*) with files containing the consensus sequences of the target produced for each sample.

####**mergedResults.tab**

    Samples VirB4   ggt     TetO    gyrA    panC    panB    FucP    panD
    81-176_p_6_MIX_R1_sample_800000 256.867 129.603 166.287 87.08   83.382  95.933  Absent  55.907
    81-176_p_3_MIX_R1_sample_800000 272.206 128.768 229.676 82.031  91.005  95.244  Absent  60.997

- **Absent** - The target was not found after mapping.
- **Presence** - The mean coverage depth is shown when a sequence is present.

To finish our analysis, explore the *gyrA_merged_sequences.fasta* containing the consensus sequences produced by ReMatCh. **How many mutations can be found? Does that produce any modification in the protein sequence?** Use [MView](http://www.ebi.ac.uk/Tools/msa/mview/) and [EMBOSS Transeq](http://www.ebi.ac.uk/Tools/st/emboss_transeq/) to visualize the results.

The most common mutation in GyrA lead to Ciprofloxacin resistance are the amino acid substitution Thr86AIle, and the less frequent Asp90AAsn and Ala70AThr. **Can you detect more mutations at position 86, 70 or 90?**

> Written with [StackEdit](https://stackedit.io/).
