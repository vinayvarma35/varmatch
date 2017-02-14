# VarMatch
robust matching of small variant datasets using flexible scoring schemes

# Authors
- Chen Sun (The Pennsylvania State University)
- Paul Medvedev (The Pennsylvania State University)

# Release Date
### TBA


# Prerequisite
- GCC 5.2 or later for c++11 support
- Python 2.7 or later
- matplotlib*

> *matplotlib is only used for graphic visualization. you can use '-G' parameter to disable visualization function

> *matplotlib is not a prerequisite if either `-f`, `-G` or `-C` parameter is used

# Installation
### Quick Install Instruction:
You can build VarMatch from source. 
```
git clone https://github.com/medvedevgroup/varmatch.git
cd varmatch
make all
```

### Uninstall
`cd` to the directory of VarMatch
```
make clean
```

# Test Data Set

- Links to a test data set (~15M) : [https://github.com/medvedevgroup/varmatch/blob/master/test_data.txt](https://github.com/medvedevgroup/varmatch/blob/master/test_data.txt)

- Links to the data used for bencharmking in the paper: [https://github.com/medvedevgroup/varmatch/blob/master/data.txt](https://github.com/medvedevgroup/varmatch/blob/master/data.txt)

# Usage
### VarMatch Usage:

*compare vcf files to match variants*

```
./varmatch -b baseline.vcf -q query.vcf [query2.vcf query3.vcf ...] -g ref.fa -o output
```
- `-b` baseline vcf file
- `-q` query vcf file
- `-g` genome fasta file
- `-o` output file prefix, default value is `out`

>*fast mode is suggested for ordinary analysis.

>VarMatch accept baseline and query in VCF file format (e.g. xx.vcf), it does not accept gz file (e.g. xx.vcf.gz) in current version.

>see [Results of VarMatch](#results-of-varmatch) section for intepretation of results in output directory. 

### Help Information:

use `-h/--help` for detailed help message.

### Convert VarMatch Result to GIAB Intermediate VCF files

> see [GIAB Intermediate VCF Format]() for format detail.
> see [Results of VarMatch](#results-of-varmatch) section for intepretation of results in output directory.

```
./vm-convert -b baseline.vcf -q query.vcf -B OUTPUT_DIR/baseline.vcf.miss -Q OUTPUT_DIR/query.vcf.miss -m OUTPUT_DIR/query1.0_0_0.match -o baseline.intermediate.vcf -u query.intermediate.vcf
```

- `-b` baseline vcf file
- `-q` query vcf file
- `-B` VCF entries not accessed in baseline vcf, generated by VarMatch in output directory
- `-Q` VCF entries not accessed in query vcf, generated by VarMatch in output directory
- `-m` .match file, generated by VarMatch in output directory
- `-o` output filename for baseline intermediate vcf file
- `-u` output filename for query intermediate vcf file
- `OUTPUT_DIR` VarMatch's output directory

# Results of VarMatch

### varmatch (.match) file
You can find varmatch (.match) files in VarMatch output directory, filename is in the format of query`x`.`u`\_`m`\_`s`.match

- `x` is the id of queries.
- `u` is the value of parameter `-u`, `--score_unit`
- `m` is the value of parameter `-m`, `--match_mode`
- `s` is the value of parameter `-s`, `--score_scheme`

For instance, if you use one query VCF file and use `-f` parameter, there is query1.0_0_0.match in your output file.

varmatch file contains the information of matched VCF entries from baseline and query VCF file.

Lines in varmatch file started with `#` are comment lines. They contain general information of baseline and query VCF file, and also general information of varmatch file. The first 2 lines of .match file starts with `###`:

- `###VCF1` is the baseline VCF filename

- `###VCF2` is the query VCF filename

varmatch files contains at least 9 fields:

  1. `CHROM` field represents chromosome ID
  2. `POS` field represents genome position on reference genome sequence
  3. `REF` field represents reference allele
  4. `ALT` field represents alternative alleles, multiple alleles are separated by `/`
  5. `VCF1` field represents variants from baseline. If it is a direct match, this column is `.`. If it is not a direct match, this column contains variants separated by `;`. Each variant contains three information: reference genome position, reference allele, alternative alleles.
  6. `VCF2` field represents variants from query. If it is a direct match, this column is `.`.
  7. `PHASE1` field represents phasing information of variants from baseline. If it is a direct match, this column is `.`.
  8. `PHASE2` field represents phasing information of variants from query. If it is a direct match, this column is `.`.
  9. `SCORE` field represents the total score of variants from baseline and query, calculated based on given score unit, and score scheme.

The meaning of each line in varmatch file:

> variants in `VCF1` column is equivalent to variants in `VCF2` column. If applying them separately on `REF` sequence, which is a substring of reference genome sequence starts at position `POS` of chromosome `CHROM`, can get the same donor sequences in `ALT` column.
The phasing information of variants in `VCF1` and `VCF2` are separately in `PHASE1` and `PHASE2`. 

varmatch (.match) file format gives a standard representation of equivalent variants, especially for complex variants.

If you have any suggestions of improving .match file format, please [contact me](#contact).

### .stat file

It contains some statistical information.

### .miss file

It contains VCF entries that are not accessed.
For input VCF `FILENAME.vcf`, the corresponding .miss file is `.FILENAME.vcf.miss`

### .matrix file

It contains matching matrix for matching multiple VCF files.

Lines in varmatch file started with `#` are comment lines. They contain general information of baseline and query VCF files. The first `N` lines (`N-1` query VCF files) of .matrix file starts with `##`:

- `##VCF1` is the baseline VCF filename
- `##VCF2` is the first query VCF filename
- `##VCF3` is the second query VCF filename
......
- `##VCFN` is the N-1 th query VCF filename

.matrix file contains `8+N` fields (given `N-1` query VCF files):
  1. `CHROM` field represents chromosome ID
  2. `POS` field represents genome position on reference genome sequence
  3. `REF` field represents reference allele
  4. `ALT` field represents alternative alleles, multiple alleles are separated by `/`
  5. `GT` field is genotype information of current variant (VCF Format v4.3)
  6. `GQ` field is genotype quality information of current variant (VCF Format v4.3)
  7. `DP` field is read depth information of current variant (VCF Format v4.3)
  8. `AD` field is total read depth for each allel of current variant (VCF Format v4.3)
 
The next N field is 1/0 value, indicating whether the variant appeared in VCF-N

# License

See [license.txt](https://github.com/medvedevgroup/varmatch/blob/master/license.txt)

# Contact

chensun@cse.psu.edu

You also can report bugs or suggest features using issue tracker at GitHub [https://github.com/medvedevgroup/varmatch](https://github.com/medvedevgroup/varmatch)

# Acknowledgements

If using VarMatch, please cite:
Sun, C. and Medvedev, P., 2016. VarMatch: robust matching of small variant datasets using flexible scoring schemes. Bioinformatics, p.btw797.

Corresponding BiBTex:
```
@article{sun2016varmatch,
  title={VarMatch: robust matching of small variant datasets using flexible scoring schemes},
  author={Sun, Chen and Medvedev, Paul},
  journal={Bioinformatics},
  pages={btw797},
  year={2016},
  publisher={Oxford Univ Press}
}
```

This project has been supported in part by NSF awards DBI-1356529, CCF-1439057, IIS-1453527, and IIS-1421908.


