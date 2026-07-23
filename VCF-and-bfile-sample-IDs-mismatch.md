## FAQ: VCF and bfile sample IDs do not match

### Problem

This may occur when sample IDs were anonymised during data upload to an imputation server. Then, the IDs in the VCF files do not match the IIDs in the PLINK `.fam` file, and PLINK may report that no samples remain after `--keep` in 01g.

### Solution

Create a two-column mapping file without a header:

```text
OLD_ID_1 NEW_ID_1
OLD_ID_2 NEW_ID_2
```

Then update the VCF IDs with PLINK 2:

```bash
for chr in {1..22}; do
    plink2 \
        --vcf "chr${chr}.vcf.gz" \
        --update-ids id_map.txt \
        --export vcf bgz id-paste=iid \
        --out "chr${chr}_ids_updated"
done
```

The `id-paste=iid` option ensures that only the IID is written to the VCF header, rather than a combined `FID_IID` value. This is because a VCF file can only store each ID in one column.

Check the updated IDs before rerunning the pipeline:

```bash
zgrep -m1 '^#CHROM' chr1_ids_updated.vcf.gz | less -S
```
The line should contain the IIDs your bfiles have:
```
#CHROM POS ID REF ALT QUAL FILTER INFO FORMAT SAMPLE_1 SAMPLE_2 ...
```
PLINK 2 documentation:

* [[Updating sample IDs](https://www.cog-genomics.org/plink/2.0/data#update_indiv)](https://www.cog-genomics.org/plink/2.0/data#update_indiv)