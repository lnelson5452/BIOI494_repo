# PropagAtE-tutorial
## How does PropagAtE work?
PropagAtE uses coordinates of prophages found using the bacteriophage detection software VIBRANT or from a coordinate file generated by the user and short sequencing reads to determine if each prophage is in the lytic (active) or lysogenic (dormant) cycle. PropagAtE determines the phage:host read coverage ratio, which will show if the prophage is actively replicating its genome (there are significantly more phage genomes present than host genomes) or if the prophage is dormant (it is about 1:1). 
## Installation
### Requirements:

System Requirements: PropagAtE has been tested and runs on Mac, Linux, and Ubuntu 

Program Dependencies: [Python 3](https://www.python.org/) (Version>=3.5), [Bowtie2](http://bowtie-bio.sourceforge.net/bowtie2/manual.shtml), and [Samtools](http://www.htslib.org/)

Python Dependencies: [PySam](https://github.com/pysam-developers/pysam) (>=0.15.0), [Numpy](https://numpy.org/install/) (>=1.17.0), and [Numba](https://numba.pydata.org/numba-doc/latest/user/installing.html) (>= 0.50.0)

While not mandatory, [VIBRANT](https://github.com/AnantharamanLab/VIBRANT) is useful to use to obtain the prophage coordinates. VIBRANT and PropagAtE were made by the same authors and work with eachother seamlessly. 

### Downloading and Installing PropagAtE
1. ``` git clone https://github.com/AnantharamanLab/PropagAtE ``` 
2. ``` cd PropagAtE ```
3. ``` pip install . ``` (don't forget the dot)
4. Ensure permissions ```chmod +x Propagate/*``` (execute from within the PropagAtE directory)

note: a new folder ``` PropagAtE.egg-info ``` will appear after installing with pip

## Testing PropagAtE
Test data can be found in the ```example_output``` directory for both active and dormant examples

### Dormant Prophage Test
input: scaffold sequences, short read sequences, VIBRANT prophage coordinate file
1. ``` cd example_output/dormant ```
2. ``` Propagate -f example_sequence.fasta -r sample_forward_reads.fastq.gz sample_reverse_reads.fastq.gz -v VIBRANT_integrated_prophage_coordinates_example.tsv -o PropagAtE_example_results_dormant --clean -t 2 ```

### Active Prophage Test
1. ``` cd example_output/active ```
2. ``` Propagate -f AE017333_partial_genome.fasta -b AE017333_partial_genome.sorted.bam -v manual_prophage_coordinates_AE017333.tsv -o PropagAtE_example_results_active ```

## Flag Descriptions
### Input
+ ```-f```: _Required_. Denotes input genomes/scaffold (fasta file).
+ ```-v```: _Required_. Prophage coordinate file from VIBRANT
Pick one:
+ ```-b```: Input BAM file (sorted or unsorted)
+ ```-s```: Input SAM file (will be converted to a BAM file)
+ ```-r```: Input paired short reads (separate file paths by a space) ```-r forward_1.fastq reverse_2.fastq```
+ ```-i```: Input interleaved paired short reads
+ ```-u```: Input unpaired short reads
### Other flags
+ ```-o```: specify output folder (not mandatory)
+ ```-t```: specify number of threads to be used by Bowtie2 and Samtools

_The following flags can be used to modify how PropagAtE determines active vs dormant_
+ ```-p```: minimum percent identity per aligned read for calculating coverage. (default = 0.97)
+ ```-e```: minimum effect size for significance by Cohen's _d_ test. (default = 0.7 minimum = 0.6
+ ```-c```: minimum prophage:host coverage ratio for significance. (default = 2.0 minimum = 1.5)
+ ```--mask```: mask coverage values bases on each end of a scaffold. (default = 150 bp)
+ ```--min```: minimum average coverage to consider a prophage present. (default = 1x)
+ ```--breadth```: minimum breadth coverage as fraction of bases >= minimum coverage. (default = 0.5)
+ ```--clean```: this flag will remove and generated SAM, unsorted BAM and/or Bowtie2 index files. (off by default)

## Output Explanation
PropagAtE generates two output files
1. Log file: The first section contains information about input command and run info. The second section contains an overview of the processes run.
2. Results file: A spreadsheet containing the determined activity for each prophage and relevant metrics/stats.
   Results file column explanations:
   + prophage: the name of the prophage
   + host: the name of the host
   + active: "active" indicates the phage was lytic. "dormant" indicates that the phage was lysogenic. "ambiguous" means that the prophage passed ```-e``` and ```-c``` cutoffs, but not the ```--min``` or ```--breadth``` cutoffs, meaning that it is likely not active, but there isn't enough evidence to claim that is it dormant. "not present" means that the prophage had no coverage.
   + CohenD: the Cohen's _d_ effect size for the prophage and host coverages
   + prophage-host_ratio: the prophage:host ratio
   + mean_difference: the difference between the prophage and host coverages
   + prophage_len: the length in nucleotides of the prophage region
   + prophage_median_cov: the mean coverage of the prophage region
   + prophage_median_cov: the median coverage of the prophage region
   + prophage_sd_cov: the standard deviation of the prophage region coverage values
   + prophage_cov_breadth: the breadth of coverage of the prophage region
   + host_len: the length in nucleotides of the host region
   + host_mean_cov: the mean coverage of the host region
   + host_median_cov: the median coverage of the host region
   + host_sd_cov: the standard deviation of the host region coverage values

## Citing this awesome tool!
Kieft, K., and Anantharaman, K. (2022). Deciphering active prophages from metagenomes. mSystems, 7 (2), e00084-22.
