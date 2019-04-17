## BioAWK 基础  
Bioawk is an extension of the UNIX core utility command `awk`. It provides several features for biological data manipulation in a similar way as that of `awk`. This tutorial will give a brief introduction and examples for some common tasks that can be done with this command.

You can download and install it from the [Git repository](https://github.com/lh3/bioawk).
### Features  
* It can automatically recognize some popular formats and will parse different features associated with those formats. The format option is passed to `bioawk` using `-c` arg flag. Here arg can be `bed`, `sam`, `vcf`, `gff` or `fastx` (for both `fastq` and `FASTA`). It can also deal with other types of table formats using the `-c header` option. When `header` is specified, the field names will used for variable names, thus greatly expanding the utility.  
* There are several builtin functions (other than the standard `awk` built-ins), that are specific to biological file formats. When a format is read with `bioawk`, the fields get automatically parsed. You can apply several functions on these variables to get the desired output. Let’s say, we read `fasta` format, now we have `$name` and `$seq` that holds sequence name and sequence respectively. You can use the `print` function (`awk` builtin) to print `$name` and `$seq`. You can also use `bioawk` built-in with the `print` function to get length, reverse complement etc by just using `'{print length($seq)}'`. Other functions include `reverse`, `revcomp`, `trimq`, and, `or`, `xor` etc.  
* It can automatically read gzipped/compressed files  
### Options  
* `-t` to set input and output filed separator as tab  
* `-c fmt` to read and parse the file in desired format  
* `-v var=value` initialize a variable and value [std to awk as well]  
* `-H` retain header in the output file (for files like SAM)  
* And all standard awk flags will work with bioawk  
### Variables for each format  
For the `-c` you can either specify `bed`, `sam`, `vcf`, `gff`, `fastx` or `header`. Bioawk will parse these variables for the respective format  

bed |	sam	| vcf |	gff	| fastx
--- | --- | --- | --- | ---
chrom |	qname |	chrom |	seqname |	name
start |	flag |	pos |	source |	seq
end |	rname |	id |	feature |	qual
name |	pos |	ref |	start |	comment
score |	mapq |	alt |	end	 | 
strand | 	cigar |	qual |	score |	 
thickstart |	rnext |	filter |	filter	|   
thickend |	pnext |	info |	strand |	 
rgb |	tlen |	group |	 	|   
blockcount | seq |	attribute |	 |  	 
blocksizes |	qual |  |   |   	 	 	 
blockstarts |  |	 |	 |	 

If `-c header` is specified, the field names (first line) will be used as variables (spaces and special character will be changed to under_score).  
## 示例 Demos   
```
1. For FASTA files
# Get length for sequences
bioawk -c fastx '{ print $name, length($seq) }' input.fasta

# Get %GC for sequences
bioawk -c fastx '{ print $name, gc($seq) }' input.fasta

# Get reverse complement for all sequences
bioawk -c fastx '{ print ">"$name;print revcomp($seq) }' input.fasta

# Print sequences with length greater than 100 bases
bioawk -c fastx 'length($seq) > 100{ print ">"$name; print $seq }'  input.fasta

# Add a prefix/suffix to the sequence defline
bioawk -c fastx '{ print ">PREFIX"$name; $seq }' input.fasta
bioawk -c fastx '{ print ">"$name"|SUFFIX"; $seq }' input.fasta

# Convert FASTA to tabular format
bioawk -t -c fastx '{ print $name, $seq }' input.fasta

# Extract sequences based on ids in a file
#for large scale use cdbyank instead
bioawk -cfastx 'BEGIN{while((getline k <"IDs.txt")>0)i[k]=1}{if(i[$name])print ">"$name"\n"$seq}' input.fasta

2. For fastq files
# Count the number of records (reads)
bioawk -t -c fastx 'END {print NR}' input.fastq
# note that when fastq is specified, each record is 4 lines

# Convert fastq to FASTA
bioawk -c fastx '{print ">"$name; print $seq}' input.fastq

# Get the mean Phred quality score from fastq
bioawk -c fastx '{print ">"$name; print meanqual($qual)}' input.fastq

# Filter reads shorter than 10 bp (or any bp)
bioawk -cfastx 'length($seq) > 10 {print "@"$name"\n"$seq"\n+\n"$qual}' input.fastq

# Trim fastq files based on quality
bioawk -c fastx ' trimq(30, 0, 5){print $0}' input.fastq
# trims fastq bases 0 to 5 (beginning to end), scores less than 30.

3. For BED files
# Print the feature length
bioawk -c bed '{ print $end - $start }' test.bed

4. For SAM files
# Extract unmapped reads
bioawk -c sam 'and($flag,4)' input.sam

# Extract mapped reads
bioawk -c sam -H '!and($flag,4)' input.sam

# Create FASTA from SAM
bioawk -c sam '{ s=$seq; if(and($flag, 16)) {s=revcomp($seq) } print ">"$qname"\n"s}' input.sam > output.fasta

5. For VCF files
# Print the genotypes of sample foo and bar from a VCF:
grep -v ^## in.vcf | bioawk -tc hdr '{print $foo,$bar}'
```
