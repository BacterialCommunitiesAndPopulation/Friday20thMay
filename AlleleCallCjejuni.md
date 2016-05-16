
## Allele call using Campylobacter jejuni data ##

For this hands-on module we will use [chewBBACA](https://github.com/mickaelsilva/chewBBACA) for performing the allele call on a dataset of Campylobacter jejuni , from the article 
"[Tracing isolates from domestic human Campylobacter jejuni infections to chicken slaughter batches and swimming water using whole-genome multilocus sequence typing.](http://www.ncbi.nlm.nih.gov/pubmed/27041390)".

[chewBBACA](https://github.com/mickaelsilva/chewBBACA) 's main developer is Mickael Silva (https://github.com/mickaelsilva).

This dataset will have genome assemblies from 17 *C .jejuni* isolates and we will work directly on the assemblies provided.

For the allele call you will need :  

 1. a cg/wgMLST schema - one fasta file per loci
 2. assemblies in fasta format

The schema that we will use is available from PubMLST website (http://pubmlst.org/) developed by Keith Jolley (Jolley & Maiden 2010, BMC Bioinformatics, 11:595) and sited at the University of Oxford. The development of that website was funded by the Wellcome Trust.

The description of how the  *Campylobacter sp*. cgMLST schema was created can be found at at: http://pubmlst.org/campylobacter/info/cgMLST.shtml

0) Make sure you have your and software  available and on the path. For Taito run

    module load biokit

1) create a directory for the cgMLST analysis:

    mkdir /wrk/<username>/campy_cgMLST

 2) Copy the Schema files and the Assemblies from the shared data folder to this directory. 
		
    cd campy_cgMLST
    mv ../course_data/shared_all/Friday20thMay/Campylobacter/Assemblies/ .
    mv ../course_data/shared_all/Friday20thMay/Campylobacter/CampiPubmlstSchema/ .
  
  Note: The schema dir also includes another dir (*short*) that includes the auxiliary files for the allele call. These are Alleles for each loci that are used for the allele call and the BSR pre-calculated for those alleles. When creating a new schema you have to create this dir. During the working groups exercise you will have an example of the creation of a schema.
   
  3) Create a list of the assembly files with the full path to each to the files

	cd Assemblies/
	ls *.fasta | xargs -I {} sh -c 'echo $(pwd)/{}' > listGenomes.txt
	cd ..
	
4) Create a list of the the loci to be used on the allele call. We will use all the loci available on the schema so we will use the same command as in the last step:

	cd CampiPubmlstSchema/
	ls *.fasta | xargs -I {} sh -c 'echo $(pwd)/{}' > cgMLSTLoci.txt

5) you can check the number of loci in the schema by running the following command:
	
	cat cgMLSTLoci.txt | wc
	cd ..
	 
you should have 1343 loci in the Oxford Campylobacter cgMLST v1.

6) Make sure that chewBBACA scripts are on the PATH. (Substitute < username > by your Taito user name)

    export PATH="/homeappl/home/<username>/appl_taito/bact_pop_course/chewBBACA/allelecall:$PATH"

7) run the allele call

     alleleCalling_ORFbased_protein_main3_local.py -i Assemblies/listGenomes.txt -g CampiPubmlstSchema/cgMLSTLoci.txt -o AlleleCall_ST230.txt -p prodigal

8) The allele call should take around 20 mins to run. While the allele call is running , go to the NCBI genomes website (http://www.ncbi.nlm.nih.gov/genome/) and search Campylobacter jejuni. Download 3 genomes (your choice) in fasta format to your  /wkr/< username >/campy_cgMLST directory to include in our allele call later on.

9) When the allele call finishes, three files will be present on the output directory:    

 - AlleleCall.txt  - contains the allele call annotated with extra information for each allele 
 - contigsInfo.txt - reports the contig position and orientation for each of the alleles  called , for all the strains in the run
 - statistics.txt - summary statistics on the run

10) Open the  summary statistics file:

    less statistics.txt
 
 You should have something like this:
 

    Stats:  EXC     INF     LNF     LOT     PLOT    NIPL    ALM     ASM
	ST230_10.fasta  1304    0       11      0       1       0       11      7
	ST230_11.fasta  1306    0       11      0       1       0       10      6
	ST230_12.fasta  1302    0       12      1       1       0       10      8
	ST230_13.fasta  1304    0       12      0       1       0       10      7
	(...)


The headers are:

 - EXC - allele has exact match (100% identity) to a known allele
 - INF - infered allele with prodigal
 - LNF - locus not found
 - LOT - locus on the tip of the contig (partial match)
 - PLOT - locus possibly on the tip of the contig (CDS match is on the tip of the contig - to be manualy curated) 
 - NIPL - Non informative paralogous locus (two or more blast matches with BSR >0.6 for the protein)
 - ALM - allele larger than gene size mode (match CDS length> gene mode length + gene mode length * 0.2)
 - ASM - allele smaller than gene size mode (match CDS length < gene mode length - gene mode length * 0.2)
 
<details> <summary>Evaluate the file *statistics.txt* file : Did all the genomes have good allele calls?  If you find any genome with problems , open the *AlleleCall.txt* file and remove the corresponding line </summary>
   A: The ST230_9.fasta has 1332 locus not found (LNF). Check the file size for this genome to see if matches the other strains. Remove this genome from the AlleleCall.txt
</details>

11) The next step is to convert the AlleleCall.txt into a format that can be easily be used for PHYLOViZ to visualize the results in a tree. To do that step run 

    XpressGetCleanLoci4Phyloviz.py -i AlleleCall_ST230.txt -g AlleleCall_ST230.phyloviz.txt 

(yes I know...we have to work on the script names....) 

the script should output the following:

    deleted : 57 loci
	total genes remaining : 1279

meaning that 57 loci where removed because at least one allele was not called in one of the strains. So the final number of loci that will be used in the analysis is 1279. We will use the *AlleleCall_ST230.phyloviz.txt* in the hands-on session on PHYLOViZ

12) Extra step: Follow the tutorial to run the allele call for the 3 genomes you downloaded from NCBI and add the results of the allele call for those genomes to the results you obtained to the ST230 strains (*AlleleCall_ST230.txt*).   Make sure you choose different names for the new allele call files and you create copies of the (*AlleleCall_ST230.txt*) before adding the extra information  

> Written with [StackEdit](https://stackedit.io/).

