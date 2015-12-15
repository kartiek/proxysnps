# proxysnps

`proxysnps` is an R package that implements functions to get proxy SNPs
in [linkage disequilibrium][LD] (LD) with a [SNP] in the [1000 Genomes
Project][1000genomes].



## Install


```r
install.packages("devtools")
devtools::install_github("slowkow/proxysnps")
```

## Examples

### Get proxy SNPs

Get the `R.squared` and `D.prime` between our SNP of interest `rs11063140` at
position 583,090 ([GRCh37]) and all other SNPs within a 100 kilobase window:


```r
library(proxysnps)
d <- get_proxies(chrom = "12", pos = 583090, window_size = 1e5, pop = "AFR")
kable(head(d))
```



|    | CHROM|    POS|ID          |REF |ALT |       MAF| R.squared|  D.prime|CHOSEN |
|:---|-----:|------:|:-----------|:---|:---|---------:|---------:|--------:|:------|
|663 |    12| 583090|rs11063140  |A   |G   | 0.1550681|  1.000000| 1.000000|TRUE   |
|685 |    12| 584423|rs10849066  |A   |G   | 0.2307110|  0.596535| 0.987318|FALSE  |
|661 |    12| 583003|rs11063139  |C   |T   | 0.1686838|  0.546084| 0.777021|FALSE  |
|678 |    12| 584054|rs145345255 |G   |A   | 0.0779123|  0.429203| 0.965528|FALSE  |
|682 |    12| 584354|rs10849065  |G   |T   | 0.1875946|  0.375957| 0.687769|FALSE  |
|711 |    12| 586815|rs58254236  |C   |T   | 0.0839637|  0.361458| 0.850726|FALSE  |

### Plot the linkage disequilibirum (LD)

Plot the LD between our SNP and all others within a 100 kilobase window:
  

```r
library(ggplot2)
ggplot() +
  geom_point(data = d, aes(x = POS, y = R.squared), size = 2) +
  geom_text(
    data = d[d$CHOSEN,,drop=FALSE],
    aes(x = POS, y = R.squared, label = ID),
    hjust = -0.1, size = 4
  ) +
  theme_bw(base_size = 14) +
  labs(x = "Position", y = bquote("R"^2))
```

![plot of chunk plot_r2](https://github.com/slowkow/proxysnps/blob/master/figures/README/plot_r2-1.png) 

### Get data for a genomic region

Get data describing African individuals, metadata for each SNP, and
genotypes for each SNP and each individual:


```r
vcf <- get_vcf(chrom = "12", start = 533090, end = 623090, pop = "AFR")
names(vcf)
```

```
## [1] "ind"  "meta" "geno"
```


```r
kable(vcf$ind[1:5,])
```



|        |Family.ID |Individual.ID |Paternal.ID |Maternal.ID |Gender |Population |Relationship |Siblings |Second.Order |Third.Order |Other.Comments |SuperPopulation |
|:-------|:---------|:-------------|:-----------|:-----------|:------|:----------|:------------|:--------|:------------|:-----------|:--------------|:---------------|
|HG01879 |BB01      |HG01879       |0           |0           |male   |ACB        |father       |0        |0            |0           |0              |AFR             |
|HG01880 |BB01      |HG01880       |0           |0           |female |ACB        |mother       |0        |0            |0           |0              |AFR             |
|HG01882 |BB02      |HG01882       |0           |0           |male   |ACB        |father       |0        |0            |0           |0              |AFR             |
|HG01883 |BB02      |HG01883       |0           |0           |female |ACB        |mother       |0        |0            |0           |0              |AFR             |
|HG01885 |BB03      |HG01885       |0           |0           |male   |ACB        |father       |0        |0            |0           |0              |AFR             |


```r
kable(vcf$meta[10:15,])
```



|   | CHROM|    POS|ID          |REF |ALT |QUAL |FILTER |INFO |
|:--|-----:|------:|:-----------|:---|:---|:----|:------|:----|
|10 |    12| 533949|rs58430723  |C   |T   |.    |PASS   |.    |
|12 |    12| 534160|rs12308942  |G   |A   |.    |PASS   |.    |
|13 |    12| 534166|rs11062919  |G   |A   |.    |PASS   |.    |
|14 |    12| 534366|rs28783500  |G   |A   |.    |PASS   |.    |
|15 |    12| 534387|rs190509989 |G   |A   |.    |PASS   |.    |
|16 |    12| 534389|rs7303777   |A   |G   |.    |PASS   |.    |


```r
kable(vcf$geno[10:15,1:5])
```



|            | HG01879| HG01879| HG01880| HG01880| HG01882|
|:-----------|-------:|-------:|-------:|-------:|-------:|
|rs58430723  |       0|       0|       0|       0|       0|
|rs12308942  |       1|       0|       0|       0|       0|
|rs11062919  |       0|       0|       0|       0|       0|
|rs28783500  |       1|       0|       0|       0|       0|
|rs190509989 |       0|       0|       0|       0|       0|
|rs7303777   |       0|       0|       0|       0|       0|

## Contributing

Please [submit an issue][issues] to report bugs or ask questions.

Please contribute bug fixes or new features with a [pull request][pull] to this
repository.

[issues]: https://github.com/slowkow/proxysnps/issues
[pull]: https://help.github.com/articles/using-pull-requests/

## Related work

[HaploReg][haploreg]

> HaploReg is a tool for exploring annotations of the noncoding genome at
> variants on haplotype blocks, such as candidate regulatory SNPs at
> disease-associated loci. Using [LD] information from the [1000 Genomes
> Project][1000genomes], linked SNPs and small indels can be visualized along
> with chromatin state and protein binding annotation from the [Roadmap
> Epigenomics][roadmap] and [ENCODE] projects, sequence conservation across
> mammals, the effect of SNPs on regulatory motifs, and the effect of SNPs on
> expression from [eQTL] studies.

[LocusZoom][locuszoom]

> LocusZoom is a tool to plot regional association results from genome-wide
> association scans or candidate gene studies.

[SNAP][snap] (defunct)

> SNAP is a computer program and web-based service for the rapid retrieval of
> linkage disequilibrium proxy SNP results given input of one or more query
> SNPs and based on empirical observations from the [International HapMap
> Project][hapmap] and the [1000 Genomes Project][1000genomes].

[Tagger][tagger]

> Tagger is a tool for the selection and evaluation of tag SNPs from genotype
> data such as that from the [International HapMap Project][hapmap]. It
> combines the simplicity of pairwise tagging methods with the efficiency
> benefits of multimarker haplotype approaches.

[LD]: https://en.wikipedia.org/wiki/Linkage_disequilibrium
[SNP]: https://en.wikipedia.org/wiki/Single-nucleotide_polymorphism
[eQTL]: https://en.wikipedia.org/wiki/Expression_quantitative_trait_loci

[hapmap]: http://www.hapmap.org/
[1000genomes]: http://www.1000genomes.org/
[GRCh37]: http://www.1000genomes.org/faq/which-reference-assembly-do-you-use
[roadmap]: http://www.roadmapepigenomics.org/
[ENCODE]: https://www.encodeproject.org/

[haploreg]: http://www.broadinstitute.org/mammals/haploreg/
[locuszoom]: http://locuszoom.sph.umich.edu/locuszoom/
[snap]: http://www.broadinstitute.org/mpg/snap/
[tagger]: https://www.broadinstitute.org/mpg/tagger/
