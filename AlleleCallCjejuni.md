
## Allele call using Campylobacter jejuni data ##

For this hands-on module we will use [chewBACCA](https://github.com/mickaelsilva/chewBBACA) for performing the allele call on a dataset of Campylobacter jejuni , from the article 
"[Tracing isolates from domestic human Campylobacter jejuni infections to chicken slaughter batches and swimming water using whole-genome multilocus sequence typing.](http://www.ncbi.nlm.nih.gov/pubmed/27041390)".

This dataset will have genome assemblies from 17 *C .jejuni* isolates and we will work directly on the assemblies provided.

For the allele call you will need : 
1) a cg/wgMLST schema - one fasta file per loci
2) assemblies 

The schema that we will use is available from PubMLST website (http://pubmlst.org/) developed by Keith Jolley (Jolley & Maiden 2010, BMC Bioinformatics, 11:595) and sited at the University of Oxford. The development of that website was funded by the Wellcome Trust.

The description of how the  *Campylobacter sp*. cgMLST schema was created can be found at at: http://pubmlst.org/campylobacter/info/cgMLST.shtml

0) Make sure you have your and software  available and on the path. For Taito run

    module load biokit

1) create a directory for the cgMLST analysis:

    mkdir /wkr/<username>/campy_cgMLST

 2) Copy the Schema files and the Assemblies from the shared data folder to this directory. 
		
    cd campy_cgMLST
    mv ../shared_all/Friday20thMay/Campylobacter/Assemblies/ .
    mv ../shared_all/Friday20thMay/Campylobacter/CampiPubmlstSchema/ .
  
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

7) run the allele call. Make sure 

     alleleCalling_ORFbased_protein_main3_local.py -i Assemblies/listGenomes.txt -g CampiPubmlstSchema/cgMLSTLoci.txt -o AlleleCall.txt -p prodigal

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
 
 11) <details> <summary>Evaluate the file *statistics.txt* file : Did all the genomes have good allele calls?  If you find any genome with problems , open the *AlleleCall.txt* file and remove the corresponding line </summary>
   A: The ST230_9.fasta has 1332 locus not found (LNF). Check the file size for this genome to see if matches the other strains.
</details>

> Written with [StackEdit](https://stackedit.io/).
