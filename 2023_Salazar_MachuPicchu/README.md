# 2023_Salazar_MachuPicchu
This package was created on 2024-09-06 and was processed using the following versions:
 - nf-core/eager version:  2.5.1
 - Minotaur config version: 0.2.1dev
 - CaptureType profile: 1240K
 - CaptureType config version: 0.2.2dev
 - Config template version: 0.3.0dev
 - Package config version: 0.3.1
 - Minotaur-packager version: 0.4.2dev
 - populate_janno.py version: 0.3.3dev

## Mirror Group_Name and Genetic_Sex from janno to fam
```bash
paste -d "\t" 2023_Salazar_MachuPicchu/2023_Salazar_MachuPicchu.fam <(cut -f 1-3 2023_Salazar_MachuPicchu/2023_Salazar_MachuPicchu.janno |tail -n +2) | \
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
mv tmp.fam 2023_Salazar_MachuPicchu/2023_Salazar_MachuPicchu.fam

## trident version: 1.5.4.0
trident rectify --packageVersion Patch --logText "Add janno info collected by @Kavlahkaff. Mirror info to fam file."  --checksumAll -d 2023_Salazar_MachuPicchu
```
