# 2024_Sasso_PNAS
This package was created on 2025-01-07 and was processed using the following versions:
 - nf-core/eager version:  2.5.1
 - Minotaur config version: 0.4.0dev
 - CaptureType profile: 1240K
 - CaptureType config version: 0.2.2dev
 - Config template version: 0.3.0dev
 - Package config version: 0.3.0dev
 - Minotaur-packager version: 0.4.3dev
 - populate_janno.py version: 0.5.0dev

## Mirror Group_Name and Genetic_Sex from janno to fam
```bash
package_name=2024_Sasso_PNAS
paste -d "\t" ${package_name}/${package_name}.fam <(cut -f 1-3 ${package_name}/${package_name}.janno |tail -n +2) | \
  awk '
  BEGIN{
    OFS=IFS="\t"
  }
  {
    split($9,groups,";");
    $1=groups[1]
    if ($8 == "M") {
      $5=1
    } else if ($8 == "F") {
      $5=2
    }
    print $1,$2,$3,$4,$5,$6
  }
  ' > tmp.fam
## Cannot overwrite in the same command that reads in the file, so an extra mv is needed.
mv tmp.fam ${package_name}/${package_name}.fam

## trident version: 1.5.4.0
trident rectify --packageVersion Patch --logText "Add janno info collected by @Kavlahkaff. Mirror info to fam file."  --checksumAll -d ${package_name}
```
