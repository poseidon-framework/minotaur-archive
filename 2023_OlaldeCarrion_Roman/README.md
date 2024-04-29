# 2023_OlaldeCarrion_Roman

This package was created on 2024-04-03 and was processed using the following versions:

- nf-core/eager version: 2.4.6
- Minotaur config version: 0.2.1dev
- CaptureType profile: 1240K
- CaptureType config version: 0.2.2dev
- Config template version: 0.3.0dev
- Package config version: 0.3.0dev
- Minotaur-packager version: 0.3.0dev
- populate_janno.py version: 0.3.2dev

The janno information not produced by MINOTAUR was kindly provided by [@danimfernandes](https://github.com/danimfernandes).

The `Genetic_Sex` and `Group` information provided was mirrored to the .fam file using the following awk script:

```bash
paste -d "\t" 2023_OlaldeCarrion_Roman/2023_OlaldeCarrion_Roman.fam <(cut -f 1-3 2023_OlaldeCarrion_Roman/2023_OlaldeCarrion_Roman.janno |tail -n +2) | \
  awk '
  BEGIN{
    OFS=IFS="\t"
  }
  {
    $1=$9
    if ($8 == "M") {
      $5=1
    } else if ($8 == "F") {
      $5=2
    }
    print $1,$2,$3,$4,$5,$6
  }
  ' > tmp.fam
## Cannot overwrite in the same command that reads in the file, so an extra mv is needed.
mv tmp.fam 2023_OlaldeCarrion_Roman/2023_OlaldeCarrion_Roman.fam

## trident version: 1.4.1.0
trident rectify --packageVersion Patch --logText "Add provided information to janno (thanks @danimfernandes). Mirror info to fam file."  --checksumAll -d 2023_OlaldeCarrion_Roman
```
