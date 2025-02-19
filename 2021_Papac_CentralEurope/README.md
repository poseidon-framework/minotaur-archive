# 2021_Papac_CentralEurope
This package was created on 2025-02-19 and was processed using the following versions:
 - nf-core/eager version:  2.5.1
 - Minotaur config version: 0.4.0dev
 - CaptureType profile: 1240K
 - CaptureType config version: 0.2.2dev
 - Config template version: 0.3.0dev
 - Package config version: 0.3.0dev
 - Minotaur-packager version: 0.4.3dev
 - populate_janno.py version: 0.5.0dev

## Fill relevant columns from the community archive package.

```bash
package_name="2021_Papac_CentralEurope"
fill_from_archive="community-archive"
fill_from_package="2021_Papac_CentralEurope-1.2.0"

## First fill-in missing metadata from relevant columns (no processing-based info).
trident jannocoalesce \
  -s ../../archives/${fill_from_archive}/${fill_from_package}/${package_name}.janno \
  -t ${package_name}/${package_name}.janno \
  --stripIdRegex "(_ss_MNT$)|(_MNT$)|(.SG)" \
  --includeColumns Alternative_IDs,Relation_To,Relation_Degree,Relation_Type,Relation_Note,Collection_ID,Country,Country_ISO,Location,Site,Latitude,Longitude,Date_Type,Date_C14_Labnr,Date_C14_Uncal_BP,Date_C14_Uncal_BP_Err,Date_BC_AD_Start,Date_BC_AD_Median,Date_BC_AD_Stop,Date_Note,MT_Haplogroup,Y_Haplogroup,Source_Tissue,Primary_Contact,Note,Keywords

## Then fill in Group_Name and Genetic_Sex
trident jannocoalesce \
  -s ../../archives/${fill_from_archive}/${fill_from_package}/${package_name}.janno \
  -t ${package_name}/${package_name}.janno \
  --stripIdRegex "(_ss_MNT$)|(_MNT$)|(.SG)" \
  --includeColumns Genetic_Sex,Group_Name \
  --force

## Mirror Sex and Group name info to the fam file.
paste -d "\t" ${package_name}/${package_name}.fam <(cut -f 1-3 ${package_name}/${package_name}.janno |tail -n +2) | \
  awk '
  BEGIN{
    OFS=IFS="\t"
  }
  {
    split($9,group_name,";")
    $1=group_name[1]
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

  ## trident version: 1.6.2.1
  trident rectify --packageVersion Patch --logText "Fill-in metadata from ${fill_from_archive}: ${fill_from_package}" --checksumAll -d ${package_name}
```
