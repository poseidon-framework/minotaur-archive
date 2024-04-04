# 2021_CarlhoffNature

This package was created on 2024-04-03 and was processed using the following versions:

- nf-core/eager version: 2.4.6
- Minotaur config version: 0.2.1dev
- CaptureType profile: 1240K
- CaptureType config version: 0.2.2dev
- Config template version: 0.3.0dev
- Package config version: 0.3.0dev
- Minotaur-packager version: 0.3.0dev
- populate_janno.py version: 0.3.2dev

## Fill relevant columns from the community archive package.

```bash
## First fill-in missing metadata from relevant columns (no processing-based info).
trident jannocoalesce \
  -s ../../archives/community-archive/2021_CarlhoffNature-2.2.0/2021_CarlhoffNature.janno \
  -t 2021_CarlhoffNature/2021_CarlhoffNature.janno \
  --stripIdRegex "(_ss_MNT$)|(_MNT$)" \
  --includeColumns Alternative_IDs,Relation_To,Relation_Degree,Relation_Type,Relation_Note,Collection_ID,Country,Country_ISO,Location,Site,Latitude,Longitude,Date_Type,Date_C14_Labnr,Date_C14_Uncal_BP,Date_C14_Uncal_BP_Err,Date_BC_AD_Start,Date_BC_AD_Median,Date_BC_AD_Stop,Date_Note,MT_Haplogroup,Y_Haplogroup,Source_Tissue,Primary_Contact,Note,Keywords

## Then fill in Group_Name and Genetic_Sex
trident jannocoalesce \
  -s ../../archives/community-archive/2021_CarlhoffNature-2.2.0/2021_CarlhoffNature.janno \
  -t 2021_CarlhoffNature/2021_CarlhoffNature.janno \
  --stripIdRegex "(_ss_MNT$)|(_MNT$)" \
  --includeColumns Genetic_Sex,Group_Name \
  --force

## Mirror Sex and Group name info to the fam file.
paste -d "\t" 2021_CarlhoffNature/2021_CarlhoffNature.fam <(cut -f 1-3 2021_CarlhoffNature/2021_CarlhoffNature.janno |tail -n +2) | \
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
  mv tmp.fam 2021_CarlhoffNature/2021_CarlhoffNature.fam

  ## trident version: 1.4.1.0
  trident rectify --packageVersion Patch --logText "Fill-in metadata from community-archive: 2021_CarlhoffNature-2.2.0" --checksumAll -d .
```
