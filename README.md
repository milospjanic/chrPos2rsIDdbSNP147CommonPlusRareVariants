# chrPos2rsIDdbSNP147CommonPlusRareVariants


chrPos2rsIDdbSNP147CommonPlusRareVariants is a script to convert a list of genomics positions in a format: chrno_position_allele1_allele2 to SNP rsIDs. Script will append rsIDs as a column to an existing file. chrPos2rsIDdbSNP147CommonPlusRareVariants is useful for quick conversion to SNP rsIDs for various downstream analysis and lookups.

chrPos2rsIDdbSNP147CommonPlusRareVariants uses dbSNP147 dataset from UCSC table browser that contains both common and rare variants (in total **158,622,213** variants , and thus can convert datasets with rare variants.

**Usage**

This script will check if the working folder ~/chrPos2rsIDdbSNP147CommonPlusRareVariants is present and if not it will create ~/chrPos2rsIDdbSNP147CommonPlusRareVariants. Next, script will go into ~/chrPos2rsIDdbSNP147CommonPlusRareVariants and check if dbSNP bed file (downloaded from UCSC Table browser, including both common and rare variants) for human genome hg38 is present or not, version 147, and in case it is not present it will download it from a custom link.

**Note - Script chrPos2rsIDdbSNP147CommonPlusRareVariants.sh can be placed anywhere as well as the input file, however output will be in ~/chrPos2rsIDdbSNP147CommonPlusRareVariants**

Your input file should be formatted as:

<pre>
MarkerName	Allele1	Allele2	Freq1	Effect	StdErr	P-value	Direction	
6_34845648_C_T	t	c	0.8545	0.1959	0.0484	5.11E-05	++	
6_34883363_A_G	a	g	0.146	-0.1927	0.0484	6.96E-05	--	
6_34908015_C_T	t	c	0.144	-0.1931	0.0489	7.78E-05	--	
6_34906168_C_T	t	c	0.856	0.1931	0.0489	7.78E-05	++	
6_34851134_C_T	t	c	0.8545	0.1908	0.0484	8.09E-05	++	
6_34846832_A_G	a	g	0.1456	-0.1904	0.0484	8.32E-05	--	
6_34847229_C_T	t	c	0.8547	0.1907	0.0485	8.38E-05	++	
6_34861103_C_G	c	g	0.8548	0.19	0.0484	8.77E-05	++	
6_34861799_A_G	a	g	0.1452	-0.19	0.0484	8.78E-05	--	
</pre>

Next an akw code will parse your file to free up chr and position from the first column, append chr to newly formed first column, remove alleles, and perform comparison of your file and dbSNP data using 4 different hash tables in awk. Script will output a file of your input SNPs with rsID appended to it, separated by tab and save it as $1.rsID, $1 being first parameter provided to the script that should be the file name containing SNPs. 

dbSNP download will produce a file dbSNP147allSNPs.gz, with 158,622,213 SNPs:
<pre>
zcat dbSNP147allSNPs.gz | head
#chrom  chromStart      chromEnd        name
chr1    10485758        10485762        rs544485030
chr1    149946368       149946368       rs587706965
chr1    5111803         5111824         rs537299298
chr1    22806522        22806534        rs528803201
chr1    22806526        22806534        rs542546102
chr1    51118080        51118080        rs201004118
chr1    52297721        52297729        rs550763018
chr1    58982394        58982405        rs370257144
chr1    93454336        93454336        rs71586794

mpjanic@zoran:~/chrPos2rsIDdbSNP147CommonPlusRareVariants$ wc -l dbSNP147allSNPs
158622213 dbSNP147allSNPs
</pre>

**Unzip the file before running the script**

<pre>
gunzip dbSNP147allSNPs.gz
</pre>

**chrPos2rsIDdbSNP147CommonPlusRareVariants will check if dbSNP file exists and if it is parsed into categories, and if not it will download it from mySQL and parse the file into insertion (same base pair coordinates), SNPs plus simple deletions (single base pair coordinates), and large deletions (more than 1 base pair difference in the coordinates), that will be proccessed with a separate code and at the end merged into a single output.**

<pre>
-rw-rw-r-- 1 mpjanic mpjanic 494M Dec 14 18:48 snp147Common.bed
-rw-rw-r-- 1 mpjanic mpjanic  24M Mar 29 12:41 snp147Common.bed.insertions
-rw-rw-r-- 1 mpjanic mpjanic 455M Mar 29 12:41 snp147Common.bed.snp.plus.simple.deletions
-rw-rw-r-- 1 mpjanic mpjanic  16M Mar 29 12:41 snp147Common.bed.large.deletions

mpjanic@zoran:~/chrPos2rsIDdbSNP147CommonPlusRareVariants$ head snp147Common.bed.insertions
chr1	10177	10177	rs367896724
chr1	10352	10352	rs555500075
chr1	13417	13417	rs777038595
chr1	15903	15903	rs557514207
chr1	54712	54712	rs568927205
chr1	91551	91551	rs375085441
chr1	249275	249275	rs200079338
chr1	255923	255923	rs199745078
chr1	363244	363244	rs572571697
chr1	604229	604229	rs556776674
mpjanic@zoran:~/chrPos2rsIDdbSNP147CommonPlusRareVariants$ head snp147Common.bed.snp.plus.simple.deletions
chr1	11007	11008	rs575272151
chr1	11011	11012	rs544419019
chr1	13109	13110	rs540538026
chr1	13115	13116	rs62635286
chr1	13117	13118	rs62028691
chr1	13272	13273	rs531730856
chr1	14463	14464	rs546169444
chr1	14598	14599	rs531646671
chr1	14603	14604	rs541940975
chr1	14672	14673	rs4690
mpjanic@zoran:~/chrPos2rsIDdbSNP147CommonPlusRareVariants$ head snp147Common.bed.large.deletions
chr1	17358	17361	rs749387668
chr1	63735	63738	rs201888535
chr1	66435	66437	rs560481224
chr1	82133	82135	rs550749506
chr1	129010	129013	rs377161483
chr1	267227	267230	rs374780253
chr1	532325	532327	rs577455319
chr1	612688	612691	rs201365517
chr1	691567	691571	rs566250387
chr1	701779	701783	rs201234755
</pre>

Script preserves the header of the original file and adds 'rsID' as a first field and prepends it to the output file.  

Output file will be placed in ~/chrPos2rsIDdbSNP147CommonPlusRareVariants

**Running**

To run the script type:
<pre>
chmod 775 chrPos2rsIDdbSNP147CommonPlusRareVariants.sh 
./chrPos2rsIDdbSNP147CommonPlusRareVariants.sh path/to/file
</pre>

**Prerequisites**

MySQL

**Example**

<pre> 
head SNP.file
MarkerName	Allele1	Allele2	Freq1	Effect	StdErr	P-value	Direction	
6_34845648_C_T	t	c	0.8545	0.1959	0.0484	5.11E-05	++	
6_34883363_A_G	a	g	0.146	-0.1927	0.0484	6.96E-05	--	
6_34908015_C_T	t	c	0.144	-0.1931	0.0489	7.78E-05	--	
6_34906168_C_T	t	c	0.856	0.1931	0.0489	7.78E-05	++	
6_34851134_C_T	t	c	0.8545	0.1908	0.0484	8.09E-05	++	
6_34846832_A_G	a	g	0.1456	-0.1904	0.0484	8.32E-05	--	
6_34847229_C_T	t	c	0.8547	0.1907	0.0485	8.38E-05	++	
6_34861103_C_G	c	g	0.8548	0.19	0.0484	8.77E-05	++	
6_34861799_A_G	a	g	0.1452	-0.19	0.0484	8.78E-05	--	

./chrPos2rsIDdbSNP147CommonPlusRareVariants.sh SNP.file

head SNP.file.rsID.final
rsID	chr	position	Allele1	Allele2	Freq1	Effect	StdErr	P-value	Direction	
rs2985	chr6	34845648	t	c	0.8545	0.1959	0.0484	5.11E-05	++	
rs2092428	chr6	34883363	a	g	0.146	-0.1927	0.0484	6.96E-05	--	
rs847847	chr6	34908015	t	c	0.144	-0.1931	0.0489	7.78E-05	--	
rs847848	chr6	34906168	t	c	0.856	0.1931	0.0489	7.78E-05	++	
rs2273006	chr6	34851134	t	c	0.8545	0.1908	0.0484	8.09E-05	++	
rs4646944	chr6	34846832	a	g	0.1456	-0.1904	0.0484	8.32E-05	--	
rs4646940	chr6	34847229	t	c	0.8547	0.1907	0.0485	8.38E-05	++	
rs9394252	chr6	34861103	c	g	0.8548	0.19	0.0484	8.77E-05	++	
rs13219624	chr6	34861799	a	g	0.1452	-0.19	0.0484	8.78E-05	--	
</pre>
