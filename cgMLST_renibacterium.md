
##*Renibacterium salmoninarum* Allele Call

In this section we will walk you through all steps necessary to perform an wgMLST/cgMLST analysis using  *Renibacterium salmoninarum* samples. We will cover the following steps:

 1. Creating a wgMLST schema with chewBBACA
 2. Preparing the input for Allele Call
 3. Using chewBBACA to perform the Allele Call
 4. Creating PHYLOViZ input

###1. Creating a wgMLST schema with chewBBACA

1) Make sure you have software available. For Taito run

    screen -S <job title>
    sinteractive
    module load biokit
    
**NOTE:** Remember with *Ctrl a d* you can always close the *screen* session. You can reattach the *screen* by typing *screen -r job title*. 

Also verify if you have chewBBACA dirs in the PATH. 

    echo $PATH
    
If not ( you should have 3 chewBBACA dirs) add them by using the source command:

   `source ~/appl_taito/source_course_path`
        
3) Create a new folder where you will store the *Renibacterium salmoninarum* data sets

    cd /wrk/<username/
    mkdir reni_cgMLST
    cd reni_cgMLST

3) Copy the reference to the newly created folder

    cp /wrk/<username>/course_data/shared_all/Wednesday18thMay/Renibacterium/Reference/ATCC-33209.ffn .

4) Create a new Schema using the reference file with the command (script is available on the *chewBACCA/createschema* folder)

    CreateSchema.py -i ATCC-33209.ffn -g 200 >SchemaCreation_Reni.log

This will create a *schema_seed* folder with a set of *.fasta* with coding sequences belonging to  the reference genome that will be used as a starting point for the allele call. the -g 200 parameter is used to exclude all the sequences with sizes smaller than 200 bp.
Next step is to initialize the schema_seed folder by running

    bash init_bbaca_schema.sh
	    
This creates a dir named *short* which will contain a copy of each of the alleles and will create on schema_seed dir a file name listGenes.txt that will contain the list of genes that define the schema	   

###2. Preparing the input for Allele Call

1) **chewBBACA** will use the Schema created in the previous step and will modify it by adding new alleles to the loci belonging to the schema. To mantain a copy of the initial schema, create a copy of it using

    cd /wrk/<username>/reni_cgMLST/
    cp -r ./schema_seed ./schemaToUse

2) The next step is to make a copy of the genomes that we want to use as query

    cd /wrk/<username>/reni_cgMLST/
    cp -r /wrk/<username>/course_data/shared_all/Thursday19thMay/Renibacterium_Example/Assemblies/ .
    #Replace username by your user account
 
2) **chewBBACA** also needs a list as input with the FULL PATH of the genomes that will be used as query and also from the genes that will be used in the schema.
		
To create those lists, run the following commands

    cd Assemblies/
    #Get the list of query Genomes
    ls *.fasta | xargs -I {} sh -c 'echo $(pwd)/{}' > listGenomes.txt
    cd ../schemaToUse
    #Get the list of the schema genes
    ls *.fasta | xargs -I {} sh -c 'echo $(pwd)/{}' > cgMLSTLoci.txt

Note: In some cases, FASTA files can have different characters depending on the OS they were created. To overcome these problems, run the *dos2unix* program to convert any unsupported character.

    dos2unix *.fasta
    #Convert all .fasta files

###3. Using chewBBACA to perform the Allele Call

To perform the allele call on the *Renibacterium* dataset, we will run the application in batch mode.

Copy the *chewbbaca_job.sh* file from the shared folder to the reni_cgMLST folder with the command

`cp /wrk/<username>/course_data/shared_all/Friday20thMay/Renibacterium/chewbbaca_job.sh /wrk/<username>/reni_cgMLST/`
#replace <username> by your user account

Open the batch file in `/wrk/<username>/reni_cgMLST/chewbbaca_job.sh`

Modify the parameters inside brackets **[]** and confirm the PATHs before running the job.

Run the batch job inside reni_cgMLST with the command:

`sbatch chewbbaca_job.sh`

After that, follow the instructions for *C. jejuni* in the [allele call tutorial](https://github.com/BacterialCommunitiesAndPopulation/Friday20thMay/blob/master/AlleleCallCjejuni.md) to obtain the input file for PHYLOViZ.


> Written with [StackEdit](https://stackedit.io/).
