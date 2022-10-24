# hot-seq-summer

### Datasets:

RNAseq datasets looking at differentially expressed genes in response to heat stress.

* Mus musculus: `mus_rna_seq.py` takes in `mus_rnaseq_data.xlsx` (from [this paper](https://elifesciences.org/articles/07687/); GEO accession: GSE65636) and returns `mus_rna_seq_final.txt` - a tab delimited file containing three columns: ensembl accession ID, log fold change, and p-value.
* Homo sapiens: `HUMAN_genes.txt`
* Yeast: `yeast_degs.txt`

### Shared Functions:
`from shared_functions import *`

##### Turning RNA Seq data into a dictionary:

`rnaseqs_to_dict()`: Inputs a formatted text file (or files) with 3 columns of RNAseq data: Ensembl ID, log fold change, and p value. Outputs an RNAseq nested dict with {key:filename: value = {key = ensembl_ID : value = [logFC, pvalue]}}. **To run:** `rnaseqs_to_dict([list of file(s)])`. *Note that even if you are only inputting one file it needs to be in a list.* (This function is based off of `rnaseq_to_dict.py`.)

##### Getting domains/GO terms:

`species_annotation_df()` can output Ensembl IDs with domains (from pfam) or GO terms (from Ensembl). **To run:** `species_annotation_df(<species>, <annotation type>)` where species = "hsapiens", "mmusculus", or "scerevisiae" and annotation = "pfam" for domains or "go_id" for GO terms. (This function is based off of `annotation_download.py`.)

##### Determining differentially expressed genes:

`deg_list()`: takes a dictionary of differentially expressed genes (DEGs) in format {ensembl_ID : [logFC , pvalue]} and outputs two lists: `genes_up`, `genes_down`. **To run:** `deg_list(DEG_dict)`. (This function is based off of the function `deg_list(deg_data)` originally in `oma_finder.py`.)

##### Visualizing data (Venn Diagram):

`make_venn_diagram()` takes three lists and creates a venn diagram to show overlap between the lists; outputs to .png. **To run:** `venn_diagram(species1, species2, species3, list1, list2, list3, output_name, title)`. (This function is based off of `make_venn_diagram.py `.)

### Enriched Domains:

* `enriched_domains.py` takes (a) a dictionary for a single species with format {key = ensembl_ID : value = [logFC, pvalue]} and (b) a dictionary containing all protein domains for genes of the specified species and returns a list of protein domains that are particularly enriched in the differentially expressed genes.
  * To run: `./enriched_domains.py <diff_direction> <species>` where diff_dicrection = "up" or "down" and species is "hsapiens", "mmusculus", or "scerevisiae".
  * Output: prints to std out `pfamID` \t `pvalue` for all domains with probability < 0.05
* `domain_comp_2.py` takes in two .txt files generated by `enriched_domains.py` and compares the enriched domains in each list. `domain_comp_3.py` takes in 3 lists, finds shared domains between all 3.
  * To run: `./domain_comp_2.py <filename1> <filename2>`
    * These files are all stored in `ProcessedData/`
  * Output: Prints to std out:
    * The number of total enriched domains in each list
    * The number of shared domains between all three lists
    * The number of domains only enriched in list1/2/3
    * For each shared domain: the description for the corresponding pfam ID
  * In order to look up the domain description from pfam ID:
    * Download `pfamA.txt` from [pfam FTP](https://ftp.ebi.ac.uk/pub/databases/Pfam/current_release/database_files/): `wget “https://ftp.ebi.ac.uk/pub/databases/Pfam/current_release/database_files/pfamA.txt.gz”`; extract necessary columns and output to new .txt file with `awk -F"\t" '{print $1 "\t" $4}' pfamA.txt > pfam2desc.txt`
