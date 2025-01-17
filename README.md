# Identification and comparison of somatic antigen structures of symbiotic and pathogenic bacteria from Morganellaceae family

## Introduction

Analysis of the functional organization of the O-antigen operons makes it possible to identify genes specific for each O-serogroup and use them both for molecular typing of strains and for identifying pathways of bacterial evolution.

This project aims to identify and compare candidate O-antigen operons in bacteria of the Morganellaceae family with different levels of virulence.

Goals of the project:
* to find genes of O-antigen biosynthesis in the _Providencia_ and _Xenorhabdus_ species analyzing the literature;
* to analyze the quality of all _Providencia_ assemblies available at NCBI and select the best one for each species;
* to create and test the pipeline for identifying and visualizing O-antigen operons;
* to compare candidate O-antigen operons in selected _Providencia_ and _Xenorhabdus_ species;

## Programs used in the study

This project was performed with:
* python v3.7; all required packages are specified in the `environment.yml` file
* [Operon-Mapper](https://biocomputo.ibt.unam.mx/operon_mapper/) (Taboada B. et al., 2018)
* [BPROM](http://www.softberry.com/berry.phtml?topic=bprom&group=programs&subgroup=gfindb) (Solovyev V. et al., 2011)
* [FindTerm](http://www.softberry.com/berry.phtml?topic=findterm&group=programs&subgroup=gfindb) (Solovyev V. et al., 2011)
* [ORFfinder](https://www.ncbi.nlm.nih.gov/orffinder/) (Wheeler DL. et al., 2003)
* MEGAX v10.2.4 (Sudhir K. et al., 2018)

## Workflow

__1. Setting up the environment__

```bash
git clone https://github.com/rybinaanya/O-antigens
cd O-antigens
conda env create -f environment.yml
conda activate operons
```

All downstream steps should be performed at `O-antigens` directory. 

__2. Processing the _Providencia_ assemblies__

[This script](https://github.com/rybinaanya/O-antigens/blob/main/process_assemblies.sh) was used to work with assemblies:

```bash
bash process_assemblies.sh
```

This step includes:

* Downloading all _Providencia_ assemblies

In this project we downloaded all _Providencia_ assemblies available to 04.03.2021 (204 assemblies)

* Selecting the best assemblies, based on statistics:
    - number of contigs
    - Total length (>= 50000 bp)
    - N50
    - L50

#### Summary of selected assemblies (out of 204 assemblies available to 04.03.2021 at NCBI)

| Assembly ID   |Organism Name                           |Total length|Contigs|  N50  |L50|                     Link                                           |                                           
|:-------------:|:----------------------------------------:|:----------:|:-----:|:-----:|:-:|:------------------------------------------------------------------:|
|GCF_001874625.1|Providencia rettgeri strain RB151       |  4889093   |2      |4780676|1  | [Assembly](https://www.ncbi.nlm.nih.gov/assembly/GCF_001874625.1/) |       
|GCF_010669105.1|Providencia stuartii strain MF1         |  4528609   |2      |4525926|1  | [Assembly](https://www.ncbi.nlm.nih.gov/assembly/GCF_010669105.1/) |    
|GCF_000259175.1|Providencia stuartii MRSN 2154          |  4402109   |1      |4402109|1  | [Assembly](https://www.ncbi.nlm.nih.gov/assembly/GCF_000259175.1/) |     
|GCA_016618195.1|Providencia vermicola strain LLDRA6     |  4342370   |1      |4342370|1  | [Assembly](https://www.ncbi.nlm.nih.gov/assembly/GCA_016618195.1/) |     
|GCF_013702185.1|Providencia alcalifaciens strain 1701003|  4033976   |1      |4033976|1  | [Assembly](https://www.ncbi.nlm.nih.gov/assembly/GCF_013702185.1/) |     
|GCF_900455075.1|Providencia rustigianii strain NCTC11802|  3969308   |3      |3926179|1  | [Assembly](https://www.ncbi.nlm.nih.gov/assembly/GCF_900455075.1/) |    
|GCF_900635875.1|Providencia rustigianii strain NCTC6933 |  3913850   |1      |3913850|1  | [Assembly](https://www.ncbi.nlm.nih.gov/assembly/GCF_900635875.1/) |
|GCF_014652175.1|Providencia thailandensis KCTC 23281    |  4342585   |39     |484316 |3  | [Assembly](https://www.ncbi.nlm.nih.gov/assembly/GCF_014652175.1/) |  
|GCF_013702025.1|Providencia rettgeri strain 2055        |  4674814   |1      |4674814|1  | [Assembly](https://www.ncbi.nlm.nih.gov/assembly/GCF_013702025.1/) |  

* Retrieving PGAP annotation of selected assemblies from NCBI 
* Annotating all selected assemblies with Prokka

  
__3. Identify operon boundaries with Operon-Mapper__

Upload genome FASTA file to the web-server and submit with default options. After job completion, download output compressed folder that includes list of predicted operons with their gene compostion, predicted ORFs coordinates and their DNA sequences, and other files.

__4. Search for and visualize candidate O-antigen operons__

At this stage, we used [Python script](https://github.com/rybinaanya/O-antigens/blob/main/search_visualize_operons.py) to search for candidate O-antigen operon genes from GFF annotation, the numbers of operons in which these genes are present, and the boundaries of these operons. Operon numbers and GFF files were used to visualize candidate O-antigen operons.

Example of operon search and operon visualization in _Xenorhabdus_ could be found [here](https://github.com/rybinaanya/O-antigens/blob/main/Examples/Xenorhabdus_example.ipynb). 

__5. (OPTIONAL) Validate boundaries of candidate O-antigen operons and clarify annotation of some genes__

When the boundaries of operons are ambiguous, or annotation of a gene was undefined, we applied [this script](https://github.com/rybinaanya/O-antigens/blob/main/get_sequence.py) for extracting DNA fragment to validate operon boundaries using BPROM and FindTerm and to clarify annotation using BLAST and ORFfinder.

__6. Codon-based test of neutrality__

* creating multi-FASTA files for each gene from the conserved operons. For this stage, we used PGAP annotation since its results are more accurate than those of Prokka; obtained multi-FASTA files are deposited [here](https://github.com/rybinaanya/O-antigens/tree/main/Z_test)
* running multiple sequence alignment on gene multi-FASTA files using MAFFT

These two steps were implemented in [bash script](https://github.com/rybinaanya/O-antigens/blob/main/align_conserved_operon_genes.sh):

```bash
bash align_conserved_operon_genes.sh
```

* computing the Z-test statistics of neutral evolution in MEGAX: syn-nonsynonymous substitution type and Nei-Gojobori (Jukes-Cantor) model were applied on 1000 bootstraps with pairwise deletion as gaps/missing data treatment


## Results

1. Out of 204 _Providencia_ assemblies available to 04.03.2021 at NCBI, we selected only seven complete genomes and two genomes of contig/scaffold assembly level that demonstrated the best quality. In total, we analyzed six distinct _Providencia_ species.

2. We have designed a unique pipeline for identifying candidate O-antigen operons. The key features of our pipeline are: employing several genome annotation tools, additional prediction and validation of operon boundaries, and manual curation and annotation of unknown genes. Our pipeline allowed us to detect 23 and 12 genes in variable and conserved candidate O-antigen operons, respectively. Among these genes, only seven (_galE_, _wxz_, _wzc_, _wza_, _ugd_, _rmlA_, and _wecA_)  were previously described as O-antigen ones in _Providencia_ and _Xenorhabdus_ species.  

3. Both Providencia and Xenorhabdus species have O-antigen conserved operon involved in the nucleotide (UDP- or dTDP-) sugar biosynthesis (_wecB_, _wecC_, _rffG_, _rfbA_, _rffC_, _rffA_), glucosyl to lipid transfer (_wecA_, _wecF_, _wecG_), and O-antigen processing (_wzzE_,  _wzxE_, _wzyE_). 

|    Gene    | P-value |       Z(dN−dS)             | Selection |                                           
| :---------:|:-------:| :-------------------------:|:---------:|
| wecA       | 0.0     |       -8.95                |negative   |
| wzzE       | 0.0     |       -9.25                |negative   |
| wecB       | 0.0     |       -9.35                |negative   |
| wecC       | 0.0     |       -10.32               |negative   |
| rffG       | 0.0     |       -10.47               |negative   |
| rffH       | 0.0     |       -6.84                |negative   |
| wecD       | 0.0     |       -6.47                |negative   |
| wecE       | 0.0     |       -9.71                |negative   |
| wzxE       | 0.0     |       -10.59               |negative   |
| wecF       | 0.0     |       -10.18               |negative   |
| wzyE       | 0.0     |       -5.88                |negative   |
| wecG       | 0.0     |       -6.83                |negative   |

Almost all _Providencia_ used in the study have _galETKMR_ operon (except for _Providencia alcalifaciens_ strain 1701003) with the same genetic order and gene content.

|    Gene    | P-value |       Z(dN−dS)             | Selection |                
| :---------:|:-------:| :-------------------------:|:---------:|
| galE       | 0.0     |       -8.15                |negative   |
| galT       | 0.0     |       -6.20                |negative   |
| galK       | 0.0     |       -6.33                |negative   |
| galM       | 0.0     |       -8.22                |negative   |
| galR       | 0.0     |       -7.53                |negative   |

4. We did not observe a correlation between lifestyle and O-antigen operon organization as we initially expected. 

For more details on results, check the [slides](https://github.com/rybinaanya/O-antigens/blob/main/O-antigens_presentation.pdf)

## Authors:

* Anna Churkina, Almazov National Medical Research Centre, Saint Petersburg, Russia
* Anna Rybina, Skolkovo Institute of Science and Technology, Moscow, Russia

Supervisors:
* Polina Kuchur, Aleksey Komissarov, 
Applied Genomics Laboratory, SCAMT Institute, ITMO University, 191002 Saint-Petersburg, Russia

## References

1. Bisch, Gaëlle, Sylvie Pagès, John G. McMullen 2nd, S. Patricia Stock, Bernard Duvic, Alain Givaudan, and Sophie Gaudriault. 2015. “Xenorhabdus Bovienii CS03, the Bacterial Symbiont of the Entomopathogenic Nematode Steinernema Weiseri, Is a Non-Virulent Strain against Lepidopteran Insects.” Journal of Invertebrate Pathology 124 (January): 15–22.
2. Herbert, Erin E., and Heidi Goodrich-Blair. 2007. “Friend and Foe: The Two Faces of Xenorhabdus Nematophila.” Nature Reviews. Microbiology 5 (8): 634–46.
3. Ovchinnikova, Olga G., Bin Liu, Dan Guo, Nina A. Kocharova, Magdalena Bialczak-Kokot, Alexander S. Shashkov, Lu Feng, Antoni Rozalski, Lei Wang, and Yuriy A. Knirel. 2012. “Structural, Serological, and Genetic Characterization of the O-Antigen of Providencia Alcalifaciens O40.” FEMS Immunology and Medical Microbiology 66 (3): 382–92.
4. Samuel, Gabrielle, and Peter Reeves. 2003. “Biosynthesis of O-Antigens: Genes and Pathways Involved in Nucleotide Sugar Precursor Synthesis and O-Antigen Assembly.” Carbohydrate Research 338 (23): 2503–19.
5. Wang, Lei, Quan Wang, and Peter R. Reeves. 2010. “The Variation of O Antigens in Gram-Negative Bacteria.” Sub-Cellular Biochemistry 53: 123–52.
6. Blanca Taboada, Karel Estrada, Ricardo Ciria, Enrique Merino, Operon-mapper: a web server for precise operon identification in bacterial and archaeal genomes, Bioinformatics, Volume 34, Issue 23, 01 December 2018, Pages 4118–4120, doi: 10.1093/bioinformatics/bty496
7. V. Solovyev & A Salamov (2011) Automatic Annotation of Microbial Genomes and Metagenomic Sequences. In Metagenomics and its Applications in Agriculture, Biomedicine and Environmental Studies (Ed. R.W. Li), Nova Science Publishers, p. 61-78
8. Wheeler DL, Church DM, Federhen S, et al. Database resources of the National Center for Biotechnology. Nucleic Acids Res. 2003;31(1):28-33. doi:10.1093/nar/gkg033
9. Sudhir Kumar, Glen Stecher, Michael Li, Christina Knyaz, Koichiro Tamura, MEGA X: Molecular Evolutionary Genetics Analysis across Computing Platforms, Molecular Biology and Evolution, Volume 35, Issue 6, June 2018, Pages 1547–1549, doi: /10.1093/molbev/msy096
