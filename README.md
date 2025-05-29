# <img src="img/OpenWingsProject-logo-small.png" width="50" height="50" /> OpenWings OpenData on Amazon Web Services

The OpenWings Project is a scientific research project having the goal of inferring a time-calibrated [phylogeny]() for all of the world's ~10,000 bird species (class [Aves](https://en.wikipedia.org/wiki/Bird)).  To generate this phylogeny, we have collected DNA sequence data from genomic libraries that have been enriched for a class of genomic markers known as [ultra-conserved elements](https://en.wikipedia.org/wiki/Ultraconserved_element) or UCEs. The enrichment process effectively increases the concentration of UCE loci relative to the rest of the genome, and we sequence the enriched product.

The set of 4,434 loci targeted for this project (5Kv2A and 5Kv2B) are a slightly modified subset of UCE loci commonly used in other studies of birds.  More information on the target enrichment baits used for this study can be found in the [OpenWings Bait repository](https://github.com/openwings-project/baits).

## About the data

We are providing the "raw" reads that we have generated from genomic libraries enriched for UCE loci. It is important to note that these data **HAVE NOT** been rigorously quality controlled.  The taxonomy of the individuals sequenced also **HAS NOT** been standardized.

What this means is that we think these data represent the species indicated and the loci targeted, but we have not performed significant analyses of these data to verify that this is the case. Verification is an ongoing process. This also means some species labels **could be incorrect**.

## Data format

The data for each species are 150 base pair (bp), paired-end (PE), Illumina sequencing reads (i.e., PE150). Data are formatted as `fastq.gz` files, and each species in the dataset has both a R1 and a R2 file (representing each of the paired reads).  File names look like the following - e.g., the two read files for the species `Rhea pennata`:
```
B-10338.LSUMZ.TISS-3:D4.Rhea-pennata.R1.fq.gz                   
B-10338.LSUMZ.TISS-3:D4.Rhea-pennata.R2.fq.gz
```
The file naming convention is:
```
[Museum_Accession].[Museum_Code].[DNA_Plate]:[DNA_Row][DNA_Column].[Genus]-[species].[Read]
```
|                    |                                                                                                                                                                                                                                                         |
|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Museum_Accession] | This is the accession number of the particular tissue used for DNA extraction, target enrichment, and DNA sequencing. This may correspond to a tissue sample in a collection *OR* to a specimen/skin within a given collection.                         |
| [Museum_Code]      | This is the INSDC code for the institution providing the tissue/toepad used for DNA extraction.  Generally speaking, the combination of the [Museum_Code] and [Museum_Accession] should be referenced every time that data from this specimen are used. |
| [DNA_Plate]        | This is the DNA extraction plate in which a particular specimen was placed.  Values of `TISS-`, `LOAN-`, `INTL-` correspond to tissues, and values of `TP-` correspond to toepads.                                                                            |
| [DNA_Row]          | This is the row location of the sample in the DNA extraction plate.                                                                                                                                                                                     |
| [DNA_Column]       | This is the column location of the sample in the DNA extraction plate.                                                                                                                                                                                  |
| [Genus]-[species]  | This is the likely species of the individual from which a tissue/toepad was collected.  The taxonomy of these individuals has not necessarily been standardized, because these values were given by the institution providing the tissue.               |
| [Read]             | This is a value of `R1` for read 1 and `R2` for read 2.                                                                                                                                                                                                 |

## Downloading the data

The data are temporarily stored (see [NCBI SRA Identifiers](#NCBI_SRA_identifiers) for additional information) on Amazon Web Services in their [S3](https://aws.amazon.com/s3/) Cloud Object Storage system.  As such, you can use tools like the [AWS CLI tool](https://aws.amazon.com/cli/) to view different buckets in which the data are stored (corresponding to sequencing runs), as well as all of the different files within a bucket. For instance, after [installing the AWS CLI tool](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) here is an example session:
```bash
# be sure that the AWS CLI tool is installed and configured correctly.
# Show the contents of the openwings project:
aws s3 ls s3://<tbd>/

# now that we have a list of buckets/sequencing runs, let's look inside of each sequencing run to see what files are included in that run:
aws s3 ls s3://<tbd>/2024-openwings-FB01Q001

# which returns:
...<tbd>...


# let's say you want to download the data for the B-66112.LSUMZ.TISS-3:A1.
# Crypturellus-transfasciatus.R1.fq.gz library.
#
# To download individual files to the local directory in which you are working, you can:
aws s3 cp s3://<tbd>/2024-openwings-FB01Q001/B-66112.LSUMZ.TISS-3:A1.Crypturellus-transfasciatus.R1.fq.gz B-66112.LSUMZ.TISS-3:A1.Crypturellus-transfasciatus.R1.fq.gz ./

aws s3 cp s3://<tbd>/2024-openwings-FB01Q001/B-66112.LSUMZ.TISS-3:A1.Crypturellus-transfasciatus.R2.fq.gz B-66112.LSUMZ.TISS-3:A1.Crypturellus-transfasciatus.R2.fq.gz ./
# this gets's the R1 (read 1) and R2 (read 2) files for this individual 
# (B-66112.LSUMZ.TISS-3:A1.Crypturellus-transfasciatus) from S3 and
# saves them locally on your machine.
```

This is rather cumbersome if you need a significant number of files, so there is another way that you can browse, select, get a list, and download the list of files that you would like.  We've setup an online spreadsheet of samples that you can browser, filter, etc.  Once you do that, download the resulting CSV file - like you can see in this short video:

[![Image of and link to youtube video](https://img.youtube.com/vi/XcHEaDy9Zxc/0.jpg)](https://www.youtube.com/watch?v=XcHEaDy9Zxc)

After you download the CSV file (it must be CSV), **do not modify it** and place it in the directory where you would like to store your data, navigate to that directory on the command line, and run the following small shell script:
```bash
# Chat this to the name of the file that you downloaded
input="OpenWings-Libraries-Table1.csv"

# Do not edit below
while IFS=, read f1 f2 f3 f4 f5 f6 f7 f8 f9 f10 f11; 
do 
    echo "Working on ${f1}";
    aws s3 cp <url>/${f8}/${f9} ./;
    aws s3 cp <url>/${f8}/${f10} ./;
done < <( tail -n +2 "$input")
 # Do not edit above
```

## Data license

Data available from the OpenWings project are released under a [Creative Commons](https://creativecommons.org) [CC-0](https://creativecommons.org/public-domain/cc0/) license.

## Data restrictions

Although the data are released under [CC-0](https://creativecommons.org/public-domain/cc0/) license, the primary investigators of OpenWings request the right to publish studies using the data collected at taxonomic levels above order (e.g. interordinal and higher levels), and to infer and time-calibrate phylogenies using the entire complement of data collected from all bird species **before these data are used by other investigators for similar purposes**.  This is in keeping with the so-called [Ft. Lauderdale Agreement](https://www.sanger.ac.uk/wp-content/uploads/fortlauderdalereport.pdf).

## If you use these data... (Part 1)

Following the general standard for the field of avian phylogenetics, please include a table listing the institution/museum code and the tissue/specimen number for each OpenWings sample that you use. Typically, such a table will also include the species name and other information associated with each sample.


## If you use these data... (Part 2)
Studies that use data, libraries, or other resources generated by OpenWings should acknowledge support from the "OpenWings Project" and should include references to the following National Science Foundation grants:
| Grant #s    |
|-------------|
| DEB-1655559 |
| DEB-1655624 |
| DEB-1655683 |
| DEB-1655736 |

We suggest something similar to the following text for inclusion in an Acknowledgements section (or similar) of a manuscript that uses OpenWings data:

> Data used as part of this publication were generated by the OpenWings Project, which was supported by National Science Foundation grants DEB-1655559, DEB-1655624, DEB-1655683, DEB-1655736

## If you use these data... (Part 3)

We request that researchers using these resources provide us with information regarding their use of these data so that we can track the impact of this research project.  Please complete the:

[OpenWings Data Use Form](https://lsu.formstack.com/forms/openwings_data_use)

## NCBI SRA identifiers

As we vet the individual data files and ensure that their taxonomic descriptions are accurate, we will upload validated data to the [NCBI SRA](https://www.ncbi.nlm.nih.gov/sra), and we will update the metadata provided here to assign correct SRA identifiers to file names.

**If you use these data, please do not also submit these data to the NCBI SRA - you did not generate them.**

## Major participating institutions

* American Museum of Natural History (Brian Smith)
* Australian National Wildlife Collection (Leo Joseph)
* University of Minnesota / Bell Museum of Natural History (Keith Barker, Sushma Reddy)
* UW / Burke Museum of Natural History and Culture (Sharon Birks, Adam Leache, Alejandro Rico-Guevara)
* Bruce Museum (Dan Ksepka)
* Field Museum of Natural History (Ben Marks, Shannon Hackett, John Bates)
* LSU / LSU Museum of Natural Science (Brant Faircloth, Robb Brumfield)
* Moore Laboratory of Zoology (John McCormack)
* Museu de Zoologia da USP (Luís Fábio Silveira)
* National History Museum of Denmark (Peter Hosner)
* University of Florida (Rebecca Kimball, Ed Braun)
* University of Kansas Biodiversity Institute (Rob Moyle)
* NMNH, Smithsonian Institution, USGS Biological Survey Unit (Terry Chesser)
* University of Alaska Museum of the North (Kevin Winker)


